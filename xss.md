 
# Cross-site scripting (xss)


* Cross-site scripting vulnerabilities normally allow an attacker to masquerade as a victim user, to carry out any actions that the user is able to perform, and to access any of the user's data. 
- If the victim user has privileged access within the application, then the attacker might be able to gain full control over all of the application's functionality and data.


 alert() 
 print()
 replace()

## How does XSS work?

Cross-site scripting works by manipulating a vulnerable web site so that it returns malicious JavaScript to users. When the malicious code executes inside a victim's browser, the attacker can fully compromise their interaction with the application

## Types of XSS attacks

1. Reflected XSS, where the malicious script comes from the current HTTP request.
2. Stored XSS, where the malicious script comes from the website's database.
3. DOM-based XSS, where the vulnerability exists in client-side code rather than server-side code.

## Uses of XSS

Impersonate or masquerade as the victim user.
Carry out any action that the user is able to perform.
Read any data that the user is able to access.
Capture the user's login credentials.
Perform virtual defacement of the web site.
Inject trojan functionality into the web site.

__Content security policy (CSP)__ is a browser mechanism that aims to mitigate the impact of cross-site scripting and some other vulnerabilities. If an application that employs CSP contains XSS-like behavior, then the CSP might hinder or prevent exploitation of the vulnerability. Often, the CSP can be circumvented to enable exploitation of the underlying vulnerability.

__Dangling markup injection__ It is used to capture data when xss cannot be fully implemented due to security measures.

 It can often be exploited to capture sensitive information that is visible to other users, including CSRF tokens that can be used to perform unauthorized actions on behalf of the user.


## How to prevent XSS attack

1. __Filter input on arrival__. At the point where user input is received, filter as strictly as possible based on what is expected or valid input.
2. __Encode data on output__. At the point where user-controllable data is output in HTTP responses, encode the output to prevent it from being interpreted as active content. Depending on the output context, this might require applying combinations of HTML, URL, JavaScript, and CSS encoding.
3. __Use appropriate response headers__. To prevent XSS in HTTP responses that aren't intended to contain any HTML or JavaScript, you can use the Content-Type and X-Content-Type-Options headers to ensure that browsers interpret the responses in the way you intend.
4. __Content Security Policy__. As a last line of defense, you can use Content Security Policy (CSP) to reduce the severity of any XSS vulnerabilities that still occur.

***
# Sink

A method or function that causes the malicious javascript to be injected to a page.

# checks

1. check if tags are working >  h1 or if we can push an alert
2. in stored, try posting a comment
3. In dom , finf where yo inputs are present using inspect, then think of how to come out of the present tag (document.write)
    * `input" onload = "alert()` we didn't close it with" bcz it will be included in ""
4. if location.search is being used, 
 selct an option Inspect >  > &storeid(the respective according to cases) 
 `&storeid=life</select><img src ="1" onerror= "alert(1)" >`
 so in here we purposely makeimg src wrong so that onerror works

5. .innerHTML
if script is not going thorugh try image tag
`<img src ="1" onerror= "alert(1)" >`

6. jquery sink

in some websites there will be a backlink, (let's say u navigate to feedback form and u wanna go back to home page, for that there will be a back button on the page, when you inspect, you see an anchor tag `a id = "backlink" href="/"`)
so we can inject javascript:alert(document.cookie) inplace of / in url
so if we inspect `a id = "backlink" href="javascript:alert(document.cookie)"`