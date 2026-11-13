+++
title = "Handling Gaps in Time-Series Data with PostgreSQL's generate_series() and Ecto"
date = 2026-01-26
draft = false
tags = ['PostgreSQL', 'Ecto', 'Elixir']
canonicalURL = 'https://blog.idopterlabs.com.br/handling-gaps-in-time-series-data-with-postgresqls-generate-series-and-ecto-a68f25edb388'
+++

## Introduction

Consider a monitoring system for a weather station. A sensor in the field collects temperature readings every hour and reports them back to the system, but occasionally goes offline due to power outages or maintenance. When generating daily reports, the data shows gaps.

Querying the readings table for a particular day returns only the hours when the sensor was operational. Asking users to figure out which hours are missing feels like poor UX.

In this article we'll learn how to leverage PostgreSQL's `generate_series()` function to tackle this problem and how to use it in a Phoenix application with Ecto.

## The generate_series() function

PostgreSQL's `generate_series()` function produces a set of values within a specified range. Here's the signature of this function:

```
generate_series(
    start_value,
    stop_value,
    step_value
)
```

And here's how we will use it:

```sql
SELECT generate_series(
    '2024-12-21 00:00:00'::timestamptz, -- first hour of the day
    '2024-12-21 23:00:00'::timestamptz, -- last hour of the day
    '1 hour'::interval -- step through each of the 24 hours of the day
) AS hour; -- alias for easier reference
```

This command returns 24 rows. One row for each hour of December 21, 2024 (the date will be provided as input by the user):

```
        hour
------------------------
 2024-12-21 00:00:00+00
 2024-12-21 01:00:00+00
 2024-12-21 02:00:00+00
 ...
 2024-12-21 23:00:00+00
```

## Data with Gaps

The `temperature_readings` table is used for storing hourly readings, with columns `reading_time` and `temp_celsius`. On a normal day, we'd have 24 records. However, if the sensor was offline between 02:30 and 04:30, for example, then we'd only have 19 records. Here's a simple SQL query that returns all readings for 2024-12-21:

```sql
SELECT
  reading_time, temp_celsius
FROM
  temperature_readings
WHERE
  reading_time::date = '2024-12-21'
ORDER BY
  reading_time;
```

Result (simplified):

```
    reading_time      | temp_celsius
-----------------------+--------------
 2024-12-22 00:00:00   | -3.9
 2024-12-22 01:00:00   | -4.2
 2024-12-22 02:00:00   | -4.5
 -- 03:00 and 04:00 missing! 🤨
 2024-12-22 05:00:00   | -4.5
 2024-12-22 06:00:00   | -4.5
```

## Combining generate_series() with a LEFT JOIN

By combining `generate_series()` with a `LEFT JOIN` we can ensure **ALL** hours appear in the result:

```sql
SELECT
    hours.hour::timestamptz AS reading_time,
    t.temp_celsius
FROM
    generate_series(
        '2024-12-21 00:00:00'::timestamptz, -- first hour of the day
        '2024-12-21 23:00:00'::timestamptz, -- last hour of the day
        '1 hour'::interval
    ) AS hours(hour)
    LEFT JOIN temperature_readings t
      ON t.reading_time = hours.hour::timestamptz
ORDER BY hours.hour;
```

The `LEFT JOIN` ensures that all rows from the "left" table (`hours` table) are included in the result even if a matching record on the joining table (`temperature_readings` table) does not exist. In other words, we need a row for every hour of the day (left table) even if a reading for that hour (right table) is missing.

The query gives us a result that includes all 24 hours, with `NULL` values where there's no data:

```
    reading_time      | temp_celsius
-----------------------+--------------
 2024-12-21 00:00:00   | -3.9
 2024-12-21 01:00:00   | -4.2
 2024-12-21 02:00:00   | -4.5
 2024-12-21 03:00:00   | NULL
 2024-12-21 04:00:00   | NULL
 2024-12-21 05:00:00   | -4.5
 2024-12-21 06:00:00   | -4.5
```

This is a much easier result set for us to work with from our application. We will take it up a notch by including a `status` column based on the value of the `temp_celsius` column:

```sql
SELECT
    hours.hour::timestamptz AS reading_time,
    t.temp_celsius,
    CASE WHEN t.temp_celsius IS NULL THEN 'MISSING' ELSE 'OK' END AS status
FROM
    generate_series(
        '2024-12-21 00:00:00'::timestamptz,
        '2024-12-21 23:00:00'::timestamptz,
        '1 hour'::interval
    ) AS hours(hour)
    LEFT JOIN temperature_readings t ON t.reading_time = hours.hour::timestamptz
ORDER BY hours.hour;
```

And the result now includes a third column that we can directly reference from our application:

