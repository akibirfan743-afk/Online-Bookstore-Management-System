
-- Books data
Drop table if exists Books;
Create table Books(
	Book_id serial primary key,
	Title varchar(100),
	Author varchar(100),
	Genre varchar(50),
	Published_year int,
	Price numeric(10,2),
	Stock int
)

-- Customers data
Drop table if exists customers;
Create table customers(
	Customer_id Serial primary key,
	Name varchar(100),
	Email varchar(100),
	Phone varchar(50),
	City varchar(50),
	Country varchar(150)
); 

--Orders data
Drop table if exists orders;
	
Create table orders(
	order_id serial primary key,
	customer_id int references customers(Customer_id),
	Book_id int references Books(book_id),
	order_date date,
	Quantity int,
	Total_amount numeric(10,2)
);




-- Import data into books table
COPY customers(Book_id,Title,Author,Genre,Published_year, price,stock)
FROM 'C:\Users\pc\Downloads\Books.csv'
CSV HEADER

-- Import data into customer table
COPY customers(Customer_id,Name,Email,Phone,City,country,)
from 'C:\Users\pc\Downloads\Customers.csv'
csv header 



-- Import data into orders table
copy orders(Order_id,customer_id,book_id,order_date,quantity,total_amount)
from 'C:\Users\pc\Downloads\Orders.csv'
csv header;


SELECT * FROM books;
SELECT * FROM customers;
SELECT * FROM orders;



-- 1) Retrieve all books in the "Fiction" genre:

CREATE VIEW Fiction_genre as 
SELECT * 
FROM books
Where genre = 'Fiction'


-- Accessing view
select * from Fiction_genre;




-- 2) Find books published after the year 1950:

Create view published_after_1950 as
select * 
from books
where published_year >1950;

select * from published_after_1950;

-- 3) List all customers from the Canada:

Create view from_canada as
SELECT * 
FROM customers
Where country = 'Canada';

select * from from_canada;


-- 4) Show orders placed in November 2023:

CREATE VIEW orders_in_november as 
select * from orders
where order_date between '2023-11-01' AND '2023-11-30';

select * from orders_in_november;

-- 5) Retrieve the total stock of books available:

create view Total_stock as
select sum(stock) as Total_stock
from books;

select * from total_Stock;

-- 6) Find the details of the most expensive book:
create view most_expensive_book as
Select * from books
order by price desc
limit 1;

select * from most_expensive_book;


-- 7) Show all customers who ordered more than 1 quantity of a book:

create view Quantity_morethen_5 as
SELECT DISTINCT c.customer_id, c.name,o.quantity
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
WHERE o.quantity > 5;




-- 8) Retrieve all orders where the total amount exceeds $20:

create view amount_greater_then_20 as
select * from orders
where total_amount > 20;

select * from amount_greater_then_20;


-- 9) List all genres available in the Books table:

create view books_genres as
select distinct(genre)
from books;

select * from books_genres;

-- 10) Find the book with the lowest stock:

select min(stock) from books;

create view lowest_stock as
select * from books
order by stock
limit 1;

select * from lowest_stock



-- 11) Calculate the total revenue generated from all orders:

create view Total_revenue as
select sum(total_amount) as Total_Revenue
from orders;

select * from Total_revenue;

-- Advance Questions : 

-- 1) Retrieve the total number of books sold for each genre:


create view books_sold as
SELECT b.genre, SUM(o.quantity) AS total_books_sold
FROM books b
JOIN orders o ON b.book_id = o.book_id
GROUP BY b.genre;


select * from books_sold;


-- 2) Find the average price of books in the "Fantasy" genre:

select * from books;

create view avg_of_Fantasy as 
select avg(price) as avg_fantasy_price
from books
where genre = 'Fantasy';

select * from avg_of_Fantasy;



-- 3) List customers who have placed at least 2 orders:

create view at_least_2_orders as
select c.customer_id, c.name, count(o.order_id) as Total_orders
from customers c
join orders o ON c.customer_id = o.customer_id
Group by c.customer_id, c.name
having count(o.order_id) >=2;

select * from at_least_2_orders;


-- 4) Find the most frequently ordered book:

create view frequently_ordered_book as
SELECT book_id, count(order_id) as order_count
from orders
group by book_id
order by order_count desc 
limit 1;

select * from frequently_ordered_book;

-- 5) Show the top 3 most expensive books of 'Fantasy' Genre :

create view expensive_fantasy_book as
select book_id,title,genre,price from books
Where Genre = 'Fantasy'
order by price desc
limit  3;

select * from expensive_fantasy_book;

-- 6) Retrieve the total quantity of books sold by each author:

create view author_total_sold_book as
select b.author, sum(o.quantity) as Total_quantity_sold
from books b
join orders o on b.book_id = o.book_id
group by b.author

select * from author_total_sold_book;

-- 7) List the cities where customers who spent over $30 are located:
	
create view city_spend as
select distinct(c.city),o.total_amount
from customers c
join orders o on c.customer_id = o.customer_id
Where o.total_amount > 30;


select * from city_spend;



-- 8) Find the customer who spent the most on orders:

create view spend_most_on_orders as
select c.customer_id, c.name, sum(o.total_amount) as total_spend
from customers c
join orders o on c.customer_id = o.customer_id
group by c.customer_id,c.name
order by total_spend desc;

select * from spend_most_on_orders;

--9) Calculate the stock remaining after fulfilling all orders:

Create view remaining_stock as
SELECT b.book_id, b.title, b.stock, coalesce(sum(o.quantity),0) as order_quantity,
	b.stock - coalesce(sum(o.quantity),0) as remaining_stock
from books b
join orders o on o.book_id = b.book_id
group by b.book_id

select * from remaining_stock;

