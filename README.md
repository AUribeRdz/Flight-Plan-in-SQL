# Flight Plan in SQL: Multi-Stop Routes and Cost

Builds a complete routing table from a table of direct flights, including non-stop and multi-stop routes to every reachable destination, with total cost, using a recursive common table expression (CTE).

## The problem

Given the direct flights below, produce a final table of all possible routes departing from **Vienna** with **no more than 5 stops**, showing each destination, the route taken and its total cost.

| Departure | Arrival | FlightNumber | Cost | FlightTime |
| --- | --- | --- | --- | --- |
| London | Frankfurt | LH20903 | 759.44 | 2 |
| London | San Francisco | EA87334 | 159.30 | 10 |
| London | New York | LH19681 | 46.21 | 8 |
| London | Paris | LH19618 | 59.21 | 1.5 |
| Frankfurt | Vienna | AU9134 | 569.92 | 3 |
| Frankfurt | New York | LH12375 | 546.10 | 9 |
| Frankfurt | Paris | EH54200 | 848.58 | 2 |
| San Francisco | New York | LH71803 | 379.27 | 4 |
| San Francisco | Vienna | EA10922 | 105.60 | 11 |
| San Francisco | Frankfurt | EH29963 | 29.48 | 10 |
| New York | Paris | AU45243 | 853.72 | 8 |
| New York | Vienna | EA8302 | 178.95 | 7 |
| New York | Frankfurt | AU36738 | 799.23 | 9.5 |
| Paris | San Francisco | AU53720 | 941.36 | 8.5 |
| Paris | Vienna | LH89281 | 873.52 | 3 |
| Paris | Frankfurt | EH52253 | 459.41 | 2 |
| Vienna | New York | AU84861 | 482.42 | 2.4 |
| Vienna | Paris | EA37910 | 74.88 | 3 |
| Vienna | Chicago | EH55853 | 391.23 | 8 |

## Approach

Routing is a graph traversal problem: expand paths hop by hop, stop at a limit, avoid cycles, and aggregate a cost along each path.

- **Anchor member:** every flight departing Vienna, with `stops = 0` and the leg cost as the starting total.
- **Recursive member:** join each path's last arrival to the next departure, add 1 to `stops`, add the leg cost, and append the city to the route string.
- **Termination:** `stops < 5`.
- **Cycle prevention:** skip a next city that already appears in the route. The route is wrapped in delimiters before the check (`INSTR(' -> ' || route || ' -> ', ' -> ' || city || ' -> ') = 0`), so one city name can never match inside another.
- **Output:** `Departure`, `Arrival`, `route`, `stops`, `totalCost`, stored in a `flight_plan` table and read back ordered by `totalCost`.
- **Cheapest per destination:** a second query ranks the routes with `ROW_NUMBER() OVER (PARTITION BY Arrival ORDER BY totalCost)` and keeps rank 1.

## Tech stack

| Area | Tools |
| --- | --- |
| SQL | Recursive CTE (`WITH RECURSIVE`), window function, SQLite (in-memory) |
| Python | `sqlite3`, pandas, Jupyter Notebook |

## Results

The recursion returns **17 routes** from Vienna. The cheapest route to each reachable destination:

| Destination | Route | Stops | Total cost |
| --- | --- | --- | --- |
| Paris | Vienna -> Paris | 0 | 74.88 |
| Chicago | Vienna -> Chicago | 0 | 391.23 |
| New York | Vienna -> New York | 0 | 482.42 |
| Frankfurt | Vienna -> Paris -> Frankfurt | 1 | 534.29 |
| San Francisco | Vienna -> Paris -> San Francisco | 1 | 1,016.24 |

London is not reachable from Vienna in this data, so it does not appear.

## Run it

```bash
pip install pandas jupyter
jupyter notebook FPSQL_Pdn_Rel_1.ipynb
```

Run the notebook top to bottom. It builds the table in an in-memory SQLite database, so there is nothing to set up and nothing is written to disk.

## Known limitations

- The route enumeration is exhaustive, so it grows quickly with more cities and hops. Larger graphs need pruning, shortest-path algorithms or a graph database.
- Cost is the only ranking criterion; flight time is stored but not used.
- Written and tested for SQLite. The recursive CTE and window function are standard SQL, but string concatenation and `INSTR` differ between databases.

## Next steps

- Rank routes by flight time or by a combination of cost and time.
- Port the query to PostgreSQL (array paths and the `CYCLE` clause) and to SQL Server.

## Skills demonstrated

Recursive query design, graph reasoning in SQL, cost aggregation, analytical SQL, data engineering fundamentals.
