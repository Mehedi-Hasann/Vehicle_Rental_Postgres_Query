# 🚗 Vehicle Rental System

A SQL-based backend system for managing vehicle rentals.  
This project demonstrates **database design**, **data insertion**, and **advanced SQL queries** such as `JOIN`, `EXISTS`, `WHERE`, `GROUP BY`, and `HAVING`.

---

## Project Overview

The Vehicle Rental System manages:
- Users (Admin & Customer)
- Vehicles (Car, Bike, Truck)
- Bookings (Rental history)

This project focuses on **relational database concepts** and **real-world query scenarios**.

---

## Database Schema

### Users Table
```sql
create table users (
  user_id int primary key,
  name varchar(100),
  email varchar(100) unique,
  phone varchar(100),
  role varchar(100) check(role in ('Admin','Customer'))
);
```
### Vehicles Table
```sql
create table vehicles (
  vehicle_id int primary key,
  name varchar(100),
  type varchar(50) check(type in ('car','bike','truck')),
  model int,
  registration_number varchar(200),
  rental_price int,
  status varchar(100) check(status in ('available','rented','maintenance'))
);
```
### Bookings Table
```sql
create table bookings (
  booking_id int primary key,
  user_id int references users(user_id),
  vehicle_id int references vehicles(vehicle_id),
  start_date date,
  end_date date,
  status varchar(100) check(status in ('pending','confirmed','completed','cancelled')),
  total_cost int
);
```
## Sample Data Insertion
### Users
```sql
insert into users values
(1, 'Alice', 'alice@example.com', '1234567890', 'Customer'),
(2, 'Bob', 'bob@example.com', '0987654321', 'Admin'),
(3, 'Charlie', 'charlie@example.com', '1122334455', 'Customer');
```
### Vehicles
```sal
insert into vehicles values
(1, 'Toyota Corolla', 'car', 2022, 'ABC-123', 50, 'available'),
(2, 'Honda Civic', 'car', 2021, 'DEF-456', 60, 'rented'),
(3, 'Yamaha R15', 'bike', 2023, 'GHI-789', 30, 'available'),
(4, 'Ford F-150', 'truck', 2020, 'JKL-012', 100, 'maintenance');
```
### Bookings
```sql
insert into bookings values
(1, 1, 2, '2023-10-01', '2023-10-05', 'completed', 240),
(2, 1, 2, '2023-11-01', '2023-11-03', 'completed', 120),
(3, 3, 2, '2023-12-01', '2023-12-02', 'confirmed', 60),
(4, 1, 1, '2023-12-10', '2023-12-12', 'pending', 100);
```
#SQL Queries & Explanations
##Query 1: JOIN
Retrieve booking information along with: Customer name and Vehicle name
Concepts used: INNER JOIN
```sql
select b.booking_id, u.name as customer_name, 
  v.name as vehicle_name, b.start_date, b.end_date, 
  b.status from users as u 
inner join bookings as b on u.user_id=b.user_id
inner join vehicles as v on b.vehicle_id=v.vehicle_id;
```
####Explanation
As the customer name is on users table, vehicle name is on vehicles table and booking information is on the bookings table that's why
we join the users,vehicles and the bookings table to retrive the data. bookings table have the link with the users and the vehicles that's
why we join the bookings table withe other two tables.

##Query 2: EXISTS
Find all vehicles that have never been booked.
Concepts used: NOT EXISTS
```sql
select * from vehicles as v
where not exists (select b.vehicle_id from bookings as b 
                  where v.vehicle_id = b.vehicle_id);
```
####Explanation
In this case we have to retrive the vehicle information that have not booked yet. To retrive this information firstly we need to retrive the 
vehicle that are booked from the bookings table, then we apply the not exists clause on the vehicles table with retrive data. Then it will return 
the vehicle information except where v.vehicle_id = b.vehicle_id that means it return the data where v.vehicle_id <> b.vehicle_id

##Query 3: WHERE
Retrieve all available vehicles of a specific type (e.g. cars).
Concepts used: SELECT, WHERE
```sql
select * from vehicles
where status = 'available' and type = 'car';
```
####Explanation
To retrive the vehicle from vehicles table of avaiable car we find to check both status = 'available' and type = 'car' condition. 
Then it will return the vehicle that are available car.

##Query 4: GROUP BY and HAVING
Find the total number of bookings for each vehicle and display only those vehicles that have more than 2 bookings.
Concepts used: GROUP BY, HAVING, COUNT
```sql
select v.name as vehicle_name, count(*) as total_bookings from vehicles as v
join bookings as b on v.vehicle_id = b.vehicle_id
group by v.name having count(*) > 2;
```
####Explanation
To find the vehicle that have more than 2 bookings we need to join the bookings and the vehicles table because booking info and vehicle info contain two 
different table. Then if apply the goup by clause then we can make group based on the vehicle name as the requirement because we need to find the booking
info for each vehicle. After that we check count(*)> 2, it will return that group that have more than 2 rows.
