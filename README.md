# SQL Practice Queries and Solutions


# Q1. Question:- Given a student table, find out the total marks of the top 2 subjects for each student (sname) based on marks.

``sql
CREATE TABLE Students (sname VARCHAR(5), sid VARCHAR(5), marks INT);``
``sql
INSERT INTO Students (sname, sid, marks) VALUES
    ('ABC', 'SCI', 75),
    ('ABC', 'MAT', 80),
    ('ABC', 'HIS', 95),
    ('DEF', 'SCI', 90),
    ('DEF', 'HIS', 91),
    ('DEF', 'MAT', 75); ``
    

# Solution:
``sql
WITH rank_table AS(
	SELECT sname,
		sid,
		marks,
		DENSE_RANK() OVER (PARTITION BY sname ORDER BY marks DESC) AS marks_rank  
	FROM Students
	)
SELECT sname, SUM(marks) AS total_marks FROM rank_table WHERE marks_rank <= 2 GROUP BY sname; ``


# Q2. Question:- Find the maximum ID by excluding duplicates.

``sql
CREATE TABLE employees ( id INT);``
``sql
INSERT INTO employees (id) VALUES 
(2), 
(5), 
(6), 
(6), 
(7), 
(8), 
(8);``

# Solution:
``sql
WITH count_table AS(
  SELECT id, COUNT(id) AS count_no
  FROM employees GROUP BY id
)
SELECT MAX(id) FROM count_table WHERE count_no < 2; ``


# Q3. Question:- How do you find the 3rd highest salary from a table?

``sql
CREATE TABLE Employees_records ( Employeeld INT PRIMARY KEY, 
	EmployeeName VARCHAR(50), 
	City VARCHAR(50), 
	DateOfJoining  TIMESTAMP,
	Salary DECIMAL(10, 2), 
	DepartmentId INT);``
``sql
INSERT INTO Employees_records (Employeeld, EmployeeName, City, DateOfJoining, Salary, DepartmentId);  
VALUES 
    (1, 'Rahul', 'Pune', '2022-06-30 20:53:58.963', 123400.00, 4), 
    (2, 'Sharath', 'Kanpur', '2022-03-17 06:13:11.000', 56789.00, 3), 
    (3, 'Pankaj', 'Delhi', '2022-07-24 20:23:25.200', 34560.00, 3), 
    (4, 'Sharddul', 'Pune', '2023-07-01 16:10:11.530', 23400.00, NULL), 
    (5, 'Mohan', 'Manali', '2023-07-14 10:15:10.310', 12600.00, NULL), 
    (6, 'Rekha', 'Manali', '2023-07-14 10:15:10.310', 12600.00, NULL), 
    (7, 'Mamta Banarjee', 'Kolkata', '2023-07-15 13:47:48.530', 15600.00, NULL), 
    (8, 'Parambeer Singh', 'Mumbai', '2022-09-16 07:27:46.453', 3450.00, 4);``

# Solution:
``sql
WITH sal_table AS(
  SELECT *,
    ROW_NUMBER() OVER(ORDER BY Salary DESC) AS row_no
  FROM employees_records 
)
SELECT * FROM sal_table WHERE row_no = 3;``


# Q4. Question:- calculating the percentage of genders in an Employee table.
``sql
CREATE TABLE employ ( eid INT PRIMARY KEY, ename VARCHAR(50), gender VARCHAR(10));``
``sql
INSERT INTO employ (eid, ename, gender) 
VALUES 
    (1, 'John Doe', 'Male'), 
    (2, 'Jane Smith', 'Female'), 
    (3, 'Michael Johnson', 'Male'), 
    (4, 'Emily Davis', 'Female'), 
    (5, 'Robert Brown', 'Male'), 
    (6, 'Sophia Wilson', 'Female'), 
    (7, 'David Lee', 'Male'), 
    (8, 'Emma White', 'Female'), 
    (9, 'James Taylor', 'Male'), 
    (10, 'William Clark', 'Male');``

# Solution:
``sql
SELECT 
	COUNT(CASE WHEN gender = 'Male' THEN 1 END)*100 /COUNT(*) AS Male_per,
	COUNT(CASE WHEN gender = 'Female' THEN 1 END)*100 / COUNT(*) AS Female_per
FROM employ;`` 


# Q5.Question:- Find out the student wise total marks for Top 2 subjects.

