## ***Os enuncio una serie de consultas para hacer en casa o en clase:***

1. Mostrar los nombres y apellidos de todos los actores de la tabla de actores.

~~~sql
SELECT first_name AS Nombre, last_name As Apellido
FROM ACTOR;
~~~

2. Necesitas encontrar el número de identificación, nombre y apellido de un actor, del que sólo conoces el nombre: "Joe".

~~~sql
SELECT actor_id AS ID, first_name AS NOMBRE, last_name AS APELLIDO 
FROM actor
WHERE first_name LIKE 'JOE';
~~~

3. Encontrar todos los actores cuyo apellido contenga las letras "GEN".

~~~sql
SELECT * 
FROM actor
WHERE last_name LIKE '%GEN%';
~~~

4. Usando IN, mostrar las columnas country_id y country de los siguientes países: Afganistán, Bangladesh y China.

~~~sql
SELECT country_id AS ID, country AS PAIS
FROM country
where country IN ('Afghanistan', 'Bangladesh', 'China');
~~~

5. Enumerar los apellidos de los actores y el número de actores que tienen el mismo apellido, pero sólo para los apellidos con al menos dos actores.

~~~sql
SELECT last_name AS Apellido, COUNT(*) As Tienen_Mismo_Apellido
FROM actor
GROUP BY last_name
HAVING COUNT(*) >=  2;
~~~

6. Utiliza JOIN para mostrar el nombre y los apellidos, así como la dirección, de cada miembro del personal. Utiliza las tablas staff y address.

~~~sql
SELECT first_name AS NOMBRE, last_name AS APELLIDO, address AS DIRECCION
FROM staff INNER JOIN address ON address.address_id = staff.staff_id;
~~~

7. Utiliza JOIN para mostrar la cantidad total que ingresó cada miembro del personal en marzo de 2020. Utiliza las tablas staff y payment.

~~~sql
SELECT first_name AS nombre, SUM(payment.amount)
FROM payment INNER JOIN staff ON payment.staff_id = staff.staff_id
GROUP BY first_name;
~~~

8. Enumerar cada película y el número de actores que aparecen en la lista para esa película. Usar las tablas film_actor y film.

~~~sql
SELECT title AS Titulo, COUNT(*) AS Numero_Actores
FROM actor INNER JOIN film_actor ON actor.actor_id = film_actor.actor_id
			INNER JOIN film ON film_actor.film_id = film.film_id
GROUP BY title;
~~~

9. ¿Cuántas copias de la película " Hunchback Impossible" existen en el sistema de inventario?

~~~sql
SELECT COUNT(*) AS copias 
FROM inventory
WHERE film_id = (SELECT FILM_ID 
				 FROM FILM
				 WHERE TITLE = 'HUNCHBACK IMPOSSIBLE' );
~~~

10. Usando las tablas de pago y cliente y el comando JOIN, lista el total pagado por cada cliente. Lista los clientes alfabéticamente por apellido.

~~~sql
SELECT first_name AS nombre, last_name AS Apellido, SUM(amount) AS Total_Pagado
FROM customer INNER JOIN payment ON customer.customer_id = payment.customer_id
GROUP BY first_name, last_name
ORDER BY last_name asc;
~~~

11. Utiliza las subconsultas para mostrar todos los actores que aparecen en la película "Alone Trip". Lista los actores alfabéticamente por apellido.

~~~sql
-- Paso 1 conseguir el id de la película:
-- SELECT film_id From film Where title = 'ALONE TRIP'
-- Paso 2 conseguir los id de los actores de esa película:
-- SELECT actor_id 
-- From film_actor 
-- WHERE film_actor.film_id = (SELECT film_id
--                             From film
--                             Where title = 'ALONE TRIP')
-- Paso 3 Mostrar los nombre de los actores de esa película:
SELECT first_name as "Nombre", last_name as "Apellido"
FROM actor
WHERE actor_id IN (SELECT actor_id
					From film_actor
					WHERE film_actor.film_id = (SELECT film_id
					From film
					Where title = 'ALONE TRIP'))
ORDER BY last_name ASC;	
~~~

12. Quieres realizar una campaña de marketing por correo electrónico en Canadá, para lo cual necesitas los nombres y direcciones de correo electrónico de todos los clientes canadienses. Utiliza las subconsultas para recuperar esta información.

~~~sql
-- Paso 1 sacar la id de canada
-- SELECT country_id FROM country WHERE country = 'Canada'
-- Paso 2 sacar las ciudades de canada
-- SELECT city_id FROM city INNER JOIN country ON country.country_id = city.country_id
-- WHERE city.country_id = (SELECT country_id 
-- FROM country WHERE country = 'Canada')
-- Paso 3 sacar las direcciones de estas ciudades
-- SELECT address_id FROM address INNER JOIN city ON address.city_id = city.city_id
-- WHERE address.address_id IN (SELECT city_id 
--                              FROM city INNER JOIN country ON country.country_id = city.country_id
--  								            WHERE city.country_id = (SELECT country_id 
--                                                        FROM country 
 --                                                       WHERE country = 'Canada'))
-- Paso 4 sacar el email de los clientes de esas ciudades
SELECT first_name AS Nombre, last_name AS Apellido, email AS correo_electronico
FROM customer INNER JOIN address ON customer.address_id = address.address_id
WHERE customer.address_id IN (SELECT address_id 
                              FROM address INNER JOIN city ON address.city_id = city.city_id
	                            WHERE address.address_id IN (SELECT city_id 
                                                           FROM city INNER JOIN country ON country.country_id = city.country_id
                                                           WHERE city.country_id = (SELECT country_id 
                                                           FROM country WHERE country = 'Canada')))
~~~

13. Mostrar las películas más frecuentemente alquiladas en orden descendente.

~~~sql
SELECT title AS Pelicula, COUNT(*) AS Frecuencia
FROM rental INNER JOIN inventory ON rental.inventory_id = inventory.inventory_id
			INNER JOIN film ON film.film_id = inventory.film_id
GROUP BY title
ORDER BY COUNT(*) DESC
LIMIT 10
~~~

14. Lista los géneros más populares en orden descendente.

~~~sql
SELECT name AS Categoria, COUNT(*) AS Frecuencia
FROM rental INNER JOIN inventory ON rental.inventory_id = inventory.inventory_id
			INNER JOIN film ON film.film_id = inventory.film_id
			INNER JOIN film_category ON film.film_id = film_category.film_id
			INNER JOIN category ON film_category.category_id = category.category_id
GROUP BY name
ORDER BY COUNT(*) DESC
~~~

15. Lista para cada almacén su ID, su ciudad, su país y el total de ventas realizadas en 2020.

~~~sql
SELECT store.address_id AS Almacen, city.city AS Ciudad, country.country AS Pais, SUM(AMOUNT) AS total_Ventas
FROM payment INNER JOIN staff ON payment.staff_id = staff.staff_id
     INNER JOIN store ON staff.store_id = store.store_id
	 INNER JOIN address ON store.address_id = address.address_id
	 INNER JOIN city ON address.city_id = city.city_id
	 inner JOIN country ON city.country_id = country.country_id
WHERE payment.payment_date >= '2020-01-01' and payment.payment_date <= '2020-12-31'
GROUP BY store.address_id, city.city, country.country
ORDER BY store.address_id asc
~~~
