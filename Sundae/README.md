- 👋 Hi, desjnz/Sundae is a repository to share the assessment responses for your consideration.  

-  Q1 ANSWER: 
-  select *
from (
Select  
a.First_Name
,a.Last_Name
,c.FilmCount
From 
(
Select 
fa.Actor_ID
,count(fa.film_Id) as FilmCount
From film_actor as fa 
Group by fa.Actor_ID
) as c
LEFT JOIN actor as a on a.actor_ID = c.actor_ID
ORDER BY c.FilmCount DESC
) a
limit 100;

-  Q2 ANSWER: 
-  SELECT 
 a.Name as CategoryName
,f.title as FilmTitle
,a.films as TotalFilms
FROM (
SELECT fc.category_ID, c.Name, count(fc.film_id) as films
FROM film_category fc 
LEFT JOIN category c on c.category_id = fc.category_Id
WHERE  c.name NOT IN ('Sports', 'Games') 
Group By fc.category_ID, c.Name
) as a
LEFT JOIN film_category as fc on fc.category_ID = a.category_ID
LEFT JOIN film as f on f.film_id = fc.film_ID
Group By a.Name, f.Title, a.films
Order By a.Films DESC
;

-  Q3 Answer:
-  Select 
extract(WEEK from date(r.rental_ts)) as RentalWeek
,count( r.rental_id) as TotalRentals
,sum(p.amount) as TotalRevenue
,count(distinct r.customer_ID) as TotalCustomers
From rental as r
INNER JOIN inventory as iv on iv.inventory_id = r.inventory_id and iv.Store_ID = 1
LEFT JOIN payment as p on p.rental_id = r.rental_id
Where extract(WEEK from date(r.rental_ts)) >= (extract(WEEK from date(NOW()))-12)
GROUP BY RentalWeek
;

-  Q4 Answer: 
-  SELECT count(distinct b.customer_ID) as ActiveCustomers
from
(
Select cr.customer_ID, cr.address_id, count(r.rental_ID) as Rental_Count
From customer as cr
INNER JOIN rental as r on r.customer_ID = cr.customer_id 
and 
(
(date(r.rental_ts) >=  '2020-07-16' and date(r.rental_ts) <= '2020-07-31')
OR 
(date(r.rental_ts) >=  '2020-08-16' and date(r.rental_ts) <= '2020-08-31')
) 
LEFT JOIN inventory as iv on iv.inventory_id = r.inventory_ID
INNER JOIN film as f on f.film_id = iv.Film_ID and f.rating = 'PG'
Where cr.active = 1
GROUP BY cr.customer_ID, cr.address_id
) b
LEFT JOIN address as d on d.address_id = b.address_id  
INNER JOIN city as ct on ct.city_ID = d.city_ID and ct.city <> 'Dallas'
WHERE b.rental_Count >=2
;

-  Q5 Answer: 
-  select 
DayInAug
,RentalCount
,avg(RentalCount) 
	OVER (Order by DayInAug asc
		  rows between 6 preceding and current row) as avg
from (
Select 
EXTRACT(Day from Date(r.rental_ts)) as DayInAug 
,count(rental_ID) as RentalCount
From rental r
WHERE Date(r.rental_ts) >= '2020-08-01' and Date(r.rental_ts) <= '2020-08-31'
GROUP BY DayInAug 
) as t 
GROUP BY 
t.DayInAug
,t.RentalCount
;

-  BonusRound
-  OBSV1: Not a significant pattern of customers per city.
-  select 
st.store_id, st.manager_staff_id--, --ad."address" as StoreAddress, ct."city" as StoreCity, ad.district as StoreDistrict
,ct2.city
,count(distinct cra.customer_ID) as ActiveCustomers
--,count(distinct cri.customer_ID) as InactiveCustomers
from store as st
left join address as ad on ad.address_id = st.address_id
left join city as ct on ct.city_id = ad.City_Id
LEFT JOIN Customer as cra on cra.store_id = st.store_ID and cra.active = 1
LEFT JOIN address as ad2 on ad2.address_ID = cra.address_ID
LEFT JOIN city as ct2 on ct2.city_ID = ad2.City_ID
--LEFT JOIN Customer as cri on cri.store_id = st.store_ID and cri.active <> 1
Group By st.store_id, st.manager_staff_id--, --ad."address", ct."city", ad.district
,ct2.City
ORDER BY ActiveCustomers DESC
;

-  OBSV2: Continuing to explore the type of inventory per store with rental trends to recommend a shift in stock to increase revenue...

