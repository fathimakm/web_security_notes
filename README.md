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

*encoding* is the process of putting a sequence of characters (letters, numbers, punctuation, and certain symbols) into a specialized format for efficient transmission or storage. Decoding is the opposite process
The hexadecimal encoding of ../ represents %2E%2E%2F
Then encoding the % represents %25
Double encoding of ../ represents %252E%252E%252F

*Decoding some user input twice using the same decoding scheme, once before a security measure and once afterwards, may allow double encoding attacks to bypass that security measure. Thus, to prevent double encoding attacks, all decoding operations on user input should occur before authorization schemes and security filters that intercept user input.*

# DotDotPwn Directory Traversal Fuzzing
 tool used to detect directory traversal vulnerabilities in web applications and web servers.
 `dotdotpwn -m http -h 192.168.1.110`  -d is used to specify depth of search


### How to prevent a directory traversal attack

+ avoid passing user-supplied input to filesystem APIs altogether.
- If it is considered unavoidable to pass user-supplied input to filesystem APIs, then two layers of defense should be used together to prevent attacks:
1. The application should validate the user input before processing it. Ideally, the validation should compare against a whitelist of permitted values. If that isn't possible for the required functionality, then the validation should verify that the input contains only permitted content, such as purely alphanumeric characters.
2. After validating the supplied input, the application should append the input to the base directory and use a platform filesystem API to canonicalize the path. It should verify that the canonicalized path starts with the expected base directory.
*canonicalize- converting data that has more than one possible representation into a "standard" form*

***
# Access Control

## Burp extension for checking access control vulnerability - Autorize

__Authentication__ confirms that the user is who they say they are.
__Session management__ identifies which subsequent HTTP requests are being made by that same user.
__Access control__ determines whether the user is allowed to carry out the action that they are attempting to perform.



- check robots.txt, Check page source, Check if false condition can be turned true (in burp and using inspect in browser)
- try changing user related content like email id and check if any new response occurs. -> like role id , then try changing it and send the request
- try to change user id to another user(guid) , try changing names
- post method can be chnged to get 
- som4etimes password will be forwarded in response or masked in front end, it can be viewed in page sourse or proxy

__X-Original-URL and X-Rewrite-URL__
 - non-standard HTTP headers that can be used to override the URL in the original request

- X URL header is a non standard HTTP header thet can be used to overwrite the URl in the original request

- X-Original-URL: /doesnotexist  (in  request)
- if we get a 404 or not found in response -> thsi means application supports this non standard header
- if validation is done on front end and if the resourse in request gets overwritten -> bypassing access control possible

`GET / HTTP/2`
`X-Original-Url: /admin`
-> in response you will get access to admin panel and can find carlos, but you can't view the changes in front end bcz of validation. so all the confirmation done from repeater

`GET /?username=carlos HTTP/2`
`X-Original-Url: /admin/delete`
-> since parameters are not accepted in X URL header it will be appended fron the path 


__Method-based access control__
post methd can be changed to another llike get


### Vertical privilege escalation
If a user can gain access to functionality that they are not permitted to access then this is vertical privilege escalation.

### Horizontal privilege escalation
Horizontal privilege escalation occurs if a user is able to gain access to resources belonging to another user, instead of their own resources of that type.


### IDOR Insecure direct object references
1. subcategory of access control vulnerabilities
2. if an application uses user-supplied input to access objects directly and an attacker can modify the input to obtain unauthorized access

`GET /download-transcript/2.txt HTTP/2` 
`GET /download-transcript/1.txt HTTP/2` we can access object 1 and retrieve information


3. IDOR vulnerabilities are most commonly associated with horizontal privilege escalation, but they can also arise in relation to vertical privilege escalation.

- globally unique identifiers (GUIDs)

#### multi-step processes
implementing important functions over a series of steps when -> variety of inputs or options need to be captured.-> user needs to review and confirm details before the action is performed.

if there is only validation on initial steps and not last step, requests can be manipulated - the last request

