# Routes and SQL Commands Used
### Customer
* View My flights
  * SELECT * FROM ticket NATURAL JOIN flight WHERE customer_email=%s
    * Natural joins the ticket and flight tables and returns only rows that match the customer email currently logged in so the customer can see their flights
* Search for flights
  * SELECT * FROM flight
    * Select every row from the flight table and return so the customer can see all the available flights
  * SELECT * FROM airport
    * Select and return every row so the customer knows what airports are available to fly to
  * SELECT * FROM flight WHERE departure_airport=%s AND arrival_airport=%s AND departure_date_and_time=%s AND arrival_date_and_time=%s
    * Select a specific flight based on the customer input so customer's can filter their results rather than view every flight
* Purchase tickets 
  * INSERT INTO ticket(customer_email, airline_name, flight_number, purchase_date_and_time) VALUES(%s, %s, %s, %s)
    * Inserts the ticket values into the table when a customer buys a ticket
  * INSERT INTO purchase VALUES(%s,%s,%s,%s,%s,%s,%s,%s)
    * Inserts the purchase information (card number, expiration, ...) into the database and associate it with a ticket
* Cancel Trip
  * SELECT * FROM ticket NATURAL JOIN flight WHERE customer_email=%s
    * Grab all the tickets and its associated flight information that belongs to the logged in customer
  * DELETE FROM purchase WHERE customer_email=%s AND purchase_date_and_time=%s AND ticket_id=%s
    * Delete from the purchase table the purchase information when a customer cancels their trip
  * DELETE FROM ticket WHERE ID=%s
    * Delete from the ticket table the ticket that was canceled
* Give Ratings and Comment on previous flights
  * SELECT * FROM ticket NATURAL JOIN flight WHERE customer_email=%s
    * Grab all the tickets and its associated flight information that belongs to the logged in customer
  * INSERT INTO rate VALUES(%s, %s, %s, %s)
    * Insert into the rate table a customer rating for a flight
* Track My Spending
  * SELECT ticket_id, purchase_date_and_time, sold_price, SUM(sold_price) AS total FROM purchase WHERE customer_email=%s
    * Select the tickets that belong to a customer and then calculate the sum of the sold_price attribute to get the customer's total spending
* Logout
  * No SQL was used for this
### Airline Staff
* View Flights
  * SELECT * FROM airline_staff WHERE username=%s
    * Find the airline the airline staff belongs to for next query
  * SELECT * FROM flight WHERE airline=%s
    * Return all flights that belong to the airline staff's airline so it can be viewed by the logged in staff
* Create New Flights
  * SELECT * FROM flight WHERE flight_number=%s AND departure_date_and_time=%s
    * Find a flight with the given ID and departure times, used to prevent user from inputting duplicate or conflicting flights
  * INSERT INTO flight VALUES(%s,%s,%s,%s,%s,%s,%s,%s,%s)
    * Insert into the flight table the new flight that is created
* Change Status of Flights
  * UPDATE flight SET flight_status=%s WHERE flight_number=%s
    * Update the flight_status attribute of a flight from delay to on-time or reverse so customers know what flights are currently delayed
* Add airplane in the system
  * SELECT airline FROM airline_staff WHERE username=%s
    * Find the airline the airline staff belongs to for next query
  * SELECT * FROM airplane WHERE airline=%s
    * Find all airplanes in the database to confirm the plane is added
  * INSERT INTO airplane VALUES(%s, %s, %s, %s, %s)
    * Insert into the airplane table the new airplane made by staff
* Add new airport in the system
  * SELECT * FROM airport WHERE name=%s
    * Check if airport already exists to prevent duplicate or conflicting flights
  * INSERT INTO airport VALUES(%s, %s, %s, %s)
    * Insert into the airport table the newly created airport
* View flight ratings
  * SELECT * FROM airline_staff WHERE username=%s
    * Find the airline the airline staff belongs to for next query
  * SELECT * FROM flight WHERE airline=%s
    * Return all flights that belong to the airline staff's airline so it can be viewed by the logged in staff
  * SELECT * FROM rate WHERE flight_number=%s
    * Get from the rate table all the rows that belong to a certain flight
* View frequent customers
  * SELECT customer_email FROM ticket WHERE purchase_date_and_time >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR) GROUP BY customer_email HAVING COUNT(*) IN (SELECT MAX(customers) FROM (SELECT COUNT(\*) AS customers FROM ticket GROUP BY customer_email) AS result)
    * Grab the customer email that appears the most in the past year in the ticket table to find the most frequent customer based on ticket sales in the past year
  * SELECT airline FROM airline_staff WHERE username=%s
    * Find the airline the airline staff belongs to for next query
  * SELECT DISTINCT customer_email FROM ticket WHERE airline_name=%s
    * Get all unique customer emails so airline staff can select a customer and view their purchases
* View reports
  * SELECT airline FROM airline_staff WHERE username=%s
    * Find the airline the airline staff belongs to for next query
  * SELECT * FROM ticket WHERE airline_name=%s
    * Get the tickets that belong to an airline so clientside can use the data to form a table of sales
* View Earned Revenue
  * SELECT airline FROM airline_staff WHERE username=%s
    * Find the airline the airline staff belongs to for next query
  * SELECT SUM(sold_price) as revenue FROM `purchase` INNER JOIN ticket ON ticket.ID=purchase.ticket_id WHERE airline_name=%s AND purchase.purchase_date_and_time >= DATE_SUB(CURDATE(), INTERVAL 1 MONTH)
    * Get the total number of money earned in the past month
  * SELECT SUM(sold_price) as revenue FROM `purchase` INNER JOIN ticket ON ticket.ID=purchase.ticket_id WHERE airline_name=%s AND purchase.purchase_date_and_time >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
    * Get the total number of money earned in the past year
* Logout
  * No SQL was used for this



