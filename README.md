# Apply Filters to SQL Queries

## Step 1 & Step 2: Project Description and Scenario
My organization is working to make their system more secure. It is my job to ensure the system is safe, investigate all potential security issues, and update employee computers as needed. As a cybersecurity professional, I must examine the organization's data in the `employees` and `log_in_attempts` tables to detect anomalies and identify system updates.

This technical report details how I leveraged SQL filtering commands to investigate suspicious login attempts and isolate specific employee workstations requiring security patches.

* **Project Name:** Apply filters to SQL queries
* **Database Environment:** MariaDB / MySQL Shell
* **Core Commands Used:** `SELECT`, `FROM`, `WHERE`, `AND`, `OR`, `NOT`, `LIKE`

---

## Step 3: Retrieve After-Hours Failed Login Attempts
* **The Problem:** A potential security incident occurred after standard business hours (after 18:00). To investigate, all failed system login attempts that took place during this late-night window must be audited.
* **Solution Method:** I queried the `log_in_attempts` table. I used the `WHERE` clause with the logical operator `AND` to filter for attempts where the `login_time` was greater than '18:00' and the `success` column was `FALSE` (indicating an unsuccessful login).

```sql
SELECT * FROM log_in_attempts 
WHERE login_time > '18:00' AND success = FALSE;
```

#### 📸 Output Verification Screenshot:
![After-Hours Failed Login Attempts](images/01_failed_logins.png)

* **Final Solution:** The query successfully filtered for and returned all after-hours failed login attempts. The output showed specific compromised login records (such as usernames `apatel`, `pwashing`, and `tshah`), allowing the security team to identify target profiles and block suspicious source IP addresses.

---

## Step 4: Retrieve Login Attempts on Specific Dates
* **The Problem:** A suspicious network security event occurred on 2022-05-09. To analyze the scope of this incident, I must audit all login activity that happened on that specific date and the day before (2022-05-08).
* **Solution Method:** I queried the `log_in_attempts` table. I used the `WHERE` clause combined with the logical operator `OR` to filter for records where the `login_date` matched either '2022-05-09' or '2022-05-08'.

```sql
SELECT * FROM log_in_attempts 
WHERE login_date = '2022-05-09' OR login_date = '2022-05-08';
```

#### 📸 Output Verification Screenshot:
![Login Attempts on Specific Dates](images/02_specific_dates.png)

* **Final Solution:** The query returned all login attempts (both successful and failed) within this 48-hour attack window. Reviewing these logs helped establish a comprehensive timeline of the threat actor's activity.

---

## Step 5: Retrieve Login Attempts Outside of Mexico
* **The Problem:** After investigating the login attempt logs, I believe there is an issue with attempts originating from outside of Mexico. These external foreign login attempts must be isolated for analysis.
* **Solution Method:** I queried the `log_in_attempts` table. Since the dataset represents Mexico as both 'MEX' and 'MEXICO', I used the `WHERE` clause with the logical operator `NOT` and combined it with `LIKE 'MEX%'` to exclude any country starting with those letters.

```sql
SELECT * FROM log_in_attempts 
WHERE NOT country LIKE 'MEX%';
```

#### 📸 Output Verification Screenshot:
![Login Attempts Outside Mexico](images/03_outside_mexico.png)

* **Final Solution:** The query filtered out all local Mexican login events, highlighting attempts originating from foreign countries (such as Canada `CAN` and the United States `USA`). This enabled the team to check for anomalous geo-location logins.

---

## Step 6: Retrieve Employees in Marketing
* **The Problem:** My team wants to update the computers for certain employees in the Marketing department who work in the East building. I need to get information on which employee machines require these updates.
* **Solution Method:** I queried the `employees` table. I used the `WHERE` clause with the logical operator `AND` to filter for employees whose `department` is exactly 'Marketing' and whose `office` matches the pattern 'East%' (representing the East building rooms).

```sql
SELECT * FROM employees 
WHERE department = 'Marketing' AND office LIKE 'East%';
```

#### 📸 Output Verification Screenshot:
![Marketing Employees East Building](images/04_marketing_east.png)

* **Final Solution:** This query successfully returned the list of target employees (such as `elarson`, `jdarosa`, and `fbautist`) and their device IDs in the East building, allowing the IT department to run targeted patch deployments.

---

## Step 7: Retrieve Employees in Finance or Sales
* **The Problem:** The computers for employees in the Finance and Sales departments also need a different security update. I need to extract information on employees who belong strictly to these two departments.
* **Solution Method:** I queried the `employees` table. I used the `WHERE` clause with the logical operator `OR` to filter for records where the `department` is equal to 'Finance' or 'Sales'.

```sql
SELECT * FROM employees 
WHERE department = 'Finance' OR department = 'Sales';
```

#### 📸 Output Verification Screenshot:
![Finance or Sales Employees](images/05_finance_sales.png)

* **Final Solution:** The output returned all employees belonging to either the Finance department (such as `sgilmore`, `wjaffrey`, and `abernard`) or the Sales department. This consolidated list helps automate the specific patch updates for these departments.

---

## Step 8: Retrieve All Employees Not in IT
* **The Problem:** My team needs to perform one final security update on employees' machines. Employees in the Information Technology (IT) department already received this update, so I must identify all employees in all other departments.
* **Solution Method:** I queried the `employees` table. I used the `WHERE` clause with the logical operator `NOT` to filter out and exclude any employees whose `department` was designated as 'Information Technology'.

```sql
SELECT * FROM employees 
WHERE NOT department = 'Information Technology';
```

#### 📸 Output Verification Screenshot:
![Employees Outside IT](images/06_not_it.png)

* **Final Solution:** The query successfully returned all company personnel who are not in the IT department (such as `elarson` in Marketing and `bmoreno` in Human Resources). This produced a clean list of devices that still need the mandatory update.

---

## Step 9: Summary
I applied filters to SQL queries to get specific information on login attempts and employee machines. I used two different tables, `log_in_attempts` and `employees`. I used the logical operators `AND`, `OR`, and `NOT` to filter for the specific information needed for each investigation. I also used the `LIKE` operator and the percentage sign (`%`) wildcard to filter for specific text and office patterns. These practices ensure data integrity and facilitate proactive incident response.