#### Referer-based access control
if referer can be manipulated and application depends on referer for validation

#### Location-based access control
 websites enforce access controls based on the user's geographical location. 
 ->  by the use of web proxies, VPNs, or manipulation of client-side geolocation mechanisms this can be broken

#### Unprotected functionality
when an application does not enforce any protection for  sensitive functionality.
__security by obscurity__
sensitive functionality is concealed by giving it a less predictable URL

## prevention
1. security checks for all request
2. except for public resources, deny access by default
3.  access control checks on client side and server side




# Server-side request forgery (SSRF)
 allows an attacker to cause the server-side application to make requests to an unintended location.




# Information Disclosure
aka information leakage
__when a website unintentionally reveals sensitive information to its users.__

- Data about other users, such as usernames or financial information
- Sensitive commercial or business data
- Technical details about the website and its infrastructure

*Some basic examples of information disclosure are as follows:*

1. Revealing the names of hidden directories, their structure, and their contents via a robots.txt file or directory listing
2. Providing access to source code files via temporary backups
3. Explicitly mentioning database table or column names in error messages
4. Unnecessarily exposing highly sensitive information, such as credit card details
Hard-coding API keys, IP addresses, database credentials, and so on in the source code
5. Hinting at the existence or absence of resources, usernames, and so on via subtle differences in application behavior

__How to test for information disclosure vulnerabilities__
*Fuzzing*
- Burp Intruder can be used for the same
1. Add payload positions to parameters and use pre-built wordlists of fuzz strings to test a high volume of different inputs in quick succession.
2. identify differences in responses by comparing HTTP status codes, response times, lengths, and so on.
3. Use grep matching rules to quickly identify occurrences of keywords, such as error, invalid, SELECT, SQL, and so on
4. grep extraction rules to extract and compare the content of interesting items within responses.

+  Logger++ extension can also be used

 Used to define advanced filters for highlighting interesting entries. This is just one of the many Burp extensions that can help you find any sensitive data that is leaked by the website.

 *Burp Scanner* - used by burpsuite professionals
 This provides live scanning features for auditing items while you browse, or you can schedule automated scans to crawl and audit the target site on your behalf. Both approaches will automatically flag many information disclosure vulnerabilities for you.


 Burp Scanner will alert you if it finds sensitive information such as private keys, email addresses, and credit card numbers in a response. It will also identify any backup files, directory listings, and so on.


 *Burp's engagement tools*
 1. Search - to look for any expression within the selected item
  It can fine-tune the results using various advanced search options, such as regex search or negative search.
  useful for quickly finding occurrences (or absences) of specific keywords of interest.

  2. Find comments
  can extract any developer comments found in the selected item. It also provides tabs to instantly access the HTTP request/response cycle in which each comment was found.

  3. Discover content
  used to identify additional content and functionality that is not linked from the website's visible content. This can be useful for finding additional directories and files that won't necessarily appear in the site map automatically.

  *Engineering informative responses*

  - __verbose errors__ are supplied with the name of the unhandled exception and a stack trace showing where the error occurred in the first place, usually accompanied by a line number and file name.

  studying the way error messages change according to your input is useful to manipulate the website to extract arbitrary data via an error message.

   For example, submitting an invalid parameter value might lead to a stack trace or debug response that contains interesting details. You can sometimes cause error messages to disclose the value of your desired data in the response.

   __Common sources of information disclosure__

   A web crawler, spider, or search engine bot downloads and indexes content from all over the Internet. The goal of such a bot is to learn what (almost) every webpage on the web is about, so that the information can be retrieved when it's needed.
   example: Amazonbot is an Amazon web crawler for web content identification and backlink discovery.
   Bingbot for Bing search engine by Microsoft.

1. Files for web crawlers
   Many websites provide files at /robots.txt and /sitemap.xml to help crawlers navigate their site. Among other things, these files often list specific directories that the crawlers should skip, for example, because they may contain sensitive information.

2. Directory listings
3. Developer comments
4. Error Messages