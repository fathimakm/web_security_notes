# web_sec_notes
notes from portswigger

## SQL Injection

1. __SQL injection (SQLi) is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. __**
2. An attacker can modify or delete this data, causing persistent changes to the application's content or behavior.
3. can escalate a SQL injection attack to compromise the underlying server or other back-end infrastructure, or perform a denial-of-service attack.
4.  SQL injection attack can result in unauthorized access to sensitive data, such as passwords, credit card details, or personal user information (PII). 


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
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1

__Since 1=1 is always true, the query will return all items.__
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1






***

# Directory Traversal
*aka file path traversal*

+ allows an attacker to read arbitrary files on the server that is running an application
- This might include application code and data, credentials for back-end systems, and sensitive operating system files. 
* an attacker might be able to write to arbitrary files on the server, allowing them to modify application data or behavior, and ultimately take full control of the server.
*On Unix-based operating systems ../ is valid*
*On Windows, both ../ and ..\ are valid directory traversal sequences*

1. ../../../etc/passwd   (simple file path traversal)
2. /etc/passwd (absolute path)
3. ....//....//....//etc/passwd
4. ..%252f..%252f..%252fetc/passwd
5. filename=/var/www/images/../../../etc/passwd 
(If an application requires that the user-supplied filename must start with the expected base folder, such as /var/www/images) so add ../../../etc/passwd 
6. ../../../etc/passwd%00.png 
(If an application requires that the user-supplied filename must end with an expected file extension, such as .png)

*encoding* is the process of putting a sequence of characters (letters, numbers, punctuation, and certain symbols) into a specialized format for efficient transmission or storage. Decoding is the opposite proces
The hexadecimal encoding of ../ represents %2E%2E%2F
Then encoding the % represents %25
Double encoding of ../ represents %252E%252E%252F

*Decoding some user input twice using the same decoding scheme, once before a security measure and once afterwards, may allow double encoding attacks to bypass that security measure. Thus, to prevent double encoding attacks, all decoding operations on user input should occur before authorization schemes and security filters that intercept user input.*

### How to prevent a directory traversal attack

+ avoid passing user-supplied input to filesystem APIs altogether.
- If it is considered unavoidable to pass user-supplied input to filesystem APIs, then two layers of defense should be used together to prevent attacks:
1. The application should validate the user input before processing it. Ideally, the validation should compare against a whitelist of permitted values. If that isn't possible for the required functionality, then the validation should verify that the input contains only permitted content, such as purely alphanumeric characters.
2. After validating the supplied input, the application should append the input to the base directory and use a platform filesystem API to canonicalize the path. It should verify that the canonicalized path starts with the expected base directory.
*canonicalize- converting data that has more than one possible representation into a "standard" form*