```
    reading_time      | temp_celsius | status
-----------------------+--------------+---------
 2024-12-21 00:00:00   | -3.9         | OK
 2024-12-21 01:00:00   | -4.2         | OK
 2024-12-21 02:00:00   | -4.5         | OK
 2024-12-21 03:00:00   | NULL         | MISSING
 2024-12-21 04:00:00   | NULL         | MISSING
 2024-12-21 05:00:00   | -4.5         | OK
 2024-12-21 06:00:00   | -4.5         | OK
```

## Building the Query in Ecto

Now let's implement this logic in Elixir using Ecto. The key is the `fragment/1` function, which allows us to embed raw SQL within Ecto queries.

First, we define our schema for temperature readings:

```elixir
defmodule Reports.TemperatureReading do
  use Ecto.Schema

  @primary_key {:id, :id, autogenerate: true}

  schema "temperature_readings" do
    field :reading_time, :utc_datetime
    field :temp_celsius, :decimal
  end
end
```

And here's the complete implementation of the module that fetches hourly readings, where the `get_hourly_readings/1` function implements the Ecto version of the aforementioned SQL query:

```elixir
defmodule Reports.Temperatures do
  import Ecto.Query
  alias Reports.Repo
  alias Reports.TemperatureReading

  def get_hourly_readings(date) do
    start_time = get_start_time(date)
    end_time = get_end_time(date)

    query =
      from(
        all_times in fragment(
          "generate_series(?::timestamptz, ?::timestamptz, '1 hour'::interval)",
          ^start_time,
          ^end_time
        ),
        left_join: tr in TemperatureReading,
        on: all_times == tr.reading_time,
        select: %{
          reading_time: fragment("?::timestamptz", all_times),
          temp_celsius: tr.temp_celsius,
          status: fragment(
            "CASE WHEN ? IS NULL THEN 'MISSING' ELSE 'OK' END",
            tr.temp_celsius
          )
        },
        order_by: [asc: all_times]
      )

    Repo.all(query)
  end

  # get_start_time/1 and get_end_time/1 omitted for brevity
end
```

Breaking this down:

1. `fragment`: We use `fragment/1` to call the PostgreSQL `generate_series()` function directly. The `?` placeholders are replaced by the `^start_time` and `^end_time` parameters and `::timestamptz` casts the values to timestamps with timezone set to UTC.

2. `left_join`: The LEFT JOIN ensures that all generated hours appear in the result, even those without a match in the `temperature_readings` table.

3. The `on` condition: We directly compare the generated value (`all_times`) with the `reading_time` field from the schema.

4. `select` with `fragment`: We select the columns we want to read from the result and use `fragment` again for the `CASE WHEN`, which calculates each row's status.

⚠️ **Consistent timezone handling is critical. Unless you have a strong reason NOT to, you should ALWAYS use UTC throughout your application to avoid daylight saving time complications.**

## Rendering the Reports

The big advantage of this approach is simplicity in the presentation layer. Since the query returns all 24 hours with an explicit status field, the template can iterate directly without complex logic. Here's what the controller looks like:

```elixir
defmodule ReportsWeb.ReportController do
  use ReportsWeb, :controller
  alias Reports.Temperatures

  def show(conn, %{"date" => date}) do
    date = sanitize_date(date)
    readings = Temperatures.get_hourly_readings(date)

    render(conn, :show, readings: readings, date: date)
  end
end
```

## The Template

In the HEEx template, iteration is straightforward:

```html
<table>
  <tbody>
    <%= for reading <- @readings do %>
      <tr class={reading_class(reading.status)}>
        <td>{Calendar.strftime(reading.reading_time, "%H:%M")}</td>
        <td>{display_temperature(reading.temp_celsius)}</td>
        <td>
          <span class={status_class(reading.status)}>
            {reading.status}
          </span>
        </td>
      </tr>
    <% end %>
  </tbody>
</table>
```

With this technique, our report now displays all 24 hours with clear visual indicators for missing data.

This approach brings several benefits to the HEEx template. A single loop traverses all 24 hours without conditional checks for missing data. The explicit status field enables straightforward styling decisions in the `reading_class` and `status_class` helper functions. It makes for a much simpler HEEx template with the cost of a slightly more sophisticated query. Low cost for a big benefit!

## Conclusion

The combination of `generate_series()` with `LEFT JOIN` is a powerful technique for working with time-series data that may have gaps. By moving this logic to the database, we can significantly simplify the application code.

Try implementing this approach in your next PostgreSQL backed Phoenix project and see how it simplifies report generation!

---

*Originally published on [Idopter Labs](https://blog.idopterlabs.com.br/handling-gaps-in-time-series-data-with-postgresqls-generate-series-and-ecto-a68f25edb388).*
