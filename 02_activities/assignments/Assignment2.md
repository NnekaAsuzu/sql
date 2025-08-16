# Assignment 2: Design a Logical Model and Advanced SQL

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

#### Submission Parameters:
* Submission Due Date: `August 17, 2025`
* Weight: 70% of total grade
* The branch name for your repo should be: `assignment-two`
* What to submit for this assignment:
    * This markdown (Assignment2.md) with written responses in Section 1
    * Two Entity-Relationship Diagrams (preferably in a pdf, jpeg, png format).
    * One .sql file 
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sql/pulls/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `assignment-two`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via our Slack at `#cohort-6-help`. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.

***

## Section 1:
You can start this section following *session 1*, but you may want to wait until you feel comfortable wtih basic SQL query writing. 

Steps to complete this part of the assignment:
- Design a logical data model
- Duplicate the logical data model and add another table to it following the instructions
- Write, within this markdown file, an answer to Prompt 3


###  Design a Logical Model

#### Prompt 1
Design a logical model for a small bookstore. 📚

At the minimum it should have employee, order, sales, customer, and book entities (tables). Determine sensible column and table design based on what you know about these concepts. Keep it simple, but work out sensible relationships to keep tables reasonably sized. 

Additionally, include a date table. 

There are several tools online you can use, I'd recommend [Draw.io](https://www.drawio.com/) or [LucidChart](https://www.lucidchart.com/pages/).

**HINT:** You do not need to create any data for this prompt. This is a logical model (ERD) only. 

#### Prompt 2
We want to create employee shifts, splitting up the day into morning and evening. Add this to the ERD.

#### Prompt 3
The store wants to keep customer addresses. Propose two architectures for the CUSTOMER_ADDRESS table, one that will retain changes, and another that will overwrite. Which is type 1, which is type 2? 

**HINT:** search type 1 vs type 2 slowly changing dimensions. 

```
Your answer...
```

***




## Section 1: Your Answer
# Assignment 2 – Design a Logical Model and Advanced SQL
**Participant:** Nneka Asuzu  
**Date:** August 17, 2025

### Prompt 1: Logical Model for a Small Bookstore

I designed a logical model for a small bookstore using the following tables. The relationships are designed to minimize redundancy while keeping the tables well-structured and easy to query for operational and analytical purposes.

- **Employee**: Stores employee details such as `employee_id`, `first_name`, `last_name`, `hire_date`, and `job_title`.  
  Tracks who works at the bookstore and supports linking to orders and employee shifts.

- **Customer**: Stores customer details including `customer_id`, `first_name`, `last_name`, `email`, and `phone`.  
  Captures buyers’ information and supports linking to orders.

- **Book**: Contains descriptive information about each book, such as `book_id`, `title`, `author`, `isbn`, `genre`, and `price`.  
  Works together with the Inventory table to represent stock and track book details.

- **Inventory**: Tracks stock for each book with fields `inventory_id` (primary key), `book_id` (foreign key to Book), `quantity_on_hand`, and `reorder_level`.  
  This allows inventory levels and restocking thresholds to be managed separately from book details, without duplicating book information.

- **Order**: Records each sales order with fields such as `order_id`, `customer_id` (linking to Customer), `employee_id` (linking to Employee), and `date_id` (linking to Date).  
  Links each order to the customer who placed it, the employee who processed it, and the date of the transaction, ensuring sales can be traced to both the buyer, the staff member responsible, and the time of purchase for reporting purposes.

- **Sales**: Stores line-item details for each order, including `sale_id`, `order_id` (linking to Order), `book_id` (linking to Book), `quantity`, and `unit_price`.  
  Allows multiple books per order without duplicating customer or employee data.

- **Date**: Contains date attributes such as `date_id`, `full_date`, `day`, `month`, and `year`.  
  Linked to orders and employee shifts to support reporting and analytics over time.


---



### Prompt 2: Employee Shifts

- **Shift**: Defines work shifts with fields such as `shift_id`, `shift_name`, `start_time`, and `end_time`.  
  Morning and Evening shifts are included to manage daily scheduling.

- **Employee_Shift**: Links employees to specific shifts on specific dates using `employee_id`, `shift_id`, and `date_id`.  
  This table allows flexible scheduling without duplicating employee or shift information.

---

### Prompt 3: Customer Address Architectures

- **Customer_Address_Type1 (Overwrite / Type 1 SCD)**:  
  Contains `customer_id`, `street`, `city`, `postal_code`, `province`, and `country`.  
  When a customer updates their address, the old information is overwritten.  
  Use case: Only the latest address matters.

- **Customer_Address_Type2 (Retain History / Type 2 SCD)**:  
  Contains `customer_address_id` (primary key), `customer_id`, `street`, `city`, `postal_code`, `province`, `country`, `start_date`, `end_date`, and `is_current`.  
  Keeps a record of all previous addresses for historical reporting.  
  Use case: Track address changes over time for analytics or auditing.

**Type 1** = Overwrite / no history  
**Type 2** = Retain changes / full history

---

### ERD Diagrams

