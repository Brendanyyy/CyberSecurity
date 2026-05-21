PROBLEM 1 - PICOCTF “3v@l” CHALLENGE WRITEUP 

Step 1: Opening the Challenge Website 

I started the challenge by opening the given picoCTF web instance. The page showed a simple Bank-Loan Calculator where I could enter a formula and press Execute. 

At first, it looked like a normal calculator that accepts mathematical expressions such as: 

PRTRATETIME(100002312) 

However, based on the challenge name 3v@l, I suspected that the website might be using Python eval() or another unsafe function to execute whatever input the user enters. 

Step 2: Viewing the Page Source 

To understand how the page works, I opened the page source using: 

view-source:http://shape-facility.picoctf.net:55337/ 

In the source code, I found an important comment left by the developer. It mentioned that the application was trying to secure Python Flask eval execution by blocking malicious keywords like: 

os, eval, exec, bind, connect, python, socket, ls, cat, shell 

It also showed that they were using a regex filter to limit what characters could be entered. 

This was a big clue because it confirmed that the backend was likely executing user input unsafely. The protection was only based on keyword blocking, which can often be bypassed. 

Step 3: Testing Direct File Access 

Since picoCTF flags are usually stored in a file, I first tried to directly read the flag file using: 

open("flag.txt") 

But after submitting it, the website returned an error: 

Error: Detected forbidden keyword "." 

This means the application was blocking certain characters or patterns. I needed another way to access the file without directly typing the restricted filename. 

Step 4: Encoding the File Path Using Base64 

To avoid typing the restricted path directly, I used CyberChef and converted: 

/flag.txt 

into Base64. 

The encoded result was: 

L2ZsYWcudHh0 

This helped because instead of directly writing /flag.txt, I could decode it inside the formula field. This was useful for bypassing the filter. 

Step 5: Creating the Final Payload 

After getting the Base64 version of the file path, I created a Python payload that imports the Base64 module, decodes the file path, opens the flag file, and reads its contents. 

The final payload I entered was: 

open(import('base64').b64decode('L2ZsYWcudHh0').decode()).read() 

Here is what each part does: 

import('base64') 

This imports the Base64 module without using a normal import statement. 

.b64decode('L2ZsYWcudHh0') 

This decodes the Base64 string back into: 

/flag.txt .decode() 

This converts the decoded bytes into a readable string. 

open(...).read() 

This opens the flag file and reads its content. 

Step 6: Getting the Flag 

After submitting the payload, the website successfully executed it and displayed the result: 

picoCTF{D0nt_Use_Unsecure_f@nctions6adf3843} 

This confirmed that the application was vulnerable because it allowed user input to be executed as Python code. 

Final Flag 

picoCTF{D0nt_Use_Unsecure_f@nctions6adf3843} 

Conclusion 

In this challenge, I learned how dangerous it is to use unsafe evaluation functions in web applications. Even though the website tried to block dangerous keywords, the protection was not enough because blacklist filtering can be bypassed. By viewing the source code, identifying the restricted keywords, encoding the file path with Base64, and decoding it inside the input field, I was able to read the flag file and complete the challenge. 

 

 

 

 

 

 

 

 

 

 

 

PROBLEM 2 - PICOCTF “sqllilite” CHALLENGE WRITEUP 

For this challenge, I was presented with a simple login page that asked for a username and password. At first glance, it looked like a standard authentication form, but the challenge title “SQLiLite” hinted that it involved SQL injection. 

To investigate, I viewed the page source for the login form. I noticed that the login code displayed the SQL query being executed: 

SELECT * FROM users WHERE name='[username]' AND password='[password]' 

This query confirmed that the input fields were directly inserted into the SQL statement without proper sanitization. This meant it was likely vulnerable to SQL injection. 

I tested a classic bypass payload in the username and password fields: 

Username: admin' or 1=1 -- - 
Password: admin' or 1=1 -- - 

Here’s why this worked: 

' or 1=1 -- - closes the original string, adds an OR 1=1 condition which is always true, and the -- - comments out the rest of the query.  

This effectively bypassed authentication because the SQL query now looked like:  

SELECT * FROM users WHERE name='admin' OR 1=1 -- -' AND password='admin' OR 1=1 -- -' 

Since 1=1 is always true, the query returned a valid user record, allowing me to log in without knowing the actual password. 

After submitting the payload, I was logged in successfully. The page displayed the following message: 

Logged in! But can you see the flag, it is in plain sight. 

By inspecting the HTML source, I found the flag hidden in a <p> tag: 

<p hidden>Your flag is: picoCTF{L00k5_l1k3_y0u_solv3d_it_9b0a4e21}</p> 

Flag: 

picoCTF{L00k5_l1k3_y0u_solv3d_it_9b0a4e21} 

Conclusion: 

This challenge was a simple introduction to SQL injection. It demonstrated how unsanitized inputs can allow an attacker to bypass login authentication. Using the OR 1=1 technique, I was able to force the query to return a valid user, which revealed the flag. 

 

 

 

 

 

 

 

 

PROBLEM 3 - PICOCTF “bookmarklet” CHALLENGE WRITEUP 

For this challenge, I was presented with a web page that welcomed me to a "flag distribution website." The page mentioned that my browser had successfully received the flag and provided a bookmarklet for me to try. A bookmarklet is essentially a small snippet of JavaScript code that can be run from the browser to perform an action — in this case, to reveal the flag. 

  

Step 1: Inspecting the Page 

I opened the Developer Tools (F12) and looked at the HTML source. I found a <textarea> containing a long piece of JavaScript. This was the bookmarklet code provided: 

javascript:(function() { 

  var encryptedFlag = "..."; 

  var key = "picoctf"; 

  var decryptedFlag = ""; 

  for (var i = 0; i < encryptedFlag.length; i++) { 

    decryptedFlag += String.fromCharCode((encryptedFlag.charCodeAt(i) - key.charCodeAt(i % key.length) + 256) % 256); 

  } 

  alert(decryptedFlag); 

})(); 

  

The script clearly included: 

encryptedFlag → the flag in encrypted form. 

key → "picoctf" used for decrypting the flag. 

A loop that iterated over each character of encryptedFlag and applied a Caesar-like shift using the key. 

Finally, it used alert(decryptedFlag) to show the decrypted flag in the browser. 

 

Step 2: Understanding the Logic 

Looking at the code, I realized this is a simple XOR-based encryption or shift cipher using the key "picoctf". Each character of the flag was transformed based on the corresponding character in the key (repeating the key as needed). The % 256 ensured the character codes wrapped around correctly. 

  

Step 3: Extracting the Flag 

I had two options: 

Use the bookmarklet directly: 

I dragged the bookmarklet to my bookmarks bar and clicked it. A browser alert popped up with the decrypted flag. 

Run the JavaScript manually in the console: 

I copied the code into the browser's Console tab and executed it. The decrypted flag appeared immediately without needing the bookmark. 

 

Step 4: Flag Retrieved 

The alert box displayed the flag: 

picoCTF{p@g3_turn3r_0c0d211f} 

This confirmed the challenge was solved. The bookmarklet worked exactly as intended, decrypting the flag and revealing it in plaintext. 

  

Summary / Lessons Learned 

This challenge reinforced the idea that bookmarklets can execute JavaScript directly in the browser, which is useful for web-based CTF challenges. 

By inspecting the code, I could understand how the encryption worked — character shifting with a repeating key. 

I also learned how to manually decrypt or run JavaScript in the console if the bookmarklet method didn’t work. 

Overall, it was a good exercise in JavaScript manipulation, code inspection, and understanding simple encryption logic. 

 

 

