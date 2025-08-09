# Sql-advance
In this repository I have solved multiple sql advance questions with multiple ways possible some queries can be cost effective and some not but I have tried to compare different ways of writing same query.

# Question 1 - Given : <br/><img width="372" alt="image" src="https://github.com/user-attachments/assets/e0835fb3-906b-4082-b77c-e5d7ae5236cc" /><br>
# Required:<br>
<img width="392" alt="image" src="https://github.com/user-attachments/assets/d3431f01-d117-4127-9002-63ff2602eb83" /><br>
<br>
<br>

## Create Table using this query

select create table icc_world_cup
(
Team_1 Varchar(20),
Team_2 Varchar(20),
Winner Varchar(20)
);
INSERT INTO icc_world_cup values('India','SL','India');
INSERT INTO icc_world_cup values('SL','Aus','Aus');
INSERT INTO icc_world_cup values('SA','Eng','Eng');
INSERT INTO icc_world_cup values('Eng','NZ','NZ');
INSERT INTO icc_world_cup values('Aus','India','India');


## Query 1:

SELECT name, COUNT(name) AS match_played,sum(matches_won) as match_won, COUNT(name)- sum(matches_won) as matches_lost
FROM (
    SELECT team_1 AS name, case when team_1=winner then 1 else 0 end as matches_won FROM icc_world_cup
    UNION ALL
    SELECT team_2 AS name, case when team_2=winner then 1 else 0 end as matches_won FROM icc_world_cup
) AS B
GROUP BY name

## Query 2:

select B.name , B.match_played,coalesce(A.winners,0) as matches_won, (B.match_played - coalesce(A.winners,0)) as lost_matches from (
(select winner,count(winner) as winners from icc_world_cup group by winner) as A 
right join
(SELECT name, COUNT(name)AS match_played 
FROM (
    SELECT team_1 AS name FROM icc_world_cup
    UNION ALL
    SELECT team_2 AS name FROM icc_world_cup
) AS B
GROUP BY name) as B
on A.winner=B.name)
<br>
<br>

# Question 2- Given : <br/><img width="315" alt="image" src="https://github.com/user-attachments/assets/bd195304-fb4f-4f44-85e8-d45f525454ab" />
<br>

# Required:<br><img width="383" alt="image" src="https://github.com/user-attachments/assets/04776309-7f7d-4c47-9af5-6b8393bba743" />
<br>
<br>

## Create Table using this query
create table customer_orders (
order_id integer,
customer_id integer,
order_date date,
order_amount integer
);


insert into customer_orders values(1,100,cast('2022-01-01' as date),2000),(2,200,cast('2022-01-01' as date),2500),(3,300,cast('2022-01-01' as date),2100)
,(4,100,cast('2022-01-02' as date),2000),(5,400,cast('2022-01-02' as date),2200),(6,500,cast('2022-01-02' as date),2700)
,(7,100,cast('2022-01-03' as date),3000),(8,400,cast('2022-01-03' as date),1000),(9,600,cast('2022-01-03' as date),3000)
;

## Query 1
select A.order_date,total_customers,new_customer, total_customers - new_customer as repeated_customers from
(select order_date,count(customer_id) as new_customer from customer_orders as o
where o.customer_id not in (select customer_id from customer_orders as b where o.order_date>b.order_date)
group by order_date) A
join 
(select order_date,count(customer_id) as total_customers from customer_orders as o
group by order_date) B
on A.order_date=B.order_date

## Query 2
select A.order_date,
sum(case when order_date=new_customer then 1 else 0 end) as new_customer,
sum(case when order_date!=new_customer then 1 else 0 end) as old_customer
from
(
(select * from customer_orders) A
join
(select customer_id,min(order_date) as new_customer from customer_orders
group by customer_id) B
on A.customer_id=B.customer_id
)
group by A.order_date
order by A.order_date

<br>
<br>


# Question 3- Given : <br/><img width="537" alt="image" src="https://github.com/user-attachments/assets/06903561-0b1c-4318-bf10-6d43bbe9344b" />
<br>

# Required:<br><img width="353" alt="image" src="https://github.com/user-attachments/assets/3b9e45f4-8739-49b8-8fd6-02d829fa1cd7" />
<br>
<br>

## Create Table using this query
create table entries ( 
name varchar(20),
address varchar(20),
email varchar(20),
floor int,
resources varchar(10));

insert into entries 
values ('A','Bangalore','A@gmail.com',1,'CPU'),('A','Bangalore','A1@gmail.com',1,'CPU'),('A','Bangalore','A2@gmail.com',2,'DESKTOP')
,('B','Bangalore','B@gmail.com',2,'DESKTOP'),('B','Bangalore','B1@gmail.com',2,'DESKTOP'),('B','Bangalore','B2@gmail.com',1,'MONITOR')


## Query 1
with total_visit as
(select name,count(name) as total_visits from entries
group by name),
max_visits as
(select name,floor,count(1),rank() over( partition by name order by count(1) desc)  as ranks from entries
group by name,floor),

