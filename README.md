# Eniac project 2023
---
Eniac is an online marketplace specializing in Apple-compatible accessories. It was founded 10 years ago in Spain and it has since grown and expanded to other neighboring countries.

Besides offering a wide catalog of products at competitive prices, Eniac provides friendly and professional tech support and consultation to its customers. The warmhearted spirit that thrives with direct contact with the customers is at the roots of the company.

Since the company went public, the investors have been pushing the company to scale up the business and become a big e-commerce player globally. It goes without saying that it is an arduous challenge to do so while retaining the human side of the business, Eniac’s emblem.

Here are some numbers that will help you understand Eniac’s scope (data from April 2017 – March 2018):

- Revenue: 40,044,542 €
* Avg monthly revenue: 1,011,256 €
+ Avg order price: 710 €
- Avg item price: 540 €

**Eniac is exploring an expansion to the Brazilian market.**

Here comes **Magist**. Magist is a Brazilian Software as a Service company that offers a centralized order management system to connect small and medium-sized stores with the biggest Brazilian marketplaces. 

In Brazil, however, **Eniac is considering signing a 3-year contract with Magist** and operating through external marketplaces as an intermediate step, while it tests the market, creates brand awareness, and explores the option of opening its own Brazilian marketplace.
.
**Eniac has two following main concerns about the entering brazilian market:**
- Is Magist a good fit for high-end tech products?
+ Are orders delivered on time?

Please follow the list of more conrete questions from the different memebers if the Eniac company.

We got a data from magist and got the answers to our questions wiht mySQL.

---

## **Business relation questions related to expansion of Eniac company in Brazil**

### In realtion to products:

- What categories of tech products does Magist have?

Computers,Computers_accessories,Electronics,Tablets_printing_image,Watches_gifts

- How many tech products have been sold? 

```sql 
select count(order_items.product_id) as "How many tech products have been sold"
from product_category_name_translation
inner join products on products.product_category_name=product_category_name_translation.product_category_name
inner join order_items on products.product_id=order_items.product_id
where product_category_name_english in ("Computers","Computers_accessories","Electronics","Tablets_printing_image","Watches_gifts")
```

- How many products of these tech categories have been sold (splitted by categories)?

```sql
select product_category_name_english as "Tech product category", count(order_items.product_id) as "How many products have been sold"
from product_category_name_translation
inner join products on products.product_category_name=product_category_name_translation.product_category_name
inner join order_items on products.product_id=order_items.product_id
where product_category_name_english in ("Computers","Computers_accessories","Electronics","Tablets_printing_image","Watches_gifts")
group by product_category_name_english
```

- How many of all products have been sold? 

```sql
select count(order_items.product_id) as "How many of all products have been sold"
from product_category_name_translation
inner join products on products.product_category_name=product_category_name_translation.product_category_name
inner join order_items on products.product_id=order_items.product_id
```

- What’s the average price of all products being sold?

```sql
select avg(price) as "Rounded Avg.Price"
from product_category_name_translation
inner join products using (product_category_name)
inner join order_items using (product_id)
```

- What’s the average price of the tech products being sold by category?

```sql
select product_category_name_english as "Tech product category", round(avg(price),0) as "Rounded Avg.Price"
from product_category_name_translation
inner join products on products.product_category_name=product_category_name_translation.product_category_name
inner join order_items on products.product_id=order_items.product_id
where product_category_name_english in ("Computers","Computers_accessories","Electronics","Tablets_printing_image","Watches_gifts")
group by product_category_name_english
```

- What’s the average price of the tech products being sold?

```sql
select avg(price) as "Rounded Avg.Price"
from product_category_name_translation
inner join products on products.product_category_name=product_category_name_translation.product_category_name
inner join order_items on products.product_id=order_items.product_id
where product_category_name_english in ("Computers","Computers_accessories","Electronics","Tablets_printing_image","Watches_gifts")
```

- Are expensive products popular? 

```sql
SELECT COUNT(price),
CASE 
    WHEN price >= 1000
    THEN "very expensive"
    WHEN price >= 540
    THEN "expensive"
    WHEN price >= 200
    THEN "medium"
    WHEN price >= 50
    THEN "cheap"
    ELSE "very cheap"
END AS price_range
FROM order_items
GROUP BY price_range
ORDER BY COUNT(price) ASC
```

- Are expensive tech products popular? 