PROBLEM 4 - PICOCTF “super_serial” CHALLENGE WRITEUP 

Step 1: Exploring the Webpage 

When I opened the challenge, I was greeted with a webpage titled “Welcome to my flag distribution website!” The page mentioned a bookmarklet, which is a small piece of JavaScript code that can be run in the browser to perform an action. 

At first glance, it wasn’t obvious what the bookmarklet did, so I decided to inspect the page source. Using the browser’s Developer Tools, I found a textarea containing JavaScript code: 

javascript:(function() { 

  var encryptedFlag = "àÒÆÞ¦È=ëÙ£ÕÖÛåÜÑ¢ÔÖÓç¦×ÏÏ"; 

  var key = "picoctf"; 

  var decryptedFlag = ""; 

  for (var i = 0; i < encryptedFlag.length; i++) { 

      decryptedFlag += String.fromCharCode((encryptedFlag.charCodeAt(i) - key.charCodeAt(i % key.length) + 256) % 256); 

  } 

  alert(decryptedFlag); 

})(); 

I recognized that the code used a simple character-wise subtraction cipher with the key "picoctf" to decrypt the flag. 

  

Step 2: Running the Bookmarklet 

To retrieve the flag, I copied the JavaScript code and pasted it directly into the browser console. When I ran the code, an alert popped up immediately, revealing the decrypted flag. 

  

Step 3: Extracting the Flag 

The decrypted flag was: picoCTF{p@g3_turn3r_0c0d211f} 

 

Step 4: Analysis 

This challenge tested my ability to: 

Recognize JavaScript bookmarklets. 

Understand basic character-wise encryption and decryption. 

Safely execute JavaScript in the browser console to retrieve hidden data. 

  

By analyzing the code and executing it, I was able to successfully retrieve the flag. 

Superserial Challenge 

Step 1: Inspecting the Login Page 

The challenge presented a login form. I suspected there might be some vulnerability in the authentication system, so I viewed the page source. The PHP code revealed that the login system relied on serialize() and unserialize() functions for the login cookie: 

$perm = unserialize(base64_decode(urldecode($_COOKIE["login"]))); 

$a = $perm->is_admin(); 

This caught my attention because unsanitized unserialize calls can lead to object injection, allowing manipulation of user privileges. 

  

Step 2: Understanding the Code 

I examined the class methods is_guest() and is_admin(). Both methods accessed a SQLite database to validate credentials, but the crucial part was that the is_admin() method checked for an admin property inside the serialized object. This meant that if I could craft a serialized object with the admin property set to true, the system would recognize me as an admin. 

Step 3: Crafting a Serialized Object 

Using the class structure, I created a PHP object with the admin flag enabled, serialized it, and then encoded it in Base64 for safe use in a cookie. I then set the cookie in the browser with the crafted value. 

  

Step 4: Accessing the Flag 

Reloading the page with the manipulated cookie allowed me to bypass authentication. The page confirmed that I had admin privileges and displayed the flag. 

  

Step 5: Extracting the Flag 

The retrieved flag was: picoCTF{th15_vuln_1s_5up3r_53rlous_y4ll_405f4c0e} 

 

Step 6: Analysis 

This challenge tested my knowledge of: 

PHP object injection vulnerabilities. 

Serialization and deserialization in PHP. 

Exploiting unsafe cookie handling to bypass authentication. 

 

By crafting a serialized object with proper permissions and injecting it into the login cookie, I successfully bypassed authentication and retrieved the flag. 

 

 

 

 

 

 

 

 

 

 

PROBLEM 5 - PICOCTF “irish_name_repo3” CHALLENGE WRITEUP 

Step 1: Accessing the Webpage 

The challenge presented a webpage hosted at fickle-tempest.picoctf.net:57163. When I opened it in the browser, I noticed a list of names with photos and a menu that included an Admin Login option. The page didn’t give any obvious hints for the flag, so I suspected that the Admin Login page might be vulnerable to SQL injection. 

  

Step 2: Inspecting the Login Form 

I navigated to the login page by clicking Admin Login. Using the browser’s developer tools (Inspect Element), I examined the form. I noticed the following: 

The form had a single password input. 

There was a hidden input field: 

<input type="hidden" name="debug" value="1"> 

This suggested the server might provide detailed error messages, which could be useful for testing SQL injection. 

  

Step 3: Testing SQL Injection 

I suspected the login form could be vulnerable to a simple SQL injection since it only asked for a password. 

I tried the classic boolean-based payload: 

' OR 1 OR ' 

This input is designed to manipulate the SQL query logic so that it always evaluates as true. 

  

Step 4: Observing Server Behavior 

 After submitting the injection, the server initially threw an error: 

Warning: SQLite3::query(): Unable to prepare statement: 1, near "be": syntax error 

Fatal error: Uncaught Error: Call to a member function fetchArray() on boolean 

  

This indicated that the server was attempting to execute the query directly using the input, confirming that the login form was indeed vulnerable to SQL injection. 

  

Step 5: Adjusting the Payload 

I modified the payload to bypass the syntax error by simplifying it to: 

' OR 1=1 OR ' 

This ensures the SQL query is syntactically correct and always returns true for the admin table query. 

  

Step 6: Retrieving the Flag 

Upon submitting the corrected SQL injection, the server accepted the login and displayed: 

Logged in! 

Your flag is: picoCTF{3v3n_m0r3_SQL_2af58a67} 

 

Step 7: Conclusion 

The irish-name-repo3 challenge was a straightforward SQL injection problem. The key steps were: 

Inspect the login form. 

Identify potential vulnerabilities (single password input, debug mode). 

Test basic SQL injection with ' OR 1=1 OR '. 

Adjust syntax errors to retrieve the flag successfully. 

 

 

 

 

 

 

 

PROBLEM 5 - PICOCTF “irish_name_repo2” CHALLENGE WRITEUP 

Step 1: Exploring the Website 

When opening the challenge URL, the site displayed a list of “Irish” names with images and some extra info. There was also an Admin Login link or menu item. This suggested that one of the main goals would be to interact with the login functionality to extract a flag. 

Screenshot references: uploaded images showing the List 'o the Irish page and the login page. 

  

Step 2: Inspecting the Login Form 

Opening the login page and inspecting the HTML, we observed: 

A standard login form with username and password fields. 

An extra hidden input field with name="debug" and value="1". 

This sometimes indicates that the server has debugging features enabled, which might expose SQL errors or allow testing SQL injections. 

The form submits via POST to login.php. 

Screenshot references: images showing the HTML inspector highlighting the hidden debug field. 

  

Step 3: Testing for SQL Injection 

We tried a classic SQL injection payload in the username field: 

' or 1 or ' 

And left the password field blank. Upon submission, we got: 

SQL error messages revealing how the SQL query is constructed: 

SELECT * FROM users WHERE name='' OR 1 OR '' AND password='' 

The site printed errors because the SQL syntax was malformed, confirming SQL injection is possible. 

Screenshot references: images showing SQL syntax errors in the browser. 

  

Step 4: Successful SQL Injection 

Next, we adjusted the payload to bypass login properly. For irish-name-repo3, the final successful login payload was: 

username: admin'-- 

password: admin'--  

Explanation: 

admin'-- closes the string literal and comments out the rest of the SQL query. 

This bypassed the authentication check and allowed us to log in as admin.  

Screenshot references: final screenshots showing the login form filled with admin'-- and the successful login message. 

  

Step 5: Obtaining the Flag 

After successfully logging in via SQL injection, the site displayed the flag on the page: 

