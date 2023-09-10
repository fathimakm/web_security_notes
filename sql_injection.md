
## SQL Injection

Vulnerability that consist of an attacker interfering with the SQL queries that an application makes to a database.

__Impact of SQl Vulnerablilty__
* confidentiality- unauthorized access to sensitive data like username and passwords
* Integrity - can be used to alter data in database
* Availability - can be used to delete data in database



* can escalate a SQL injection attack to compromise the underlying server or other back-end infrastructure, or perform a denial-of-service attack.
*  SQL injection attack can result in unauthorized access to sensitive data, such as passwords, credit card details, or personal user information (PII). 

### Types of SQL Injection

1. In-Band (classic)

when the attacker uses the same communication channel to both launch the attack and gather the result of the attack
- retrieved data is presented directly in the application web page

    * Error-based sql Injection: Where you force the database to generate an error giving you more information about how things operate at the back end.
    * Union based Sql injection : It's a technique that leverages the union operator to combine the result of two queries into a single one.
    `www.test.com/app.php?id=' UNION SELECT username, password FROM users--`
    we did'n give id so it's emprt
    `output:ac1, passw1, adm1, pass2`

2. Interential(Blind) 
type of vulnerability where there is no acual transfer of data via the web application (the results can't be seen in the application)
* Boolean:  the results can't be seen in the application, we can only ask the application true or false questions
* Time: pausing the database for a specefic period of time

3. Out of Band

when the attacker is unable to use the same channel to launch the attack and gather the results of the attack, it usuallly relies on the ability of an application to make a network connection.
ex: Dns or http request to deliver data to an attacker









*How to detect SQL Injection vulnerabilities*
+ majority of SQL injection vulnerabilities can be found quickly using Burp Suite's web vulnerability scanner.
-  can be detected manually by using a systematic set of tests against every entry point in the application
1. Submitting the single quote character '
2. Boolean conditions such as OR 1=1 and OR 1=2
3. payloads designed to trigger time delays when executed within a SQL query, and looking for differences in the time taken to respond.
4. OAST(out of band application security testing) payloads designed to trigger an out-of-band network interaction when executed within a SQL query, and monitoring for any resulting interactions.
5. SQL-specific syntax that evaluates to the base (original) value of the entry point, and to a different value, and looking for systematic differences in the resulting application responses.



*Most SQL injection vulnerabilities arise within the WHERE clause of a SELECT query.*

+ The most common other locations where SQL injection arises are in Update,  Select, Insert tatements and orderby, where clauses.

Some common SQL injection examples include:

+ Retrieving hidden data, where you can modify a SQL query to return additional results.
- Subverting application logic, where you can change a query to interfere with the application's logic.
+ UNION attacks, where you can retrieve data from different database tables.
- Blind SQL injection, where the results of a query you control are not returned in the application's responses.

__double-dash sequence -- is a comment indicator in SQL, and means that the rest of the query is interpreted as a comment.__

`username: bhbjhb' or 1=1 --  password: jnkjkj`
`username: bhbjhb--`
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1

__Since 1=1 is always true, the query will return all items.__
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
