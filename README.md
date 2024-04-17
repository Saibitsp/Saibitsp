### AIRPORT MANANGEMENT SYSTEM (AMS)

An Airport Management System (AMS) is a comprehensive software solution designed to streamline and optimize the operations and management of airports. It integrates various functionalities and processes to enhance efficiency, safety, and customer experience within airport environments. AMS facilitates flight - management, passenger processing, and baggage handling, ensuring smooth travel experiences. It optimizes resource allocation, coordinates ground handling services, and manages airport security effectively. With real-time monitoring and reporting capabilities, AMS enhances safety, maximizes revenue generation, and improves passenger satisfaction. Experience seamless airport management with AMS, the ultimate solution for modern airports.

### Assumption

1. Flight Information Availability: We assume that comprehensive flight information, including flight numbers, departure times, arrival times, gate assignments, and airline details, is readily available and accurately maintained in the system.
2. Passenger and Ticket Data: We assume that passenger details, ticket information, and baggage data are properly recorded and associated with each flight.
3. Employee Roles and Responsibilities: We assume predefined roles for airport employees, including pilots, flight attendants, ground staff, and gate agents, each with specific responsibilities and duties.

### Some Example Queries

1. Total number of unique passengers who have tickets for flights departing between 10:00 AM and 2:00 PM
```sql
SELECT 
    COUNT(DISTINCT P.Passenger_ID) AS Total_Passengers
FROM 
    Passenger P
JOIN 
    Tickets T ON P.Passenger_ID = T.Passenger_ID
JOIN 
    Flight F ON T.Flight_ID = F.Flight_ID
WHERE 
    F.Departure_time BETWEEN '10:00' AND '14:00';
```
2. Count the number of unique passengers who have flights departing within a specified time range.
  ```sql
 CREATE PROCEDURE CountPassengersBetweenTimes(IN arrival_time VARCHAR(45), IN departure_time VARCHAR(45))
BEGIN
    SELECT 
        COUNT(DISTINCT P.Passenger_ID) AS Total_Passengers
    FROM 
        Passenger P
    JOIN 
        Tickets T ON P.Passenger_ID = T.Passenger_ID
    JOIN 
        Flight F ON T.Flight_ID = F.Flight_ID
    WHERE 
        F.Departure_time BETWEEN arrival_time AND departure_time;
END //
```
3. The average number of passengers per flight for each airline.
```sql
SELECT 
    A.Airline_Name,
    AVG(PassengerCount) AS Avg_Passengers_Per_Flight
FROM 
    Airline A
LEFT JOIN (
    SELECT 
        F.Airline_ID,
        COUNT(DISTINCT T.Passenger_ID) AS PassengerCount
    FROM 
        Flight F
    LEFT JOIN 
        Tickets T ON F.Flight_ID = T.Flight_ID
    GROUP BY 
        F.Airline_ID, F.Flight_ID
) AS PassengerCounts ON A.Airline_ID = PassengerCounts.Airline_ID
GROUP BY 
    A.Airline_Name;
```
4. The number of flights a Pilot (identified by their employee ID) has flown in the current week.
```sql
CREATE PROCEDURE GetFlightsForPilotInWeek(
    IN input_employee_id INT
)
BEGIN
    SELECT 
        FE.Flight_Employee_Name AS Pilot_Name,
        COUNT(F.Flight_ID) AS Flights_Flown_In_Week
    FROM 
        Flight_Employees FE
    INNER JOIN 
        Flight F ON FE.Flight_Employee_ID = F.Flight_Employee_ID
    WHERE 
        FE.Flight_Employee_ID = input_employee_id
        AND FE.Position = 'Pilot'
        AND YEARWEEK(F.Departure_Time) = YEARWEEK(NOW())  -- Adjust the date as needed
    GROUP BY 
        FE.Flight_Employee_Name;
END //
```
5. The average number of baggage items per passenger.
 ```sql
 SELECT AVG(baggage_count) AS Avg_Baggage_Per_Passenger
FROM (
    SELECT p.Passenger_ID, COUNT(b.Baggage_ID) AS baggage_count
    FROM Passenger p
    LEFT JOIN Baggage b ON p.Passenger_ID = b.Passenger_ID
    GROUP BY p.Passenger_ID
) AS baggage_counts 
LIMIT 1;
```
6. Total number of passengers who have baggage associated with them.
```sql
SELECT COUNT(DISTINCT p.Passenger_ID) AS Total_Passengers_With_Baggage
FROM Passenger p
JOIN Baggage b ON p.Passenger_ID = b.Passenger_ID;
```
7. Retrieves the distinct passenger IDs and names for passengers who have more than one piece of baggage associated with them.
```sql
SELECT DISTINCT p.Passenger_ID, p.Passenger_Name
FROM Passenger p
WHERE p.Passenger_ID IN (
    SELECT Passenger_ID
    FROM Baggage
    GROUP BY Passenger_ID
    HAVING COUNT(*) > 1
);
```
8. Retrieves flight details along with the count of booked passengers for flights where the number of booked passengers exceeds three.
```sql
SELECT 
    f.Flight_ID, 
    f.Flight_Number,
    COUNT(t.Passenger_ID) AS Booked_Passengers
FROM 
    Flight f
JOIN 
    Tickets t ON f.Flight_ID = t.Flight_ID
GROUP BY 
    f.Flight_ID, f.Flight_Number
HAVING 
    COUNT(t.Passenger_ID) > 3;
```
9. Retrieves the names of flight employees along with the count of flights they have worked on, listing the top 5 flight employees based on the number of flights worked.
```sql
SELECT fe.Flight_Employee_Name, COUNT(f.Flight_ID) AS Flights_Worked
FROM Flight_Employees fe
JOIN Flight f ON fe.Flight_Employee_ID = f.Flight_Employee_ID
GROUP BY fe.Flight_Employee_Name
ORDER BY Flights_Worked DESC
LIMIT 5;
```
10. Calculates the total number of unique passengers who have tickets for flights departing between 10:00 AM and 2:00 PM.
```sql
SELECT 
    COUNT(DISTINCT P.Passenger_ID) AS Total_Passengers
FROM 
    Passenger P
JOIN 
    Tickets T ON P.Passenger_ID = T.Passenger_ID
JOIN 
    Flight F ON T.Flight_ID = F.Flight_ID
WHERE 
    F.Departure_time BETWEEN '10:00' AND '14:00';
```
11. Total number of unique passengers for each airline.
```sql
SELECT
a.Airline_Name,
COUNT(DISTINCT p.Passenger_ID) AS Total_Passengers
FROM
Passenger p
JOIN
Flight f ON p.Flight_ID = f.Flight_ID
JOIN
Airline a ON f.Airline_ID = a.Airline_ID
GROUP BY
a.Airline_Name;
```
12. Retrieves the gate ID, gate number, and the count of flights assigned to each gate. It then orders the results by the flight count in descending order and limits the output to only the gate with the highest number of flights.
```sql
SELECT 
    g.Gate_ID, 
    g.Gate_Number,
    COUNT(f.Flight_ID) AS Flight_Count
FROM 
    Gate g
JOIN 
    Flight f ON g.Gate_ID = f.Gate_ID
GROUP BY 
    g.Gate_ID
ORDER BY 
    Flight_Count DESC
LIMIT 1;
```
