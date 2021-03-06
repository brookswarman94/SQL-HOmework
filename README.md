# SQL-HOmework
/*1a. Display the first and last names of all actors from the table actor.*/

SELECT 

    actor.first_name, actor.last_name

FROM

    actor;

/*1b. Display the first and last name of each actor in a single column in upper case letters. Name the column Actor Name.*/

ALTER TABLE actor add column ActorName varchar(255);

UPDATE actor 

SET 

    ActorName = CONCAT(first_name, ' ', last_name);

SELECT 

    *

FROM

    actor;



#2a. You need to find the ID number, first name, and last name of an actor, of whom you know only the first name, "Joe." What is one query would you use to obtain this information?

SELECT 

    actor.first_name, actor.last_name, actor.actor_id

FROM

    actor

WHERE

    actor.first_name = 'Joe';



#2b. Find all actors whose last name contain the letters GEN:

SELECT 

    *

FROM

    actor

WHERE

    actor.last_name LIKE '%GEN%';



#2c. Find all actors whose last names contain the letters LI. This time, order the rows by last name and first name, in that order:

SELECT 

    actor.last_name, actor.first_name

FROM

    actor

WHERE

    actor.last_name LIKE '%LI%';



#2d. Using IN, display the country_id and country columns of the following countries: Afghanistan, Bangladesh, and China:



SELECT 

    country_id, country

FROM

    country

WHERE

    country IN ('Afghanistan' , 'Bangladesh', 'China');



#3a. Add a middle_name column to the table actor. Position it between first_name and last_name. Hint: you will need to specify the data type.

alter table actor

add column middle_name varchar (30)

after first_name;



#3b. You realize that some of these actors have tremendously long last names. Change the data type of the middle_name column to blobs.

alter table actor

modify column middle_name blob;



#3c. Now delete the middle_name column.

alter table actor

drop column middle_name;



#4a. List the last names of actors, as well as how many actors have that last name.

SELECT 

    last_name, COUNT(*)

FROM

    actor

GROUP BY last_name;



#4b. List last names of actors and the number of actors who have that last name, but only for names that are shared by at least two actors

SELECT 

    last_name, COUNT(*)

FROM

    actor

GROUP BY last_name

HAVING COUNT(*) > 1;



#4c. Oh, no! The actor HARPO WILLIAMS was accidentally entered in the actor table as GROUCHO WILLIAMS, the name of Harpo's second cousin's husband's yoga teacher. Write a query to fix the record.

UPDATE actor 

SET 

    first_name = 'HARPO',

    last_name = 'WILLIAMS'

WHERE

    first_name = 'GROUCHO'

        AND last_name = 'WILLIAMS';





#4d. Perhaps we were too hasty in changing GROUCHO to HARPO. It turns out that GROUCHO was the correct name after all! In a single query, if the first name of the actor is currently HARPO, change it to GROUCHO. Otherwise, change the first name to MUCHO GROUCHO, as that is exactly what the actor will be with the grievous error. BE CAREFUL NOT TO CHANGE THE FIRST NAME OF EVERY ACTOR TO MUCHO GROUCHO, HOWEVER! (Hint: update the record using a unique identifier.)



UPDATE actor 

SET 

    first_name = CASE

        WHEN first_name = 'Harpo' THEN 'GROUCHO'

        WHEN first_name = 'Groucho' THEN 'MUCHO GROUCHO'

        ELSE first_name

    END;





/*5a. You cannot locate the schema of the address table. Which query would you use to re-create it?*/

show create table address;



/*6a. Use JOIN to display the first and last names, as well as the address, of each staff member. Use the tables staff and address:*/



SELECT 

    staff.address_id,

    staff.first_name,

    staff.last_name,

    address.address

FROM

    staff

        JOIN

    address ON staff.address_id = address.address_id;



/*6b. Use JOIN to display the total amount rung up by each staff member in August of 2005. Use tables staff and payment.*/



SELECT 

    staff.staff_id,

    staff.first_name,

    staff.last_name,

    SUM(payment.amount) AS 'Total Charged'

FROM

    staff

        JOIN

    payment ON staff.staff_id = payment.staff_id

WHERE

    payment.payment_date BETWEEN '2005-08-01' AND '2005-08-31'

GROUP BY staff.staff_id;



/*6c. List each film and the number of actors who are listed for that film. Use tables film_actor and film. Use inner join.*/



SELECT 

    film.title, COUNT(film.film_id) AS '# of Actors'

FROM

    film

        INNER JOIN

    film_actor ON film.film_id = film_actor.film_id

GROUP BY film.film_id;



