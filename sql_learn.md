### 1. Creat and add - delete

```sql
creat table test (
	a Integer Unique,
  b integer prime key, --- creat squence of int.
  c default 'panda' --- a default value.
);

--- if insert somthing not obeying this fomat of restriction, the query will fail.
```

````sql
select * from customer;

Update customer Set address = '123 music avenue', zip = '12354'
````

```sql
delete from customer where Id = 5;
insert into customer Values (3, 'a')


select * from test where ID is null; --- or is not Null;
```

```sql
DROP table IF EXISTS test; --- incase the previous data frame still exists.

alter table test add d text default 'pandas'; --- add a column

```

---

### 2. filter data:

```sql
select name, continent, population from country
	where population < 100000 
	OR population IS NULL
	AND countinent = 'Oceania' ORDER by population DESC; 
	#order in ASC #ascending or descending
	
--------------------------------------------

select name, continent, population from country
	where name LIKE '%island%' ORDER by name;
	
	
	--- island% start as island.
	--- '_a%' second letter is a.
	
```

| a    | b    |
| ---- | ---- |
| 1    | 0    |

```sql
select 
	CASE a WHEN 1 THEN 'true' ELSE 'false' end as boolA,
	CASE b WHEN 1 THEN 'true' ELSE 'false' end as boolB
	From booltest
;

	CASE a WHEN a=1 THEN 'true' ELSE 'false' end as boolA,
	CASE b WHEN b<1 THEN 'true' ELSE 'false' end as boolB
```



### 3. duplication removal

```sql
select distinct continent from country;
```

### 4. small functions

#### 4.1 join

```sql
---JOIN---
SELECT l.description AS left, r.description AS right  # description is column name
  FROM left AS l 									# l as alias
  JOIN right AS r ON l.id = r.id
  ;
  
  SELECT l.description AS left, r.description AS right
  FROM left AS l
  JOIN right AS r ON l.id = r.id
  ;

SELECT s.id AS sale, s.date, i.name, i.description, s.price 
  FROM sale AS s
  JOIN item AS i ON s.item_id = i.id
  ;
  
---------------------------------------------
--- LEN ---
select Name, length(Name) as Len 
    from City 
    order by Len desc, name; # first ordered by len. second by name. 
    
--------------------------------------------
---substring---

select substr('this is a string', 6, 3) # substr('', position index, num of characters.)

---------------------------------------------

---remove space---
trim('string', 'by what')

--------------------------------------------

upper & lower

typeof 		---read type 

cast(* as real) --- change integer to real numbers.

round(?, digit = ?)

--------------------------------------------
--- dates and times

select datetime('now')
```

![Screen Shot 2021-01-24 at 10.44.49 PM](/Users/fyenne/Downloads/booooks/semester5/sql/Screen Shot 2021-01-24 at 10.44.49 PM.png)

#### 4.2 Group_by summarise

```sql
select region, count(*)
	from country
	group by region
	
--- avg. min. max....

select count(distinct headofState) from country.

```

![Screen Shot 2021-01-24 at 10.50.06 PM](/Users/fyenne/Downloads/booooks/semester5/sql/Screen Shot 2021-01-24 at 10.50.06 PM.png)

### 5. transactions

```sql
BEGIN TRANSACTION;
INSERT INTO widgetSales ( inv_id, quan, price ) VALUES ( 1, 5, 500 );
UPDATE widgetInventory SET onhand = ( onhand - 5 ) WHERE id = 1;
END TRANSACTION;

BEGIN TRANSACTION;
INSERT INTO widgetInventory ( description, onhand ) VALUES ( 'toy', 25 );
ROLLBACK;
SELECT * FROM widgetInventory;

```



### 6. trigger

```sql
---添加新transaction 后触发，
CREATE TRIGGER newWidgetSale AFTER INSERT ON widgetSale
    BEGIN
        UPDATE widgetCustomer SET last_order_id = NEW.id WHERE widgetCustomer.id = NEW.customer_id;
    END
;

---

CREATE TRIGGER updateWidgetSale BEFORE UPDATE ON widgetSale
---before update on, 防止数据的改变 trigger
    BEGIN
        SELECT RAISE(ROLLBACK, 'cannot update table "widgetSale"') FROM widgetSale
            WHERE id = NEW.id AND reconciled = 1; ---reconcile 作为防改的标记。
    END
 ;
 
---
--- timesteps and transaction log
  
CREATE TRIGGER stampSale AFTER INSERT ON widgetSale
    BEGIN
        UPDATE widgetSale SET stamp = DATETIME('now') WHERE id = NEW.id;
        UPDATE widgetCustomer SET last_order_id = NEW.id, stamp = DATETIME('now')
            WHERE widgetCustomer.id = NEW.customer_id;
        INSERT INTO widgetLog (stamp, event, username, tablename, table_id)
            VALUES (DATETIME('now'), 'INSERT', 'TRIGGER', 'widgetSale', NEW.id);
    END
;   ---  三个文件， trigger后自动记录log。

---触发：
insert into widgetSale (item_id, customer_id, quan, price) values (3,1,1,2995)
```

