---
title: Queries
created: '2022-10-09T15:29:57.675Z'
modified: '2022-11-02T11:45:33.120Z'
---

# Queries

## Passengers and revenue by airline and month
WITH
SET [~COLUMNS] AS
    {[Time Departure.Time Departure Hierarchy].[Month].Members}
SET [~ROWS] AS
    {[Airline.Airline Hierarchy].[Airline Name].Members}
SELECT
NON EMPTY CrossJoin([~COLUMNS], {[Measures].[Passengers], [Measures].[Revenue]}) ON COLUMNS,
NON EMPTY [~ROWS] ON ROWS
FROM [Flights]

## Plane revenue when daily revenue above 65k
SELECT Airplane.[Airplane Type].Members ON COLUMNS,
       FILTER(DESCENDANTS([Time Departure].Year.Members, [Time Departure].Day, SELF_AND_BEFORE), Measures.Revenue > 65000) ON ROWS
FROM Flights
WHERE Measures.Revenue

## Top 10 most expensive flights info
WITH MEMBER Measures.[Average Price] AS (Measures.Revenue / Measures.Passengers)

SELECT {Measures.[Average Price], Measures.Passengers} ON COLUMNS,
TOPCOUNT(
    CROSSJOIN(CROSSJOIN(Airline.[Airline Name].Members, [Airport Origin].Country.Members), [Airport Destination].Country.Members),
    10, Measures.[Average Price]) ON ROWS
FROM Flights