/*6d. How many copies of the film Hunchback Impossible exist in the inventory system?*/

SELECT 

    film.title,

    film.film_id,

    COUNT(inventory.inventory_id) AS '# of Copies'

FROM

    film

        JOIN

    inventory

WHERE

    film.film_id = inventory.film_id

        AND film.title = 'Hunchback Impossible';



/*6e. Using the tables payment and customer and the JOIN command, list the total paid by each customer. List the customers alphabetically by last name:*/

SELECT 

    customer.customer_id,

    customer.last_name,

    customer.first_name,

    SUM(payment.amount) AS 'Total Payments'

FROM

    customer

        JOIN

    payment ON customer.customer_id = payment.customer_id

GROUP BY customer.customer_id

ORDER BY customer.last_name;



/*7a. The music of Queen and Kris Kristofferson have seen an unlikely resurgence. As an unintended consequence, films starting with the letters  K and Q have also soared in popularity. Use subqueries to display the titles of movies starting with the letters K and Q whose language is English.*/



SELECT 

    title

FROM

    film

WHERE

    title LIKE 'K%'

        OR title LIKE 'Q%'

        AND language_id IN (SELECT 

            language_id

        FROM

            language

        WHERE

            name = 'English');



/*7b. Use subqueries to display all actors who appear in the film Alone Trip.*/

select first_name, last_name from actor

where actor_id in

(select actor_id from film_actor where film_id in (select film_id from film where title = "Alone Trip"));



/*7c. You want to run an email marketing campaign in Canada, for which you will need the names and email addresses of all Canadian customers. Use joins to retrieve this information.*/

SELECT 

    first_name, last_name, email

FROM

    customer

        JOIN

    address ON customer.address_id = address.address_id

        JOIN

    city ON address.city_id = city.city_id

        JOIN

    country ON country.country_id = city.country_id

        AND country.country = 'Canada'; 



/*7d. Sales have been lagging among young families, and you wish to target all family movies for a promotion. Identify all movies categorized as famiy films.*/

SELECT 

    film.film_id, film.title, category.name

FROM

    film

        JOIN

    film_category ON film.film_id = film_category.film_id

        JOIN

    category ON film_category.category_id = category.category_id

        AND category.name = 'Family';



/*7e. Display the most frequently rented movies in descending order.*/



SELECT 

    film.film_id,

    film.title,

    COUNT(rental.inventory_id) AS '# of Rentals'

FROM

    rental

        JOIN

    inventory ON rental.inventory_id = inventory.inventory_id

        JOIN

    film ON inventory.film_id = film.film_id

GROUP BY film.film_id , film.title

ORDER BY COUNT(rental.inventory_id) DESC; 



/*7f. Write a query to display how much business, in dollars, each store brought in.*/



SELECT 

    store.store_id,

    address.address,

    SUM(payment.amount) AS 'Sum'

FROM

    store

        JOIN

    customer ON store.store_id = customer.store_id

        JOIN

    payment ON customer.customer_id = payment.customer_id

        JOIN

    address ON store.address_id = address.address_id

GROUP BY store.store_id , address.address

ORDER BY store.store_id; 



/*7g. Write a query to display for each store its store ID, city, and country.*/

SELECT 

    store.store_id, city.city, country.country

FROM

    store

        JOIN

    address ON store.address_id = address.address_id

        JOIN

    city ON address.city_id = city.city_id

        JOIN

    country ON city.country_id = country.country_id; 



/*7h. List the top five genres in gross revenue in descending order. (Hint: you may need to use the following tables: category, film_category, inventory, payment, and rental.)*/

select name, sum(payment.amount) as 'Gross Revenue'

from category join film_category on category.category_id = film_category.category_id join inventory on film_category.film_id = inventory.film_id join rental on inventory.inventory_id = rental.inventory_id join payment on rental.rental_id = payment.rental_id group by name order by 'Gross Revenue' desc limit 5; 



/*8a. In your new role as an executive, you would like to have an easy way of viewing the Top five genres by gross revenue. Use the solution from the problem above to create a view. If you haven't solved 7h, you can substitute another query to create a view.*/



/*Create view TopCategory as

select name, sum(payment.amount) as 'Gross Revenue'

from category join film_category on category.category_id = film_category.category_id join inventory on film_category.film_id = inventory.film_id join rental on inventory.inventory_id = rental.inventory_id join payment on rental.rental_id = payment.rental_id group by name order by 'Gross Revenue' desc limit 5; */



/*8b. How would you display the view that you created in 8a?*/

SELECT * FROM TopCategory



/*8c. You find that you no longer need the view top_five_genres. Write a query to delete it.*/

Drop View TopCategory

