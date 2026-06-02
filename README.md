# Flight-Plan-in-SQL
# From direct flights table build a Table within stops and non-stops to same final destination & Cost in SQL

# Flight plan in SQL — graph traversal and cost optimization Given a table of direct flights, this project builds a complete routing table that includes both non-stop and multi-stop options to the same destination, ranked by total cost. 

## The problem Real-world routing is a graph traversal problem: find all valid paths between two nodes, expand the search space hop-by-hop, prune paths that exceed constraints, and rank by a cost function. This is the same logical structure as RAG pipeline retrieval — traverse a knowledge graph, rank candidate chunks by relevance score, return the optimal path to an answer. 

## Implementation - Recursive CTE to generate multi-hop paths up to N stops - Cost aggregation across legs with null-safe handling for missing routes - Final output: unified table of direct + indirect routes with total cost 

## Tech stack SQL (recursive CTEs) · Python · Jupyter Notebook · Pandas ## Skills demonstrated Data engineering · Recursive query design · Graph reasoning · Cost optimization · Analytical SQL


Using an initial Flights table as follow:

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

SOLUTION NOTE:
"Flights" Table construction as part of Solution and Solution itself are in attached File.