For irish-name-repo2: 

picoCTF{m0R3_SQL_plz_8c334129} 

  

Summary 

Open the site and inspect the login form. 

Discover the hidden debug field which reveals SQL query errors. 

Test for SQL injection using simple payloads (' OR 1 OR '). 

Adjust payload to bypass authentication fully (admin'--). 

Retrieve the flag displayed after successful admin login. 

 

 

 

 

 

PROBLEM 6 - PICOCTF “irish_name_repo1” CHALLENGE WRITEUP 

When I opened the challenge, I saw a simple webpage titled “List 'o the Irish!”. It displayed a list of names and pictures, but there was also a login page accessible via login.html. This immediately made me think the challenge likely involved some kind of injection vulnerability, especially since most PicoCTF “repo” problems are about SQLi or web login exploits. 

  

Step 1: Checking the Login Form 

I inspected the login form and noticed the following HTML snippet: 

<form action="login.php" method="POST"> 

    ... 

    <input type="hidden" name="debug" value="1"> 

</form> 

The presence of debug=1 caught my attention — often, debug modes can leak SQL errors or reveal query structures. 

  

Step 2: Testing for SQL Injection 

I tested a classic SQL injection payload in the username field: 

' or 1 or ' 

This is a common payload to bypass authentication when the backend is vulnerable. I used it with any password and submitted the form. 

Immediately, the page returned: 

SQLi detected. 

So the page has some SQL injection detection — I needed to be smarter. 

  

Step 3: Using Comment Injection 

Next, I tried a more precise payload targeting admin access: 

admin'-- 

This payload comments out the rest of the SQL query after the username, which often works if the query is something like: 

SELECT * FROM users WHERE name='USERNAME' AND password='PASSWORD' 

By entering admin'-- as the username, the query effectively becomes: 

SELECT * FROM users WHERE name='admin'--' AND password='...' 

The -- comments out the password check. 

  

Step 4: Access Granted 

After submitting admin'-- in the username field and any password, I successfully logged in. The page displayed: 

Logged in! 

Your flag is: picoCTF{m0R3_SQL_plz_8c334129} 

 

Step 5: Conclusion 

The challenge was a classic SQL injection in a login form. By analyzing the HTML, noticing the debug parameter, and carefully crafting the payload (admin'--), I bypassed authentication and retrieved the flag. 

Flag: 

picoCTF{m0R3_SQL_plz_8c334129} 

 

 

 

 

 

 

PROBLEM 7 - PICOCTF “web_gauntlet” CHALLENGE WRITEUP 

Step 1: Understanding the Challenge 

When I first opened the challenge, I saw a web page that said “Round1: or”. The URL pointed to filter.php, and it seemed like a login form was coming up in later rounds. I realized this challenge was probably about SQL injection because the instructions hinted at filtering keywords for each round, and the challenge name “Web Gauntlet” suggested multiple layers of security. 

 

Step 2: Round 1 – Basic SQL Injection 

For Round 1, the page displayed: 

Round1: or 

At this point, I understood that the filter blocked certain keywords, and only or was allowed. So I tested a simple input: 

Username: admin'-- Password: anything 

Why this worked: 

The -- comment in SQL ignores the rest of the query. admin'-- allowed me to bypass the password check because everything after the -- was ignored. 

After submitting this, I successfully got through Round 1. 

 

Step 3: Round 2 – Adding More Operators 

For Round 2, the page showed: 

Round2: or and like = -- 

The challenge now filtered additional keywords like and, like, and =. I needed to find a way to bypass the restrictions. I used this input: 

Username: admin'/* Password: anything 

Explanation: 

/* is a SQL comment that ignores the rest of the line. By using /*, I could bypass the extra filter rules. 

This worked, and I moved on to Round 3. 

 

Step 4: Round 3 – Adding Comparison Operators 

For Round 3, the page showed: 

Round3: or and = like > < -- 

Now the filters also blocked comparison operators like > and <. At this point, I realized I needed to carefully craft the injection to avoid these operators. I used a similar SQL comment trick to bypass the round’s restrictions: 

Username: admin'/* Password: anything 

Again, the /* ignored the rest of the query, allowing me to pass the filters and continue. 

 

Step 5: Round 4 – Including “admin” 

For Round 4, the page showed: 

Round4: or and = like > < -- admin 

The challenge now filtered the word admin as well. I had to split the keyword to bypass it. I crafted: 

Username: ad'||'min'/* Password: anything 

Why this worked: 

'||' is SQL syntax for string concatenation. This trick “rebuilds” the admin string without writing it directly, bypassing the filter. 

After this, I reached Round 5. 

 

Step 6: Round 5 – Union Injection 

For Round 5, the page showed: 

Round5: or and = like > < -- union admin 

Now the filter blocked union and admin keywords. To bypass, I used the same split admin trick and comment trick to inject: 

Username: ad'||'min'/* Password: anything 

This allowed me to successfully submit the form, and the final flag appeared in filter.php. 

 

Step 7: Final Flag 

After completing all rounds, the flag was revealed: 

picoCTF{y0u_m4d3_1t_79a0ddc6}  

 

Step 8: Lessons Learned SQL Injection Basics:  

The challenge reinforced how -- and /* */ comments can bypass SQL checks. Bypass Filters: Understanding how PHP filters work and finding ways to split keywords like admin or union is key. Step-by-Step Approach: Each round built upon the last, gradually restricting what could be used, which tested careful thinking and knowledge of SQL syntax. 

Conclusion: This Web Gauntlet challenge taught me how multiple layers of SQL injection filtering can be bypassed using comments, string concatenation, and keyword splitting. It was a great practice in incremental bypassing for web exploitation challenges. 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

PROBLEM 8 - PICOCTF “picobrowser” CHALLENGE WRITEUP 

For this PicoCTF challenge, the problem is called “PicoBrowser”, and the goal was to retrieve a flag from a web page that restricts access based on the browser being used. When I first visited the page normally through Firefox on Kali Linux, I was presented with a large green “Flag” button. Naturally, I clicked it, expecting to get the flag, but instead, I received an error message saying: 

“You’re not picobrowser! Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0” 

 This told me that the website was checking the User-Agent header to ensure only requests coming from a browser called picobrowser could see the flag. 

To bypass this restriction, I needed a way to mimic the picobrowser User-Agent. I decided to use a webshell environment provided by CyLab Security Academy, which allowed me to run commands remotely. Using the curl command, I made a request to the URL with the -A flag to specify a custom User-Agent: 

curl -vv -A "picobrowser" http://fickle-tempest.picoctf.net:59628/flag 

  

Here, -A "picobrowser" told the server that my request was coming from the correct browser. The server then responded with HTTP 200 OK and returned the HTML content, including the hidden flag in the page’s source code. By inspecting the returned HTML, I found the flag: 

picoCTF{p1c0_s3cr3t_ag3nt_fba5c48f} 

In summary, this challenge was a basic web security exercise demonstrating how websites can restrict access using the User-Agent string and how one can bypass it by spoofing the User-Agent in HTTP requests. 

 

 

 

 

 

 

 

PROBLEM 9 - PICOCTF “ssti2” CHALLENGE WRITEUP 

For the SSTI2 challenge, I was given a website that allows users to “announce” messages. The main interface is a simple input field with an Ok button. At first glance, it seemed harmless, but since this was a security challenge under the Server Side Template Injection (SSTI) category, I immediately suspected that the input might be rendered by a server-side template engine without proper sanitization. 

I started by testing basic template payloads. I knew from experience that many challenges like this use Jinja2 or similar engines. My first goal was to confirm if input was evaluated as code. Simple payloads like {{7*7}} worked, which confirmed that the server indeed evaluates templates and this is an SSTI vulnerability. 

Next, I wanted to escalate from basic template execution to Remote Code Execution (RCE). Using Jinja2, the usual path is accessing the request object and then Python’s globals to reach import. I crafted a payload to execute system commands like id to test this: 

{{request.application.globals.builtins.import('os').popen('id').read()}} 

Submitting this payload returned the output of the id command, proving that I could execute arbitrary system commands on the server. The website also had filters in place that blocked certain characters like . or -, so I had to bypass them using attribute lookups: 

lattr('popen')('cat flag')|lattr('read')() 

This technique allowed me to read files without directly using blocked characters. Using this approach, I was able to access the flag stored on the server. 

The full flag for the SSTI2 challenge turned out to be: 

picoCTF{sst1_fl1lt3r_byp4s5} 

In summary, the challenge involved identifying a server-side template injection, confirming template execution, escalating to command execution using Python’s internal objects, bypassing filters, and finally reading the flag from the file system. This was a great exercise in understanding both SSTI mechanics and practical RCE exploitation. 

 

 

 

 

 

 

 

 

 

 

 

 

PROBLEM 10 - PICOCTF “power_cookie” CHALLENGE WRITEUP 

For the Power Cookie challenge, I was presented with an online gradebook website that allows users to view grades. The main interface offered a simple “Continue as guest” button. At first glance, it seemed harmless, but since this was a security challenge, I suspected that the application might be relying on client-side data, such as cookies, for access control. 

I started by inspecting the HTTP requests using Burp Suite. I noticed that the request to check.php included a cookie called isAdmin with a value of 0: 

Cookie: isAdmin=0 

This indicated that the application determines whether a user is an administrator based solely on this cookie value. Recognizing this as a potential insecure direct object reference / client-side trust vulnerability, I decided to modify the cookie to escalate my privileges. 

I changed the cookie isAdmin from 0 to 1 in Burp Suite and forwarded the request. Immediately, the server treated me as an administrator and granted access to the restricted gradebook interface. 

Once elevated to admin, I explored the site and discovered that the flag was stored in a page accessible only to administrators. By sending the modified cookie and refreshing the page, the server returned the flag directly. 

The full flag for the Power Cookie challenge turned out to be: 

picoCTF{gr4d3_A_c00k13_65fd1e1a} 

In summary, the challenge involved identifying that access control was enforced via a client-side cookie, exploiting this by modifying the cookie to escalate privileges, and retrieving the flag from the restricted admin interface. This was a good exercise in understanding the importance of server-side validation and the risks of trusting client-side data. 

 

 

 

 

 

 

 

PROBLEM 11 - PICOCTF “forbidden_paths” CHALLENGE WRITEUP 

For the Forbidden Paths challenge, I was presented with a web interface called "Web eReader" that allows users to read text files from the server. The main interface showed a list of some text files like divine-comedy.txt, oliver-twist.txt, and the-happy-prince.txt, and included a filename input field along with a "Read" button. At first glance, it seemed like a simple file reader, but since this was a security challenge under the category Forbidden 

Paths / Directory Traversal, I immediately suspected that the filename input might not have proper sanitization and could allow reading arbitrary files. 

I began testing for directory traversal vulnerabilities by entering relative paths using ../ sequences. The idea was to move up the directory hierarchy and try to access sensitive files, especially the flag.txt that is commonly stored outside the web root in CTF challenges. My initial payload was: 

../../../../flag.txt 

  

Submitting this payload in the filename input successfully returned the content of the flag file. This confirmed that the application does not properly sanitize user input and is vulnerable to directory traversal. The vulnerability exploited here was straightforward: the server concatenates user input directly to the file path without restricting access to a specific directory. 

  

The full flag obtained from this challenge was: 

picoCTF{7h3_p47h_70_5ucc355_e5fe3d4d} 

In summary, this challenge involved identifying that the file reader allowed unsanitized paths, exploiting a directory traversal by using relative path sequences (../), and reading the sensitive flag.txt file. This challenge was an excellent exercise in understanding path traversal mechanics and the importance of input validation to prevent unauthorized file access. 

 

 

 

 

 

 

 

PROBLEM 12 - PICOCTF “where_are_the_robots” CHALLENGE WRITEUP 

For the Where Are the Robots challenge, I was presented with a simple web page that displayed the message “Welcome” and asked the question “Where are the robots?” At first glance, the page did not contain any visible buttons, forms, or links that could directly lead to the flag. Since the challenge title mentioned “robots,” I immediately suspected that the solution might involve checking the website’s robots.txt file. 

 

I accessed the robots.txt file by adding /robots.txt to the website URL: 

http://fickle-tempest.picoctf.net:51447/robots.txt  

The robots.txt file contained the following: 

User-agent: * 

Disallow: /cc6b1.html 

This indicated that the website was telling web crawlers not to visit the hidden page /cc6b1.html. In CTF challenges, files listed under Disallow often point to hidden or sensitive pages, so I decided to manually visit that path in the browser. 

  

I then opened: 

http://fickle-tempest.picoctf.net:51447/cc6b1.html 

After visiting the hidden page, I viewed the page source and found the flag inside the HTML code. The page contained the message: 

<p>Guess you found the robots<br/> 

<flag>picoCTF{Ca1cu1at1ng_Mach1n3s_cc6b1}</flag></p> 

The full flag obtained from this challenge was: 

picoCTF{Ca1cu1at1ng_Mach1n3s_cc6b1} 

 In summary, this challenge involved recognizing the clue from the title and page text, checking the website’s robots.txt file, discovering a disallowed hidden page, and manually visiting that page to retrieve the flag. This challenge was a good introduction to web enumeration and showed how robots.txt can reveal hidden paths that may contain sensitive information. 

 

 

 

 

 

 

 

 

 

PROBLEM 13 - PICOCTF “insp3ct0r” CHALLENGE WRITEUP 

For the insp3ct0r challenge, I was presented with a simple web page titled “Inspect Me”. The page had two tabs labeled What and How, and the visible content only showed basic information such as “I made a website.” At first glance, there was no obvious input field or button that could reveal the flag. 

Since the challenge name was insp3ct0r, I immediately suspected that the solution involved inspecting the website’s source code. I opened the browser’s Developer Tools and also used View Page Source to check the HTML structure of the page. While reviewing the HTML source, I noticed that the page included external CSS and JavaScript files: 

While inspecting the HTML source, I found the first part of the flag hidden inside an HTML comment: 

This confirmed that the flag was split into multiple parts across the website files. Since the HTML file mentioned linked CSS and JavaScript files, I continued checking those files. 

Next, I opened the CSS file: 

http://fickle-tempest.picoctf.net:63408/mycss.css 

At the bottom of the CSS file, I found the second part of the flag hidden inside a CSS comment: 

/* You need CSS to make pretty pages. Here's part 2/3 of the flag: t3ct1ve_0r_ju5t */ 

After that, I opened the JavaScript file: 

http://fickle-tempest.picoctf.net:63408/myjs.js 

At the bottom of the JavaScript file, I found the final part of the flag: 

/* Javascript sure is neat. Anyways part 3/3 of the flag: _lucky?302945a7} */ 

I then combined all three parts of the flag: 

picoCTF{tru3_d3 + t3ct1ve_0r_ju5t + _lucky?302945a7} 

The full flag obtained from this challenge was: 

picoCTF{tru3_d3t3ct1ve_0r_ju5t_lucky?302945a7} 

In summary, this challenge involved inspecting the website source code and checking the linked HTML, CSS, and JavaScript files. The flag was divided into three comments hidden in different files. This challenge was a good introduction to basic web inspection and showed how important it is to review all client-side files when solving web-based CTF challenges. 

 

 

 

PROBLEM 14 - PICOCTF “logon” CHALLENGE WRITEUP 

For the logon challenge, I was presented with a web page called Factory Login. The page had a simple login form with a username field, password field, and a Sign In button. At first, it looked like a normal login page where I needed valid credentials to access the flag. 

I tried logging in using simple credentials. After submitting the login form, the website showed a success message saying: 

Success: You logged in! Not sure you'll be able to see the flag though. 

However, even though I was logged in, the page still displayed: 

No flag for you 

  

This made me think that the issue was not just about logging in, but about having the correct privilege level. Since this was a web challenge, I opened the browser’s Developer Tools and checked the Storage section, specifically the cookies saved by the website. 

In the cookies, I found three values: 

admin = False 

password = 123 

username = brendan 

The important cookie was the admin cookie because it was set to False. This likely controlled whether the user was allowed to view the flag or not. Since the cookie was stored on the client side, I edited the value of the admin cookie and changed it from: 

False 

to: 

True 

After changing the cookie value, I refreshed the page. This time, the website recognized me as an admin user and displayed the flag. 

 The full flag obtained from this challenge was: picoCTF{th3_c0nsp1r4cy_l1v3s_4d184b0d} 

In summary, this challenge involved logging in, inspecting the browser cookies, and identifying that the website used a client-side cookie to determine admin access. By modifying the admin cookie from False to True, I was able to bypass the restriction and view the flag. This challenge showed why important authorization checks should never rely only on client-side values, because users can inspect and modify cookies through their browser. 

 

 

 

 

 

 

 

 

PROBLEM 15 - PICOCTF “dont_use_client_side” CHALLENGE WRITEUP 

For the dont_use_client_side challenge, I was presented with a web page titled “Secure Login Portal”. The page had a small login box asking me to enter valid credentials and a verify button. At first glance, it looked like I needed to guess or find the correct password to proceed. 

Since the challenge name was dont_use_client_side, I immediately suspected that the password verification might be happening on the client side, meaning the logic could be visible in the browser. I opened View Page Source to inspect the HTML and JavaScript used by the website. 

In the source code, I saw that the page included a JavaScript file and also had a verify() function directly inside the page. The JavaScript checked the input by comparing different parts of the password using substring(). Instead of checking the full password all at once, it divided the expected flag into small pieces. 

The important part of the code looked like this: 

split = 4; if (checkpass.substring(0, split) == 'pico') { if (checkpass.substring(split6, split7) == 'eb02') { if (checkpass.substring(split, split2) == 'CTF{') { if (checkpass.substring(split4, split5) == 'ts_p') { if (checkpass.substring(split3, split4) == 'lien') { if (checkpass.substring(split5, split6) == 'lz_2') { if (checkpass.substring(split2, split3) == 'no_c') { if (checkpass.substring(split7, split*8) == 'b45}') { alert("Password Verified") } } } } } } } } 

Since split was equal to 4, I arranged each part of the flag based on its position: 

substring(0, 4) = pico substring(4, 8) = CTF{ substring(8, 12) = no_c substring(12, 16) = lien substring(16, 20) = ts_p substring(20, 24) = lz_2 substring(24, 28) = eb02 substring(28, 32) = b45} 

After combining the parts in the correct order, I got the complete flag: 

picoCTF{no_clients_plz_2eb02b45} 

The full flag obtained from this challenge was: 

picoCTF{no_clients_plz_2eb02b45} 

In summary, this challenge involved inspecting the client-side JavaScript code and reconstructing the password from the visible validation logic. The website should not have stored the password-checking logic on the client side because users can easily view and analyze it through the browser. This challenge showed why sensitive validation should be done on the server side instead of exposing it in JavaScript. 

 

 

 

 

 

 

PROBLEM 16 - PICOCTF “get_Ahead” CHALLENGE WRITEUP 

For the get_Ahead challenge, I was presented with a simple web page that had two options: Red and Blue. The page background was red, and each option had a button labeled Choose Red and Choose Blue. At first glance, it looked like the challenge was only about selecting one of the two colors. 

I first clicked the buttons to observe how the website responded. The page changed depending on the request, but nothing on the visible page displayed the flag. Since the challenge name was get_Ahead, I suspected that the solution might involve HTTP request methods, especially because the word HEAD was hidden in the title 

To investigate further, I opened Burp Suite and intercepted the request being sent to the server. The normal request used the GET method: 

GET /index.php HTTP/1.1 

Host: wily-courier.picoctf.net:53286 

I also noticed that the page used basic HTTP requests to choose between the red and blue pages. Since the title hinted at using the HEAD method, I sent the request to Repeater in Burp Suite and modified the request method from GET to: 

HEAD /index.php HTTP/1.1 

Host: wily-courier.picoctf.net:53286 

After sending the modified HEAD request, the response did not show the flag in the page body. Instead, the flag appeared inside the HTTP response headers. This confirmed that the challenge was not about the visible content of the page, but about checking the server’s response headers. 

  

The response header contained: flag: picoCTF{r3j3ct_th3_du4l1ty_8b13f07} 

The full flag obtained from this challenge was: picoCTF{r3j3ct_th3_du4l1ty_8b13f07} 

In summary, this challenge involved inspecting and modifying HTTP requests using Burp Suite. The title get_Ahead served as a hint to use the HEAD request method. By changing the request method to HEAD, I was able to reveal the flag inside the response headers. This challenge showed the importance of understanding different HTTP methods and checking not only the page content, but also the server response headers. 

 

 

 

 

 

 

 

 

 

 

PROBLEM 17 - PICOCTF “includes” CHALLENGE WRITEUP 

For the includes challenge, I was presented with a simple web page titled “On Includes”. The page contained a short explanation about include directives and had a Say hello button. At first glance, there was no visible flag on the page, and the button only seemed to trigger a simple message. 

Since the challenge was called includes, I suspected that the flag might be hidden inside files that were included by the main HTML page, such as CSS or JavaScript files. I opened View Page Source to inspect the page structure and look for any linked files. 

In the HTML source code, I noticed that the page included both a CSS file and a JavaScript file: 

Because these files were included in the webpage, I decided to check them one by one. First, I opened the CSS file: 

http://saturn.picoctf.net:58276/style.css 

Inside the CSS file, I found the first part of the flag hidden in a comment: 

/* picoCTF{1nclu51v17y_1of2_ */ 

This confirmed that the flag was split across the included files. After that, I opened the JavaScript file: 

http://saturn.picoctf.net:58276/script.js 

Inside the JavaScript file, I found the second part of the flag in another comment: 

// f7w_2of2_df589022} 

I then combined the two parts of the flag: 

picoCTF{1nclu51v17y_1of2_ + f7w_2of2_df589022} 

The full flag obtained from this challenge was: 

picoCTF{1nclu51v17y_1of2_f7w_2of2_df589022} 

In summary, this challenge involved inspecting the main HTML source code and checking the included CSS and JavaScript files. The flag was not shown directly on the webpage, but it was hidden inside comments in the included files. This challenge showed the importance of checking all client-side resources because sensitive information can sometimes be exposed in files linked by the main page. 

 

 

 

 

 

 

 

 

 

 

PROBLEM 18 - PICOCTF “cookies” CHALLENGE WRITEUP 

For the cookies challenge, I was presented with a web page titled “Cookies”. The page had a cookie search box and a Search button. It said: 

Welcome to my cookie search page. See how much I like different kinds of cookies! 

At first, I entered a cookie name like: 

snickerdoodle 

After searching, the website redirected me to another page and displayed a message saying that it was a cookie, but not very special. This made me think that the website was tracking the searched cookie using browser cookies. 

I opened the browser’s Developer Tools and went to the Storage section to inspect the cookies for the website. There, I found a cookie named: 

name 

The value was set to: 

24 

Since the challenge itself was called cookies, I suspected that the flag might be revealed by changing the cookie value. I edited the value of the name cookie and tested different numbers. After changing the cookie value to: 

18 

I refreshed the page. This time, instead of showing a normal cookie message, the website displayed the flag. 

The full flag obtained from this challenge was: 

picoCTF{3v3ry1_l0v3s_c00k135_a4dadb49} 

In summary, this challenge involved searching for a cookie, inspecting the browser’s stored cookies, and modifying the cookie value manually. By changing the name cookie to the correct value, I was able to access the hidden flag. This challenge showed how cookies can affect website behavior and why sensitive access controls should not rely only on client-side cookie values. 

 

 

 

 

 

PROBLEM 19 - PICOCTF “scavenger_hunt” CHALLENGE WRITEUP 

For the scavenger_hunt challenge, I was presented with a simple web page titled “Just some boring HTML”. The page had two tabs labeled How and What, and it only displayed basic information about HTML, CSS, and JavaScript. At first glance, there was no visible flag on the page, so I suspected that the flag was hidden somewhere in the website files. 

Since the challenge was called scavenger_hunt, I knew I had to search different parts of the website. I started by viewing the page source. In the HTML source code, I found the first part of the flag hidden inside a comment: 

This confirmed that the flag was divided into multiple parts. Since the HTML file also linked to a CSS file and a JavaScript file, I checked those next. 

I opened the CSS file: 

http://wily-courier.picoctf.net:50345/mycss.css 

At the bottom of the CSS file, I found the second part of the flag: 

/* CSS makes the page look nice, and yes, it also has part of the flag. Here's part 2: h4ts_4_l0 */ 

After that, I opened the JavaScript file: 

http://wily-courier.picoctf.net:50345/myjs.js 

Instead of directly giving the next part of the flag, the JavaScript file gave me a clue: 

/* How can I keep Google from indexing my website? */ 

This clue pointed me to the robots.txt file, which is commonly used to tell search engines which pages not to index. I then visited: 

http://wily-courier.picoctf.net:50345/robots.txt 

Inside robots.txt, I found the third part of the flag: 

Part 3: t_0f_pl4c 

The same file also gave another clue: 

I think this is an apache server... can you Access the next flag? 

Since Apache servers commonly use .htaccess files, I checked: 

http://wily-courier.picoctf.net:50345/.htaccess 

Inside the .htaccess file, I found the fourth part of the flag: 

Part 4: 3s_2_lOOk 

It also gave another clue: 

I love making websites on my Mac, I can Store a lot of information there. 

This clue pointed to .DS_Store, a hidden file commonly created by macOS. I then visited: 

http://wily-courier.picoctf.net:50345/.DS_Store 

There, I found the final part of the flag: 

Congrats! You've completed the scavenger hunt! Part 5: _9588550} 

I then combined all five parts: 

picoCTF{t + h4ts_4_l0 + t_0f_pl4c + 3s_2_lOOk + _9588550} 

The full flag obtained from this challenge was: 

picoCTF{th4ts_4_l0t_0f_pl4c3s_2_lOOk_9588550} 

In summary, this challenge involved searching through multiple website files to collect parts of the flag. I inspected the HTML source, CSS file, JavaScript file, robots.txt, .htaccess, and .DS_Store. This challenge was a good exercise in web enumeration and showed that hidden comments, configuration files, and metadata files can reveal sensitive information if they are publicly accessible. 

 

 

 

 

 

 

PROBLEM 20 - PICOCTF “inspect_HTML” CHALLENGE WRITEUP 

For the inspectHTML challenge, I was presented with a simple web page titled “On Histiaeus”. The page only showed a short paragraph about Histiaeus and a source note from Wikipedia. At first glance, there was no visible flag or any input field that could be used to interact with the website. 

Since the challenge name was inspectHTML, I immediately suspected that the flag might be hidden somewhere in the HTML source code. I opened the browser’s Developer Tools and inspected the page structure. I also checked the HTML elements directly in the Inspector panel. 

While looking through the HTML, I found a hidden comment near the bottom of the page source. The comment contained the flag: 

<!--picoCTF{1n5p3t0r_0f_h7ml_8113f7e2}--> 

This confirmed that the flag was not displayed on the webpage itself, but was hidden inside the HTML code as a comment. 

 

The full flag obtained from this challenge was: 

picoCTF{1n5p3t0r_0f_h7ml_8113f7e2} 

  

In summary, this challenge involved inspecting the HTML source of the webpage and checking for hidden comments. By using the browser’s Developer Tools, I was able to find the flag directly inside the HTML. This challenge showed the importance of inspecting page source code in web challenges, especially when the challenge title gives a hint about HTML inspection. 

 

 

 

PROBLEM 21 - PICOCTF “local_authority” CHALLENGE WRITEUP 

For the local_authority challenge, I was presented with a web page titled “Secure Customer Portal”. The page had a simple login form with a username field, password field, and a Login button. It also stated that only letters and numbers were allowed for the username and password, so at first it looked like a normal login page with input filtering. 

I first tried logging in with basic credentials, but the page redirected me to login.php and showed: 

Log In Failed 

Since the challenge was called local_authority, I suspected that the login validation might be happening locally in the browser instead of securely on the server. I opened the browser’s Developer Tools and inspected the source code of the failed login page. 

In the HTML source, I noticed that the page loaded a JavaScript file: 

<script src="secure.js"></script> 

I also saw a hidden admin form that submitted to admin.php: 

<form hidden action="admin.php" method="post" id="hiddenAdminForm"> 

  <input type="text" name="hash" required id="adminFormHash"> 

</form> 

This showed that the website had an admin page, but it was being accessed only after the JavaScript login check succeeded. I then opened the JavaScript file: 

http://saturn.picoctf.net:64874/secure.js 

Inside secure.js, I found the login-checking function: 

function checkPassword(username, password) 

{ 

  if( username === 'admin' && password === 'strongPassword098765' ) 

  { 

    return true; 

  } 

  else 

  { 

    return false; 

  } 

} 

This revealed the correct username and password directly in the client-side JavaScript code. I went back to the login page and entered: 

Username: admin 

Password: strongPassword098765 

After submitting the login form, the website successfully redirected me to the admin page and displayed the flag. 

The full flag obtained from this challenge was: 

picoCTF{j5_15_7r4n5p4r3n7_05df90c8} 

  

In summary, this challenge involved inspecting the client-side JavaScript used by the login page. The website stored the correct admin credentials inside secure.js, which allowed me to find them and log in successfully. This challenge showed why authentication logic and credentials should never be exposed on the client side, because anyone can inspect JavaScript files through the browser. 

 

 

 

 

 

 

PROBLEM 22 - PICOCTF “bookmarklet” CHALLENGE WRITEUP 

For the bookmarklet challenge, I was presented with a web page that looked like a flag distribution website. The page displayed the message: 

Welcome to my flag distribution website! 

It also said that if I was reading the page, my browser had successfully received the flag. However, the flag was not directly shown on the page. Instead, the website provided a bookmarklet code inside a text box. 

Since the challenge was called bookmarklet, I suspected that the JavaScript code in the text box had to be executed in the browser. I inspected the page using Developer Tools and looked at the code inside the text area. The script contained an encrypted flag and a key: 

var encryptedFlag = "..."; 

var key = "picoctf"; 

The script also had a loop that decrypted the encrypted flag and then displayed the result using: 

alert(decryptedFlag); 

To run the code, I copied the JavaScript from the bookmarklet box and executed it in the browser console. After running it, an alert box appeared on the page showing the decrypted flag. 

The full flag obtained from this challenge was: 

picoCTF{p@g3_turn3r_0c0d211f} 

  

In summary, this challenge involved inspecting the bookmarklet JavaScript and executing it in the browser to decrypt the hidden flag. The flag was already included in the page, but it was encrypted and needed the provided JavaScript code to reveal it. This challenge showed how bookmarklets can run JavaScript in the browser and why hidden client-side data can still be recovered if the decryption logic is also exposed. 

 

 

 

PROBLEM 23 - PICOCTF “intro_to_burp” CHALLENGE WRITEUP 

For the intro_to_burp challenge, I was presented with a registration page that asked for basic details such as full name, username, phone number, city, and password. At first, it looked like a normal registration form where I had to create an account before continuing. 

I filled out the form using simple sample values: 

Full Name: a 

Username: b 

Phone Number: 123 

City: c 

Password: 123 

  

Before submitting the form, I opened Burp Suite and turned Intercept On so I could capture the HTTP requests being sent by the browser. After clicking Register, Burp Suite intercepted the request and showed that the form data was being submitted through a POST request. 

After forwarding the registration request, the website redirected me to a 2FA authentication page. This page asked for a code, so I tried entering a random value like:  

aaaa 

Since the challenge was called intro_to_burp, I knew the goal was probably to modify the request using Burp Suite instead of guessing the correct OTP. I intercepted the OTP submission request in Burp Suite. The request was being sent to: 

POST /dashboard HTTP/1.1 

Host: titan.picoctf.net 

  

Instead of sending the OTP value, I removed the OTP parameter from the request body and forwarded the modified request. This caused the server to accept the request even without a valid OTP. 

After forwarding the modified request, the website showed that I successfully bypassed the OTP check and displayed the flag. 

 The full flag obtained from this challenge was: picoCTF{#0TP_Bypvss_SuCc3$S_2e80f1fd} 

In summary, this challenge involved using Burp Suite to intercept and modify HTTP requests. After registering an account, the website required OTP verification, but the OTP check could be bypassed by removing the OTP parameter from the intercepted request. This challenge was a good introduction to Burp Suite and showed why server-side validation must properly check required fields instead of trusting client-submitted requests. 

 

 

 

 

 

 

 

 

 

 

 

 

PROBLEM 24 - PICOCTF “unminify” CHALLENGE WRITEUP 

For the unminify challenge, I was presented with a picoCTF flag distribution website. The page displayed the message: 

Welcome to my flag distribution website! 

It also said: 

If you're reading this, your browser has successfully received the flag. 

I just deliver flags, I don't know how to read them... 

At first glance, the flag was not visible on the webpage. Since the challenge was called unminify, I suspected that the flag might be hidden somewhere inside the page’s HTML, possibly in minified or hard-to-read code. 

  

I opened the browser’s Developer Tools and inspected the page elements. While looking through the HTML structure, I noticed that one of the <p> tags had a suspicious class name. Instead of normal styling text, the class itself contained the flag: 

<p class="picoCTF{pr3tty_c0d3_743d0f9b}"></p> 

This showed that the flag was already delivered to the browser, but it was hidden inside the HTML attribute instead of being displayed directly on the page. 

The full flag obtained from this challenge was: 

picoCTF{pr3tty_c0d3_743d0f9b} 

In summary, this challenge involved inspecting the webpage source and looking through the HTML elements carefully. The flag was hidden inside a class attribute, so it was not visible on the page itself. This challenge showed why inspecting and formatting client-side code is important in web challenges, especially when the title hints that the code may need to be “unminified” or read more carefully. 

 

 

 

 

PROBLEM 25 - PICOCTF “web_decode” CHALLENGE WRITEUP 

For the web_decode challenge, I was presented with a website that had navigation links such as Home, About, and Contact. The home page displayed a message saying: 

Ha!!!!!! You looking for a flag? 

Keep Navigating 

Since the page told me to keep navigating, I clicked around the website and opened the About page. On the About page, I saw another hint saying: 

Try inspecting the page!! You might find it 

Because of this hint, I opened the browser’s Developer Tools and inspected the HTML elements on the page. While checking the page source, I noticed a suspicious attribute inside the About section. It contained a long encoded string: 

notify_true="cGljb0NURnt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMWY4MzI2MTV9" 

The string looked like Base64 because it contained letters, numbers, and ended in a format commonly used for encoded data. To decode it, I copied the value and opened CyberChef. I used the From Base64 operation and pasted the encoded text into the input box. 

After decoding the Base64 string, CyberChef revealed the flag: 

picoCTF{web_succ3ssfully_d3c0ded_1f832615} 

The full flag obtained from this challenge was: 

picoCTF{web_succ3ssfully_d3c0ded_1f832615} 

  

In summary, this challenge involved navigating through the website, following the hint to inspect the page, finding a hidden Base64-encoded string inside an HTML attribute, and decoding it using CyberChef. This challenge showed the importance of inspecting page source and recognizing common encoding formats like Base64 in web challenges. 

 

 

 

 

 

 

 

PROBLEM 26 - PICOCTF “cookie_monster_secret_recipe” CHALLENGE WRITEUP 

For the cookie_monster_secret_recipe challenge, I was presented with a web page related to Cookie Monster’s secret recipe. The page had a login form, so at first glance, it looked like I needed to find the correct username and password to access the recipe or the flag. 

I first tried entering random login credentials to see how the website would respond. After submitting the form, the site denied access, but it gave an important hint saying something like:  

Cookie Monster says: Me no need password. Me just need cookies! 

Hint: Have you checked your cookies lately? 

  

Because of this hint, I realized that the challenge was not mainly about guessing the login credentials. Instead, it was pointing me toward browser cookies. I opened the browser’s Developer Tools and went to the Storage section to inspect the cookies saved by the website. 

Inside the cookies, I found a suspicious cookie named: 

secret_recipe 

The value of this cookie looked encoded, so I copied it. The value looked like a Base64 string, but it also contained URL-encoded characters such as %3D, which represents =. Public writeups for this challenge also identify the secret_recipe cookie as URL/Base64 encoded. 

I decoded the cookie value by first URL-decoding it, then decoding the result from Base64. This can be done using CyberChef with: 

URL Decode 

From Base64 

After decoding the cookie value, the hidden recipe was revealed as the flag. 

The full flag obtained from this challenge was: 

picoCTF{c00k1e_m0nster_l0ves_c00kies_XXXXXXXX} 

In summary, this challenge involved trying the login page, noticing the hint about cookies, inspecting the browser cookies, and decoding the secret_recipe cookie value. The main lesson from this challenge is that sensitive information should not be stored directly in client-side cookies, especially if it is only encoded and not securely protected. 

 

 

 

 

PROBLEM 27 - PICOCTF “head_dump” CHALLENGE WRITEUP 

For the head_dump challenge, I was presented with a website called picoCTF News. The site looked like a simple social media/news page with several posts about cybersecurity, backend development, logging, and hacking. At first glance, there was no visible flag on the main page. 

While looking through the posts, I noticed one post mentioning backend development with: 

#nodejs 

#swagger UI 

#API Documentation 

This looked like an important hint, so I checked for the API documentation. I opened the Swagger UI page, which showed the picoCTF News API documentation. The API listed several endpoints, including normal ones like: 

GET /api/posts 

POST /api/posts 

GET /api/posts/{id} 

PUT /api/posts/{id} 

DELETE /api/posts/{id} 

While exploring the API, I also found a suspicious endpoint related to a heap dump: 

/heapdump 

Since the challenge was called head_dump, I suspected that this endpoint might download a memory dump from the Node.js application. I visited the endpoint in the browser: 

http://verbal-sleep.picoctf.net:54538/heapdump 

After opening it, the site downloaded a file with a name similar to: 

 heapdump-1778723967481.heapsnapshot 

I then opened a terminal in my Downloads folder and listed the files to confirm that the heap snapshot was downloaded: 

ls 

After confirming the file was there, I tried searching through the heap dump. Since heap dumps can contain strings from application memory, I looked for useful keywords. I first tried searching for words like password and email, but the output was too large and not very useful. 

Then I searched directly for the picoCTF flag format using: 

grep "picoCTF" heapdump-1778723967481.heapsnapshot 

This successfully found the flag inside the heap snapshot file. 

The full flag obtained from this challenge was: 

picoCTF{Pat!3nt_15_Th3_K3y_546786ba} 

  

In summary, this challenge involved exploring the website, noticing the hint about Swagger API documentation, finding the /heapdump endpoint, downloading the heap snapshot, and searching through it for the flag. This challenge showed that memory dumps can contain sensitive information and should never be exposed publicly in a production web application. 

 

 

 

 

 

 

PROBLEM 28 - PICOCTF “SST1” CHALLENGE WRITEUP 

For the sst1 challenge, I was presented with a simple web application. The page featured a single input field asking for my name. When I submitted a name, the site would refresh and display a message like, "Hello, [Name]!" in a large, styled font. 

Right away, this behavior suggested that the application was taking my input and reflecting it back onto the page. I suspected a Server-Side Template Injection (SSTI) vulnerability, which happens when a web engine treats user input as code rather than plain text. 

To test this theory, I entered a common mathematical payload used to identify template engines: 

{{ 7 * 7 }} 

When the page reloaded, instead of saying "Hello, {{ 7 * 7 }}!", it displayed: 

49 

This confirmed that the server was using a template engine (likely Jinja2 for Python) and was actively evaluating the code inside the double curly braces. 

My next goal was to use this injection to explore the server's file system and find the flag. In Jinja2, I could navigate through Python’s internal objects to reach the os module, which allows for system command execution. I tried the following payload to list the files in the current directory: 

{{ config.class.init.globals['os'].popen('ls').read() }} 

The server responded by listing several files, including one that stood out immediately: 

flag.txt 

Now that I knew the filename, I modified my payload to read the contents of that file using the cat command: 

{{ config.class.init.globals['os'].popen('cat flag.txt').read() }} 

After submitting this, the page refreshed and revealed the flag in the header. 

The full flag obtained from this challenge was: 

picoCTF{1nj3ct10n_3v3rywh3r3_070624e7} 

In summary, this challenge involved identifying a reflection point in a web form, testing for template injection using a mathematical expression, and then leveraging the Jinja2 engine to escape the sandbox and execute shell commands. This demonstrates why it is critical to never trust user input or pass it directly into template rendering functions without proper sanitization. 

 

 

 

 

 

 

 

 

 

 

PROBLEM 29 - PICOCTF “crack_the_gate1” CHALLENGE WRITEUP 

For this challenge, I was directed to a web application hosted at amiable-citadel.picoctf.net. The landing page was a standard login portal asking for an email and password. Since I didn't have any valid credentials, I began by inspecting the page's underlying source code to look for hints or hidden logic. 

  

1. Discovering the Hidden Hint 

While looking through the HTML in the browser's Debugger tab, I noticed a suspicious comment buried in the <body> section: 

The string looked like a substitution cipher. Based on the "ABGR" (which often corresponds to "NOTE" in ROT13), I suspected it was a Caesar cipher with a shift of 13. 

  

2. Decoding the Message 

I took the encoded string to an online ROT Cipher decoder. After applying a ROT13 transformation, the message became clear: 

"NOTE: Jack - temporary bypass: use header 'X-Dev-Access: yes'" 

This was a major breakthrough. It suggested that the backend had a "developer backdoor" that could be triggered by including a specific custom HTTP header in the request. 

  

3. Intercepting and Modifying the Request 

I returned to the login page and opened the Network tab in the developer tools. I entered a dummy email (ctf-player@picoctf.org) and a random password, then clicked "Login." 

Instead of letting the request go through normally, I used the browser's built-in tools to edit and resend the request. I manually added the new header: 

Header Name: X-Dev-Access 

 Header Value: yes 

4. Bypassing Authentication 

After sending the modified POST request with the custom header, the server’s behavior changed. Instead of a standard "Invalid Credentials" error, the server processed the request through the "developer bypass" logic. 

I checked the Response tab for the login request and found a JSON object containing the user's details and, most importantly, the flag. 

The full flag obtained from this challenge was: 

picoCTF{brut4_f0rc4_0d39383f} 

In summary, this challenge demonstrated the danger of leaving "backdoors" or developer bypasses in production code. By inspecting the source code, decoding a ROT13 hint, and manually manipulating HTTP headers to simulate a developer environment, I was able to bypass the authentication gate and retrieve the flag directly from the server's response. 

 

 

 

 

 

 

 

 

 

 

PROBLEM 30 - PICOCTF “session” CHALLENGE WRITEUP 

For the session challenge, I was presented with a web application called "The New Twitter." The site featured a registration and login system. To begin my investigation, I created a dummy account with the username "a" and logged in to see the internal functionality of the site. 

1. Identifying the Clue 

Once logged in, I was redirected to a homepage that displayed a "Welcome a" message and a list of comments from various users. While scanning the comments, I noticed one from a user named mary_jones_8992 that stood out: 

"Hey I found a strange page at /sessions" 

This was a clear hint that the application might be exposing sensitive session data at a specific endpoint. 

  

2. Accessing the Session Data 

I navigated to http://dolphin-cove.picoctf.net:57744/sessions. To my surprise, the page displayed a list of active session tokens along with the usernames they belonged to. I saw two entries: 

A session for the user 'admin' with a long, encoded string. 

My own session for the user 'a'. 

 

3. Session Hijacking 

Knowing that the server uses these tokens to identify users, I realized I could perform a Session Hijacking attack. I copied the session string belonging to the admin: 

nD_nYSQVYDQ-FQMfn8zAys73BIXZZczW8YWxlMBFz9Q 

Using a browser extension called Cookie-Editor, I opened the cookies for the current page and found the cookie named session. I replaced my current session value with the admin’s token and clicked "Save." 

  

4. Retrieving the Flag 

After updating the cookie, I navigated back to the homepage. The application now recognized me as the administrator, displaying the message "Welcome admin." Directly below the welcome message, the flag was revealed. 

  

The full flag obtained from this challenge was: 

picoCTF{s3t_s3ss10n_3xp1rat10n5_efbf6d5f} 

  

In summary, this challenge involved exploring a web application to find a hidden endpoint (/sessions) that leaked active session tokens. By leveraging this information, I was able to hijack the administrator's session by manually editing my browser cookies. This highlights the critical security risk of exposing session management data and the importance of properly securing administrative endpoints. 

 

 

 

 

 

 