resource as
(select name,string_agg(distinct resources, ', ') as resources_used
from entries 
group by name)

select total_visit.name,max_visits.floor as most_visited_floor,resources_used from max_visits
inner join total_visit on total_visit.name=max_visits.name
inner join resource on resource.name=total_visit.name
where ranks=1
 

<br>
<br>

# Question 4- 
<br><img width="567" alt="image" src="https://github.com/user-attachments/assets/1b44e983-27cc-4dd1-8e98-0153be20020e" />
<br>![image](https://github.com/user-attachments/assets/57c460c4-3baf-442b-b03c-8c7bced9ee3c)
<br>
<br>

write a query to provide a date for nth occurance of sunday in future from a given date

SELECT '2025-07-02'::DATE 
       + ((7 - DATE_PART('dow', DATE '2025-07-02')) + (2 * 7)) * INTERVAL '1 day' AS result_date;

<br>
<br>

# Question 5- 
<br><img width="1920" height="1696" alt="image" src="https://github.com/user-attachments/assets/8bb1765b-098c-43ae-b31b-8a6b6bcfdaf5" />
<br>

# Question 6- 
<br><img width="1920" height="1696" alt="image" src="https://github.com/user-attachments/assets/7a4b31fa-6dfc-4182-ae35-5d976c4e1ff3" />
<br>

# Question 7- 
<br><img width="1920" height="3058" alt="image" src="https://github.com/user-attachments/assets/d32a9e41-ad53-4297-996e-c04998176ff7" />
<br>

# Question 8-
<br><img width="1920" height="2148" alt="image" src="https://github.com/user-attachments/assets/2aa31ffe-9d9f-468b-a5f3-99d769c03ddb" />
<br>

# Question 9- 
<br><img width="1920" height="2191" alt="image" src="https://github.com/user-attachments/assets/8ff98a48-cd5c-4d2b-8688-f2dda07e9531" />
<br>


# Question 10-
<br><img width="1920" height="3208" alt="image" src="https://github.com/user-attachments/assets/6e1840d5-0c6a-424e-b32b-46283e078643" />
<br>


# Question 11-
<br><img width="1920" height="2950" alt="image" src="https://github.com/user-attachments/assets/33f333a2-01c6-49a2-ae07-2783f2971164" />
<br>


# Question 12-
<br><img width="1920" height="2491" alt="image" src="https://github.com/user-attachments/assets/2cd5e550-c762-4dcc-9a6c-70a730039a88" />
<br>


# Question 13- 
<br><img width="1920" height="2445" alt="image" src="https://github.com/user-attachments/assets/88da9f04-aa36-4d51-b532-661b7a51bcba" />
<br>


# Question 14- 
<br><img width="1920" height="2593" alt="image" src="https://github.com/user-attachments/assets/7d8041b7-f2a0-47bf-b9cc-d9d17954bc7b" />
<br>


# Question 15- 
<br><img width="1920" height="2221" alt="image" src="https://github.com/user-attachments/assets/fac2224f-d95f-4a0c-a0c1-c5aa47093996" />
<br>

# Question 16-
<br><img width="1920" height="1732" alt="image" src="https://github.com/user-attachments/assets/f6f2e8ae-8d08-41bc-ac1e-4ad8fb6025ed" />
<br>


# Question 17-
<br><img width="1920" height="2613" alt="image" src="https://github.com/user-attachments/assets/99726d87-9b27-4cc0-83ec-f5fa2c15d0da" />
<br>


# Question 18-
<br><img width="1920" height="2094" alt="image" src="https://github.com/user-attachments/assets/89edd6d3-ac67-41c7-a941-322f8d0e27f3" />
<br>


# Question 19- 
<br><img width="1920" height="2611" alt="image" src="https://github.com/user-attachments/assets/17d51307-1871-43ad-8baf-6f057885957c" />
<br>



# Question 20- 
<br><img width="1920" height="2143" alt="image" src="https://github.com/user-attachments/assets/87e40ea7-dcc5-41fb-896c-62330a819aa1" />
<br>


# Question 21- 
<br><img width="1920" height="2407" alt="image" src="https://github.com/user-attachments/assets/cf369b57-0862-4e67-94a6-296534ae778c" />
<br>


# Question 22- 
<br><img width="1920" height="1791" alt="image" src="https://github.com/user-attachments/assets/5d8e789a-5247-45c5-86b2-541e9fc85ed1" />
<br>

# Question 23- 
<br><img width="1920" height="4092" alt="image" src="https://github.com/user-attachments/assets/2f42e593-7fd6-4b96-8447-c7abe098e3c9" />
<br>

# Question 24- 
<br><img width="1920" height="2439" alt="image" src="https://github.com/user-attachments/assets/ae7db48a-5fe7-4777-b09b-dcbf6976c5be" />
<br>


