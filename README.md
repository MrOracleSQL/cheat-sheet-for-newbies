ALTER SESSION SET CURRENT_SCHEMA = HR;

1.1

    SELECT * FROM Employees;

1.2 

    SELECT first_name, last_name FROM Employees;

1.3 

    SELECT first_name, last_name, hire_date FROM Employees ORDER BY hire_date DESC;

1.4 

    SELECT first_name, last_name, hire_date, (salary + salary * 0.15) AS salary_big FROM Employees;

1.5 

    SELECT first_name, last_name, salary FROM Employees WHERE salary > 10000;

1.6 

    SELECT * FROM Employees WHERE phone_number IS NULL;

1.7 

    SELECT * FROM Employees WHERE commission_pct IS NOT null;

1.8 

    SELECT Departments.department_name, Employees.first_name, Employees.last_name FROM Departments, Employees WHERE Departments.manager_id = Employees.employee_id;

1.9 

    SELECT Employees.first_name, Employees.last_name, Employees.salary FROM Employees, Jobs WHERE Employees.job_id = Jobs.job_id AND Employees.salary NOT BETWEEN Jobs.min_salary AND Jobs.max_salary;

1.10 

    SELECT Employees.first_name, Employees.last_name, Regions.region_name, Countries.country_name 
    FROM Employees, Regions, Locations, Departments, Countries 
    WHERE Employees.department_id = Departments.department_id
    AND Departments.location_id = Locations.location_id
    AND Locations.country_id = Countries.country_id 
    AND Countries.region_id = Regions.region_id; 


----------------

2.1 Employees that has last letter 'r' in name

	SELECT first_name FROM Employees WHERE first_name LIKE '%a';

2.2 Employees that has second letter 'r' in name and a salary < 10000

	SELECT * FROM Employees WHERE first_name LIKE '_a%' AND salary < 5000;

2.3 Employees that have salary < 7000 or are in department 20 and salary <10000 

	SELECT first_name, last_name, department_id FROM Employees WHERE salary < 3000 OR (salary < 5000 AND department_id = 50);

2.4 Show number of employees

	SELECT Count(employee_id) FROM Employees;

2.5 Sum spent on salary on this month

	SELECT SUM(salary) FROM Employees;

2.6 Show job.id, job.title, first.name and last.name

	SELECT Employees.first_name, Employees.last_name, Jobs.job_id, Jobs.job_title FROM Employees, Jobs WHERE Employees.job_id = Jobs.job_id;

2.7 Show job.id, job.title and average salary for every type of job

	SELECT Jobs.job_id, Jobs.job_title, ROUND(AVG(Employees.salary)) FROM Jobs, Employees WHERE Jobs.job_id = Employees.job_id GROUP BY jobs.job_id, Jobs.job_title;

2.8 Employees that have salary with commision a total greater or lower than what is expected for their job

	SELECT * FROM Employees, Jobs WHERE (Employees.salary + Employees.salary * NVL(Employees.commission_pct, 0)) NOT BETWEEN Jobs.min_salary AND Jobs.max_salary AND Jobs.job_id = Employees.job_id;


3.1 MAx, Min, Average salary and number of employees on each department

	SELECT department_id, MAX(salary) as SalariuMaxim, MIN(salary) as SalariuMinim, Round(AVG(salary)) as SalariuMediu, Count(employee_id) as NrAngajati FROM Employees GROUP BY department_id; 

3.2 Number of employees on each department that have salary below average salary on company

	SELECT department_id, Count(employee_id) FROM Employees WHERE salary < (SELECT AVG(salary) FROM Employees) GROUP BY department_id;

3.3 Employees with salary lower than average on their department

	SELECT first_name, last_name FROM Employees, (SELECT AVG(salary) medie, department_id departament FROM Employees GROUP BY department_id) temp
	WHERE salary < temp.medie AND Employees.department_id = temp.departament;

	SELECT first_name, last_name FROM Employees a WHERE salary < (SELECT AVG(salary) FROM Employees b WHERE a.department_id = b.department_id);

3.4 Show managers departments (ordered DESC by number of managers in each department)

	SELECT department_id, Count(employee_id) AS NrManageri FROM Employees, (SELECT DISTINCT manager_id FROM Employees) temp
	WHERE Employees.employee_id = temp.manager_id GROUP BY department_id ORDER BY(NrManageri) DESC;


