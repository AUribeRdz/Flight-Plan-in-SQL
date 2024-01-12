# Flight-Plan-in-SQL
# From direct flights table build a Table within stops and non-stops to same final destination & Cost in SQL


Using an initial flights table as follow:

flightPlan table:

Departure	Arrival	      	FlightNumber	Cost	FlightTime

London	      	Frankfurt	LH20903	      	759.44	2
London	      	San Francisco	EA87334	      	159.3	10
London	      	New York	LH19681	      	46.21	8
London	      	Paris	        LH19618	      	59.21	1.5
Frankfurt	Vienna	      	AU9134	      	569.92	3
Frankfurt	New York	LH12375	     	546.1	9
Frankfurt	Paris	        EH54200	      	848.58	2
San Francisco	New York	LH71803	      	379.27	4
San Francisco	Vienna	      	EA10922	      	105.6	11
San Francisco	Frankfurt	EH29963	      	29.48	10
New York	Paris	        AU45243	      	853.72	8
New York	Vienna	      	EA8302	      	178.95	7
New York	Franfurt	AU36738	      	799.23	9.5
Paris	        San Francisco	AU53720	      	941.36	8.5
Paris	        Vienna	      	LH89281	      	873.52	3
Paris	        Frankfurt	EH52253	      	459.41	2
Vienna	      	New York	AU84861	      	482.42	2.4
Vienna	      	Paris	        EA37910	      	74.88	3
Vienna	      	Chicago	      	EH55853	      	391.23	8

Build a SQL code to reflect a final table within all possible routes within no more than 5 stops departing from Vienna and arriving to all possible destination within stops and its cost.

Final table should look as follow:

Arrival		Departure	Route	                                                                      	Stops	TotalCost
Chicago		Vienna	  	Vienna -> Chicago								0	391.23
Chicago		Vienna	  	Vienna -> New York -> Paris -> Frankfurt -> New York -> Vienna -> Chicago	5	2911.83
Chicago		Vienna	  	Vienna -> New York -> Paris -> Frankfurt -> Paris -> Vienna -> Chicago		5	3908.88
etc.

## SQL Code:

-- Creating CTE

WITH flight_route (Departure, Arrival, stops, totalCost, route) AS

(SELECT f.Departure, f.Arrival, 0,

-- Defining the totalCost with the flight cost of the first flight

f.Cost,

-- Defining the route of a flight

CAST(Departure + ' -> ' + Arrival AS NVARCHAR(MAX))

FROM flightPlan f

WHERE Departure = 'Vienna'

UNION ALL

-- Recursive steps/variables

SELECT p.Departure, f.Arrival, p.stops + 1,

-- Adding the cost for each layover to the total costs

p.totalCost + f.cost,

-- Adding the layover airport to the route for each recursion step

p.route + ' -> ' + f.Arrival

FROM flightPlan f, flight_route p
	
 WHERE p.Arrival = f.Departure AND p.stops < 5)




-- FINAL TABLE

SELECT 

    DISTINCT Arrival, Departure, route, stops, totalCost
    
FROM flight_route;