#### ERD #1 – Main Bookstore Logical Model (Type 1 Address)
![Bookstore ERD #1 – Type 1 Address](Bookstore_ERD1_Type1_MainModel.png)

#### ERD #2 – Bookstore (Type 2: Retain Customer Address History)
![Bookstore ERD #2 – Type 2 Address History](Bookstore_ERD2_Type2_AddressHistory.png)








## Section 2:
You can start this section following *session 4*.

Steps to complete this part of the assignment:
- Open the assignment2.sql file in DB Browser for SQLite:
	- from [Github](./02_activities/assignments/assignment2.sql)
	- or, from your local forked repository  
- Complete each question


### Write SQL

#### COALESCE
1. Our favourite manager wants a detailed long list of products, but is afraid of tables! We tell them, no problem! We can produce a list with all of the appropriate details. 

Using the following syntax you create our super cool and not at all needy manager a list:
```
SELECT 
product_name || ', ' || product_size|| ' (' || product_qty_type || ')'
FROM product
```

But wait! The product table has some bad data (a few NULL values). 
Find the NULLs and then using COALESCE, replace the NULL with a blank for the first column with nulls, and 'unit' for the second column with nulls. 

**HINT**: keep the syntax the same, but edited the correct components with the string. The `||` values concatenate the columns into strings. Edit the appropriate columns -- you're making two edits -- and the NULL rows will be fixed. All the other rows will remain the same.

<div align="center">-</div>

#### Windowed Functions
1. Write a query that selects from the customer_purchases table and numbers each customer’s visits to the farmer’s market (labeling each market date with a different number). Each customer’s first visit is labeled 1, second visit is labeled 2, etc. 

You can either display all rows in the customer_purchases table, with the counter changing on each new market date for each customer, or select only the unique market dates per customer (without purchase details) and number those visits. 

**HINT**: One of these approaches uses ROW_NUMBER() and one uses DENSE_RANK().

2. Reverse the numbering of the query from a part so each customer’s most recent visit is labeled 1, then write another query that uses this one as a subquery (or temp table) and filters the results to only the customer’s most recent visit.

3. Using a COUNT() window function, include a value along with each row of the customer_purchases table that indicates how many different times that customer has purchased that product_id.

<div align="center">-</div>

#### String manipulations
1. Some product names in the product table have descriptions like "Jar" or "Organic". These are separated from the product name with a hyphen. Create a column using SUBSTR (and a couple of other commands) that captures these, but is otherwise NULL. Remove any trailing or leading whitespaces. Don't just use a case statement for each product! 

| product_name               | description |
|----------------------------|-------------|
| Habanero Peppers - Organic | Organic     |

**HINT**: you might need to use INSTR(product_name,'-') to find the hyphens. INSTR will help split the column. 

<div align="center">-</div>

#### UNION
1. Using a UNION, write a query that displays the market dates with the highest and lowest total sales.

**HINT**: There are a possibly a few ways to do this query, but if you're struggling, try the following: 1) Create a CTE/Temp Table to find sales values grouped dates; 2) Create another CTE/Temp table with a rank windowed function on the previous query to create "best day" and "worst day"; 3) Query the second temp table twice, once for the best day, once for the worst day, with a UNION binding them. 

***

## Section 3:
You can start this section following *session 5*.

Steps to complete this part of the assignment:
- Open the assignment2.sql file in DB Browser for SQLite:
	- from [Github](./02_activities/assignments/assignment2.sql)
	- or, from your local forked repository  
- Complete each question

### Write SQL

#### Cross Join
1. Suppose every vendor in the `vendor_inventory` table had 5 of each of their products to sell to **every** customer on record. How much money would each vendor make per product? Show this by vendor_name and product name, rather than using the IDs.

**HINT**: Be sure you select only relevant columns and rows. Remember, CROSS JOIN will explode your table rows, so CROSS JOIN should likely be a subquery. Think a bit about the row counts: how many distinct vendors, product names are there (x)? How many customers are there (y). Before your final group by you should have the product of those two queries (x\*y). 

<div align="center">-</div>

#### INSERT
1. Create a new table "product_units". This table will contain only products where the `product_qty_type = 'unit'`. It should use all of the columns from the product table, as well as a new column for the `CURRENT_TIMESTAMP`.  Name the timestamp column `snapshot_timestamp`.

2. Using `INSERT`, add a new row to the product_unit table (with an updated timestamp). This can be any product you desire (e.g. add another record for Apple Pie). 

<div align="center">-</div>

#### DELETE 
1. Delete the older record for the whatever product you added.

**HINT**: If you don't specify a WHERE clause, [you are going to have a bad time](https://imgflip.com/i/8iq872).

<div align="center">-</div>

#### UPDATE
1. We want to add the current_quantity to the product_units table. First, add a new column, `current_quantity` to the table using the following syntax.
```
ALTER TABLE product_units
ADD current_quantity INT;
```

Then, using `UPDATE`, change the current_quantity equal to the **last** `quantity` value from the vendor_inventory details. 

**HINT**: This one is pretty hard. First, determine how to get the "last" quantity per product. Second, coalesce null values to 0 (if you don't have null values, figure out how to rearrange your query so you do.) Third, `SET current_quantity = (...your select statement...)`, remembering that WHERE can only accommodate one column. Finally, make sure you have a WHERE statement to update the right row, you'll need to use `product_units.product_id` to refer to the correct row within the product_units table. When you have all of these components, you can run the update statement.