``sql
CREATE TABLE StudentMarks ( student_name VARCHAR(50), subject VARCHAR(50), marks INT );``
``sql
INSERT INTO StudentMarks (student_name, subject, marks)
VALUES
    ('Alice', 'Math', 65),
    ('Alice', 'Science', 80),
    ('Alice', 'English', 78),
    ('Bob', 'Math', 82),
    ('Bob', 'Science', 85),
    ('Bob', 'English', 88),
    ('Catherine', 'Math', 70),
    ('Catherine', 'Science', 72),
    ('Catherine', 'English', 68),
    ('Daniel', 'Math', 99);``

# Solution:
``sql
WITH top_marks AS (
  SELECT *, 
	  DENSE_RANK() OVER(PARTITION BY student_name ORDER BY marks DESC) AS marks_rank
  FROM studentmarks 
)
SELECT * FROM top_marks WHERE marks_rank < 3; ``


# Q6.Question:- Extract the Domain from the Email column in Employee Table.

``sql
CREATE TABLE Users ( id INT PRIMARY KEY, name VARCHAR(50), email VARCHAR(50) );``
``sql
INSERT INTO Users (id, name, email) VALUES
  (1, 'John Doe', 'john.doe@example.com'),
  (2, 'Jane Smith', 'jane.smith@company.com'),
  (3, 'Alice Johnson', 'alice.johnson@business.org'),
  (4, 'Robert Brown', 'robert.brown@enterprise.net'),
  (5, 'Emily Davis', 'emily.davis@startup.io'),
  (6, 'Michael Wilson', 'michael.wilson@web.co'),
  (7, 'Sophia Taylor', 'sophia.taylor@tech.dev'),
  (8, 'David Anderson', 'david.anderson@service.us');``

# Solution:
``sql
SELECT SUBSTRING(email, POSITION('@' IN email) + 1) AS domain_names FROM users ``

-- for POSTGRE SQL -> POSITION() for sql server -> CHARINDEX()

	

# Q7.Question:- Based on their most recent transaction date, write a query that retrieves the users along with the number of products they bought. 
# Output the user's most recent transaction date, user ID, and the number of products sorted order by the transaction date. 
``sql
CREATE TABLE transactions (
  product_id INT,
  userid INT,
  spend DECIMAL(10, 2),
  transaction_date TIMESTAMP );``
``sql
INSERT INTO transactions (product_id, userid, spend, transaction_date) VALUES
    (3673, 123, 68.9, '2022-07-08 10:00:00'),
    (9623, 123, 274.1, '2022-07-08 10:00:00'),
    (1467, 115, 19.9, '2022-07-08 10:00:00'),
    (2513, 159, 25.0, '2022-07-08 10:00:00'),
    (1452, 159, 74.5, '2022-07-10 10:00:00'),
    (1452, 123, 74.5, '2022-07-10 10:00:00'),
    (9765, 123, 100.15, '2022-07-11 10:00:00'),
    (6536, 115, 57.0, '2022-07-12 10:00:00'),
    (7384, 159, 15.5, '2022-07-12 10:00:00'),
    (1247, 159, 23.4, '2022-07-12 10:00:00');``

``sql
WITH date_table AS(
  SELECT *,
  	DENSE_RANK() OVER(PARTITION BY userid ORDER BY transaction_date) AS date_ranking
  FROM transactions 
)
SELECT userid, COUNT(product_id) AS total_products FROM date_table 
	WHERE date_ranking = 1 GROUP BY userid;``


# Q8.Question:- We need to Find department wise minimum salary emp_name and maximum salary emp_name.

``sql
CREATE TABLE emps_tbl ( emp_name VARCHAR(50), dept_id INT, salary INT);``
``sql
INSERT INTO emps_tbl (emp_name, dept_id, salary)
VALUES 
    ('Siva', 1, 30000), 
    ('Ravi', 2, 40000), 
    ('Prasad', 1, 50000), 
    ('Sai', 2, 20000), 
    ('Anna', 2, 10000);``

# Solution:
``sql
WITH min_sal_table AS(
  SELECT *,
	  ROW_NUMBER() OVER(PARTITION BY dept_id ORDER BY salary ASC) AS min_salary  
  FROM emps_tbl
),
max_sal_table AS(
  SELECT *,
	  ROW_NUMBER() OVER(PARTITION BY dept_id ORDER BY salary DESC) AS max_salary
  FROM emps_tbl
)
SELECT m.dept_id, 
  MIN(m.emp_name), MIN(m.salary),
  MAX(x.emp_name), MAX(x.salary) 