----------------

ALTER SESSION SET CURRENT_SCHEMA = IE_JOSEPHINO;

4.1 Show salary of the managers

	SELECT salary, employee_id FROM Employees WHERE employee_id IN (SELECT manager_id FROM Employees);

4.2 Average of managers salary

	SELECT ROUND(AVG(salary)) FROM Employees WHERE employee_id IN (SELECT manager_id FROM Employees);

4.3 Employees that have salary greater than average of managers

	SELECT * FROM Employees WHERE salary > (SELECT ROUND(AVG(salary)) FROM Employees WHERE employee_id IN (SELECT manager_id FROM Employees));

4.4 Employees that have salary greater that at least one manager in their department

	SELECT * FROM Employees, (SELECT MIN(salary) AS salary, department_id FROM Employees WHERE employee_id IN (SELECT manager_id FROM Employees) GROUP BY(department_id)) temp
	WHERE Employees.salary > temp.salary AND Employees.department_id = temp.department_id;

4.5 Employees that are not managers that have salary greater that at least one manager in their department

	SELECT * FROM Employees, (SELECT MIN(salary) AS salary, department_id FROM Employees WHERE employee_id IN (SELECT manager_id FROM Employees) GROUP BY(department_id)) temp
	WHERE Employees.salary > temp.salary AND Employees.department_id = temp.department_id AND Employees.employee_id NOT IN (SELECT manager_id FROM Employees WHERE manager_id IS NOT NULL);


----------------

ALTER SESSION SET CURRENT_SCHEMA = IE_JOSEPHINO;

5.1 Create tables

	CREATE TABLE PRODUSE (codp NUMBER(3), denp VARCHAR(30), coddep NUMBER(3));
	CREATE TABLE STOCURI (cods NUMBER(3), cant NUMBER(4), pret NUMBER(4));

5.2 Modify table PRODUCTS

	ALTER TABLE PRODUSE MODIFY coddep VARCHAR2(5);
	ALTER TABLE PRODUSE ADD ump VARCHAR2(3);

5.3 Modify table STOCKS

	ALTER TABLE STOCURI RENAME COLUMN cods TO codp;
	ALTER TABLE STOCURI ADD an NUMBER(4);
	ALTER TABLE STOCURI ADD CONSTRAINT ck_an CHECK(an >= 1999 AND an < 2050);

5.4 Set primary keys and foreign keys

	ALTER TABLE PRODUSE ADD CONSTRAINT codp_pk PRIMARY KEY(codp);
	ALTER TABLE STOCURI ADD CONSTRAINT codpan_pk PRIMARY KEY(codp, an);
	ALTER TABLE STOCURI ADD CONSTRAINT codp_fk FOREIGN KEY(codp) REFERENCES PRODUSE(codp);	

5.5 Insert values in table PRODUCTS

	INSERT INTO PRODUSE VALUES(1, 'acs', 'BG', 'UNITS');

5.6 Insert values in table STOCKS

	INSERT INTO STOCURI VALUES(1, 50, 5, 2004);

5.7 Create view for table STOCKS and show it

	CREATE VIEW STOCURI_VIEW AS
	SELECT * FROM STOCURI;

	SELECT * FROM STOCURI_VIEW;


----------------

ALTER SESSION SET CURRENT_SCHEMA = IE_JOSEPHINO;

6.1 Create view

	CREATE VIEW salariat_salariu_mare

6.2 Create view with the employee with the highest salary

	CREATE VIEW salariat_salariu_mare AS
	SELECT * FROM HR.Employees WHERE salary = (SELECT MAX(salary) FROM HR.Employees);

6.3 Grant permission to a mate (doesn't work)

	GRANT SELECT ON salariat_salariu_mare TO John;

6.4 Grant permission to a mate for a table

	GRANT ALL ON produse TO John;

6.5 Show all views

	SELECT VIEW_NAME FROM USER_VIEWS;

6.6 Grant update persmission to a mate for a table

	GRANT UPDATE ON produse TO John;

6.7 Create view for table PRODUCTS

	CREATE VIEW produse_view AS
	SELECT * FROM produse;

6.8 Grant update persmission to a mate for a table

	GRANT UPDATE ON produse_view TO John;

	UPDATE John.produse1 SET ump = 'kgs' WHERE codp = '1';
	COMMIT;
