# SQL-Homework - Employee DB: A Mystery in Two Parts


## Background

The first major task is a research project on employees of the corporation from the 1980s and 1990s. All that remain of the database of employees from that period are six CSV files..

In this assignment, I have to design the tables to hold data in the CSVs, import the CSVs into a SQL database, and answer questions about the data. In other words, perform the following:

1. Data Modeling
2. Data Engineering
3. Data Analysis


## Objectives

### Data Modeling
I Inspected the CSVs and sketched out an ERD of the tables using QuickDBD.

![Screenshot](Images/ERD-JLDA.png)

### Data Engineering
* Use the information to create a table schema for each of the six CSV files. Remember to specify data types, primary keys, foreign keys, and other constraints.
* Import each CSV file into the corresponding SQL table.

```
--- Data Engineering SECTION
--- Drop table if they exist before
DROP TABLE IF EXISTS departments;
DROP TABLE IF EXISTS dept_emp;
DROP TABLE IF EXISTS dept_managers;
DROP TABLE IF EXISTS employees;
DROP TABLE IF EXISTS salaries;
DROP TABLE IF EXISTS titles;

--- Schema generate trought QuickDBD and Imported
CREATE TABLE "employees" (
    "emp_no" int   NOT NULL,
    "birth_date" date   NOT NULL,
    "first_name" varchar   NOT NULL,
    "last_name" varchar   NOT NULL,
    "gender" varchar   NOT NULL,
    "hire_date" date   NOT NULL,
    CONSTRAINT "pk_employees" PRIMARY KEY (
        "emp_no"
     )
);

CREATE TABLE "titles" (
    "emp_no" int   NOT NULL,
    "title" varchar   NOT NULL,
    "from_date" date   NOT NULL,
    "to_date" date   NOT NULL
);

CREATE TABLE "salaries" (
    "emp_no" int   NOT NULL,
    "salary" int   NOT NULL,
    "from_date" date   NOT NULL,
    "to_date" date   NOT NULL
);

CREATE TABLE "departments" (
    "dept_no" varchar   NOT NULL,
    "dept_name" varchar   NOT NULL,
    CONSTRAINT "pk_departments" PRIMARY KEY (
        "dept_no"
     )
);

CREATE TABLE "dept_managers" (
    "dept_no" varchar   NOT NULL,
    "emp_no" int   NOT NULL,
    "from_date" date   NOT NULL,
    "to_date" date   NOT NULL
);

CREATE TABLE "dept_emp" (
    "emp_no" int   NOT NULL,
    "dept_no" varchar   NOT NULL,
    "from_date" date   NOT NULL,
    "to_date" date   NOT NULL
);

ALTER TABLE "titles" ADD CONSTRAINT "fk_titles_emp_no" FOREIGN KEY("emp_no")
REFERENCES "employees" ("emp_no");

ALTER TABLE "salaries" ADD CONSTRAINT "fk_salaries_emp_no" FOREIGN KEY("emp_no")
REFERENCES "employees" ("emp_no");

ALTER TABLE "dept_managers" ADD CONSTRAINT "fk_dept_managers_dept_no" FOREIGN KEY("dept_no")
REFERENCES "departments" ("dept_no");

ALTER TABLE "dept_managers" ADD CONSTRAINT "fk_dept_managers_emp_no" FOREIGN KEY("emp_no")
REFERENCES "employees" ("emp_no");

ALTER TABLE "dept_emp" ADD CONSTRAINT "fk_dept_emp_emp_no" FOREIGN KEY("emp_no")
REFERENCES "employees" ("emp_no");

ALTER TABLE "dept_emp" ADD CONSTRAINT "fk_dept_emp_dept_no" FOREIGN KEY("dept_no")
REFERENCES "departments" ("dept_no");

--- Queries to Confirm each Table have the completed data
SELECT * FROM departments;
SELECT * FROM dept_emp;
SELECT * FROM dept_managers;
SELECT * FROM employees;
SELECT * FROM salaries;
SELECT * FROM titles;
```

### Data Analysis
Once there is a complete database, do the following:

1. List the following details of each employee: employee number, last name, first name, gender, and salary.
```
SELECT a.emp_no, a.first_name, a.last_name, a.gender, b.salary 
FROM employees AS a
LEFT JOIN salaries AS b 
ON a.emp_no = b.emp_no;
```

2. List employees who were hired in 1986.
```
SELECT * 
FROM employees
WHERE hire_date BETWEEN '1986-01-01' AND '1987-01-01'
ORDER BY hire_date;
```

3. List the manager of each department with the following information: department number, department name, the manager's employee number, last name, first name, and start and end employment dates.
```
SELECT c.dept_no,c.dept_name,b.emp_no,a.last_name,a.first_name,b.from_date, b.to_date
FROM departments AS c
JOIN dept_managers AS b
ON c.dept_no = b.dept_no
JOIN employees AS a
ON b.emp_no = a.emp_no;
```

4. List the department of each employee with the following information: employee number, last name, first name, and department name.
```
SELECT b.emp_no,a.last_name,a.first_name,c.dept_name
FROM departments AS c
JOIN dept_emp AS b
ON c.dept_no = b.dept_no
JOIN employees AS a
ON b.emp_no = a.emp_no;
```

5. List all employees whose first name is "Hercules" and last names begin with "B."
```
SELECT *
FROM employees
WHERE 	first_name = 'Hercules' AND last_name LIKE 'B%';
```

6. List all employees in the Sales department, including their employee number, last name, first name, and department name.
```
SELECT b.emp_no,a.last_name,a.first_name,c.dept_name
FROM dept_emp AS b
JOIN employees AS a
ON b.emp_no = a.emp_no
JOIN departments AS c
ON b.dept_no = c.dept_no
WHERE c.dept_name = 'Sales';
```

7. List all employees in the Sales and Development departments, including their employee number, last name, first name, and department name.
```
SELECT b.emp_no,a.last_name,a.first_name,c.dept_name
FROM dept_emp AS b
JOIN employees AS a
ON b.emp_no = a.emp_no
JOIN departments AS c
ON b.dept_no = c.dept_no
WHERE c.dept_name = 'Sales' OR c.dept_name = 'Development';
```

8. In descending order, list the frequency count of employee last names, i.e., how many employees share each last name.
```
SELECT last_name, COUNT(last_name) AS "Frecuency"
FROM employees
GROUP BY last_name
ORDER BY "Frecuency" DESC;
```

### Bonus
Generate a visualization of the data, with which you will confront your boss:

1. Importing the SQL database into Pandas.
![](Images/SQL-PandasConn.png)

2. Create a histogram to visualize the most common salary ranges for employees.
![](Images/Histogram-Code.png)

3. Create a bar chart of average salary by title.

![](Images/bar-code.png)
![](Images/average_salary_by_title.png)