FROM min_sal_table m
JOIN max_sal_table x ON m.dept_id = x.dept_id AND m.min_salary = 1 AND max_salary = 1
GROUP BY m.dept_id; ``


# Q9. Question:- Write an SQL query to retrieve the total sales amount in each category.
# Include all categories, if no products were sold in a category (display as 0). Display the output in ascending order of total_sales. */

``sql
CREATE TABLE categories ( category_id INT PRIMARY KEY, category_name VARCHAR(50) );``

``sql
INSERT INTO categories (category_id, category_name) 
VALUES 
    (1, 'Electronics'), 
    (2, 'Clothing'), 
    (3, 'Books'), 
    (4, 'Home Decor');``

``sql
CREATE TABLE sales_tbl (
  sale_id INT PRIMARY KEY, 
  category_id INT, 
  amount INT, 
  sale_date DATE, 
  FOREIGN KEY (category_id) REFERENCES categories(category_id) );``

``sql
INSERT INTO sales_tbl (sale_id, category_id, amount, sale_date) 
VALUES 
    (1, 1, 500, '2022-01-05'), 
    (2, 1, 800, '2022-02-10'), 
    (4, 3, 200, '2022-02-20'), 
    (5, 3, 150, '2022-03-01'), 
    (6, 4, 400, '2022-02-25'), 
    (7, 4, 600, '2022-03-05');``

# Solution:
``sql
WITH total_sales AS(
  SELECT category_id, 
    SUM(amount) AS total_sales 
  FROM sales_tbl 
  GROUP BY category_id 
  ORDER BY SUM(amount) ASC
)
SELECT c.category_id, 
	CASE 
		WHEN ts.total_sales IS NULL THEN 0
		ELSE ts.total_sales
	END
FROM categories c LEFT JOIN total_sales ts 
ON c.category_id = ts.category_id; ``

# Q10. Question:- Write a SQL query to find emp who is inside the office..
``sql
CREATE TABLE office (
emp_id INT,
emp_status VARCHAR(10), 
time_id TIMESTAMP);``
``sql
INSERT INTO office (emp_id, emp_status, time_id) VALUES
    (1, 'in', '2023-12-22 09:00:00'),
    (1, 'out', '2023-12-22 09:15:00'),
    (2, 'in', '2023-12-22 09:00:00'),
    (2, 'out', '2023-12-22 09:15:00'),
    (2, 'in', '2023-12-22 09:30:00'),
    (3, 'out', '2023-12-22 09:00:00'),
    (3, 'in', '2023-12-22 09:15:00'),
    (3, 'out', '2023-12-22 09:30:00'),
    (3, 'in', '2023-12-22 09:45:00'),
    (4, 'in', '2023-12-22 09:45:00'),
    (5, 'out', '2023-12-22 09:40:00');``

# Soultion:
``sql
WITH curr_status AS(
  SELECT *, 
  	ROW_NUMBER() OVER(PARTITION BY emp_id ORDER BY time_id DESC) AS status
  FROM office 
)
SELECT emp_id FROM curr_status 
WHERE emp_status = 'in' AND status = 1; ``

# Q11. Question:- Return the Players and their total runs scored, those who did at least two half centuries and did not out for a duck..
``sql
CREATE TABLE matches ( match_id int, player_id int, runs_scored int );``
``sql
INSERT INTO matches 
VALUES 
  (1, 208, 28), 
  (2, 105, 0), 
  (3, 201, 75), 
  (4, 310, 48), 
  (5, 402, 52),
  (6, 208, 58),
  (7, 105, 78), 
  (8, 402, 25), 
  (9, 310, 0),
  (10, 201, 90),
  (11, 208, 84), 
  (12, 105, 102);``

``sql
CREATE TABLE players ( id int primary key, name varchar(20) );``
``sql
INSERT INTO players (id, name) VALUES
    (208, 'Dekock'), 
    (105, 'Virat'), 
    (201, 'Miller'), 
    (310, 'Warner'), 
    (402, 'Buttler');``

# Soultion:
``sql
WITH summary AS(
  SELECT player_id,
	  COUNT(CASE WHEN runs_scored >= 50 THEN 1 END) AS half_cen,
	  COUNT(CASE WHEN runs_scored = 0 THEN 1 END) AS ducks, 
	  SUM(runs_scored) AS total_runs
  FROM matches
  GROUP BY player_id
)
SELECT ps.name, s.player_id, s.total_runs FROM summary s JOIN players ps 
ON s.player_id = ps.id
AND 
s.half_cen >= 2 AND s.ducks = 0; ``