```sql
select count(price),
case 
    when price >= 1000
    then "very expensive"
    when price >= 540
    then "expensive"
    when price >= 200
    then "medium"
    when price >= 50
    then "cheap"
    else "very cheap"
end as price_range
from order_items
inner join products
on order_items.product_id=products.product_id
INNER join product_category_name_translation
on products.product_category_name=product_category_name_translation.product_category_name
where product_category_name_english in ('computers', 'computers_accessories', 'electronics', 'tablets_printing_image', 'watches_gifts')
group by price_range
ORDER BY count(price) ASC
```

### In relation to sellers:

- How many months of data are included in the magist database?
```sql
select min(order_purchase_timestamp) as earliest_date , max(order_purchase_timestamp) as present_date,
TIMESTAMPDIFF(month,'2016-09-04','2018-10-17')
from orders
```

- How many sellers are there?

```sql
select count(distinct(seller_id)) as "Amount of all sellers" from order_items
```

-How many Tech sellers are there?

```sql
select count(distinct(seller_id)) as "Amount of tech sellers"
from product_category_name_translation
inner join products using (product_category_name)
inner join order_items using (product_id)
where product_category_name_english in ("Computers","Computers_accessories","Electronics","Tablets_printing_image","Watches_gifts")
``

- What percentage of overall sellers are Tech sellers?

```sql
select (443*100/count(DISTINCT(seller_id))) as Tech_sellers_percentage
FROM order_items
```

- What is the total amount earned by all sellers?

```sql
SELECT sum(price) as total_earning_of_sellars
FROM order_items
```

- What is the total amount earned by all Tech sellers?

```sql
Select sum(price) as earnings_of_tech_sellers
from order_items
inner join products
on order_items.product_id=products.product_id
INNER join product_category_name_translation
on products.product_category_name=product_category_name_translation.product_category_name
where product_category_name_english in ('computers', 'computers_accessories', 'electronics', 'tablets_printing_image', 'watches_gifts')
```

- What is the percentage of overall sales are sales of tech sellers?

```sql
select (2507698.274344206*100/sum(price)) as percentage_of_tech_sellers_earnings
FROM order_items
```

- Can you work out the average monthly income of tech sellers?

```sql
SELECT year(order_purchase_timestamp) as year_of_delivery, month(order_purchase_timestamp) as month_of_delivery, sum(price) as monthly_earnings
from order_items
inner join products
on order_items.product_id=products.product_id
INNER join product_category_name_translation
on products.product_category_name=product_category_name_translation.product_category_name
inner join orders
on order_items.order_id=orders.order_id
where product_category_name_english in ('computers', 'computers_accessories', 'electronics', 'tablets_printing_image', 'watches_gifts')
GROUP BY year_of_delivery, month_of_delivery
order by year_of_delivery
```

### In relation to delivery time:

- What’s the average time between the order being placed and the product being delivered?

1. option 1
```sql
select avg(TIMESTAMPDIFF(day,order_purchase_timestamp,order_delivered_customer_date))
FROM orders
```

2. option 2
```sql
SELECT (TIMESTAMPDIFF(day,order_purchase_timestamp,order_delivered_customer_date)) as delivery_time, order_id as all_orders
FROM orders
```
```sql
SELECT sum(TIMESTAMPDIFF(day,order_purchase_timestamp,order_delivered_customer_date)) as delivery_time, count(order_id) as all_orders
FROM orders
```
```sql
SELECT (sum(TIMESTAMPDIFF(day,order_purchase_timestamp,order_delivered_customer_date))/count(order_id)) as avarage_order_time
FROM orders
```

- How many orders are delivered on time?

```sql
select count(distinct order_id) as delivered_on_time
from orders
where (timestampdiff(day,order_delivered_customer_date,order_estimated_delivery_date)) = 0
```
- How many orders are delivered with a delay?

```sql
SELECT count(order_id) as deliveries,
case 
    when TIMESTAMPDIFF(day, order_estimated_delivery_date, order_delivered_customer_date) < 0
    then "early delivery"
    when TIMESTAMPDIFF(day, order_estimated_delivery_date, order_delivered_customer_date) = 0
    then "on-time delivery"
    else "late delivery"
end as "waiting_time"
from orders
group by waiting_time
ORDER BY deliveries ASC
```

- Total amount of all orders delivered on time?

```sql
select count(distinct order_id) as delivered_on_time
from orders
```
