WEB SECURITY – 30 DAY DAILY SCHEDULE
DAILY STRUCTURE

Every Single Day

Block 1 – Web CTF Practice 45–60 min
Solve 2–3 web problems from:
picoCTF Web Exploitation

Write 1–2 sentence notes after each problem.

Block 2 – Web Lab 60–90 min
Follow the task for the day below.

Block 3 – 10 min Review
Answer this question:
What web vulnerability or attack pattern did I learn today?

Use only legal labs such as DVWA, OWASP Juice Shop, PortSwigger Labs, TryHackMe, picoCTF, and CTF platforms.

WEEK 1 – Web Fundamentals for Security Testing
Day 1

Install tools:

Burp Suite Community
Firefox or Chrome
Browser Developer Tools
OWASP Juice Shop or DVWA

Learn:

HTTP basics
Client-server communication
Request and response structure

Practice:

Visit a website
Open DevTools
Identify request headers, response headers, cookies, and status codes

Day 2

Learn:

HTTP methods
GET, POST, PUT, DELETE

Practice using Burp Suite:

Intercept a request
Modify simple parameters
Forward the request

Identify:

URL parameters
Form data
Request body

Day 3

Learn:

Status codes

Focus on:

200 OK
301 / 302 Redirect
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
500 Server Error

Practice:

Browse a test app
Identify different status codes
Write what each code means

Day 4

Learn:

Cookies and sessions

Practice:

Find session cookies
Identify authentication cookies
Observe what changes after login/logout

Questions to answer:

What cookie keeps the user logged in?
Does the cookie have Secure or HttpOnly flag?

Day 5

Learn:

HTML forms
Input fields
Hidden fields

Practice:

Inspect login forms
Inspect registration forms
Check hidden parameters

Focus:

How user input is sent to the server

Day 6

Practice web challenges from:

picoCTF
PortSwigger Academy
OWASP Juice Shop

Identify:

Target URL
Input fields
Parameters
Possible vulnerability type

Day 7 – CTF Day

Join or practice a Web CTF.

Focus only on:

Basic web challenges
Inspect source code
Cookies
Headers
Simple login bypass challenges

WEEK 2 – Common Web Vulnerabilities
Day 8

Learn:

SQL Injection basics

Practice:

Identify login forms
Identify URL parameters
Test in legal labs only

Goal:

Understand how unsanitized input can affect database queries

Day 9

Learn:

Authentication bypass

Practice:

Weak login logic
Default credentials
Simple username/password flaws

Identify:

Bad error messages
Predictable login behavior
Weak access control

Day 10

Learn:

Cross-Site Scripting XSS basics

Types:

Reflected XSS
Stored XSS
DOM-based XSS

Practice:

Find input fields
Check where user input appears on the page

Goal:

Understand how unsafe output can execute scripts

Day 11

Learn:

File upload vulnerabilities

Practice:

Upload normal files
Observe allowed file types
Check file size and extension restrictions

Identify:

Weak file validation
Dangerous upload behavior
Improper file storage

Day 12

Learn:

Directory traversal / path traversal

Practice:

Look for file-viewing features
Understand how file paths work

Goal:

Identify when a web app improperly allows access to server files

Day 13

Solve 3 full web exploitation challenges.

Time limit:

30 minutes each

For every challenge, write:

Vulnerability found
How you identified it
Final lesson learned

Day 14 – CTF Day

Join a live CTF or practice Web category only.

Focus on:

SQLi
XSS
Cookies
Authentication
Source code inspection

WEEK 3 – Web Investigation, Logs, and Access Control
Day 15

Learn:

Web server logs

Focus on:

IP address
Timestamp
HTTP method
Requested URL
Status code
User-Agent

Practice:

Analyze sample Apache or Nginx logs

Identify:

Suspicious requests
Repeated errors
Unusual user agents

Day 16

Learn:

Failed login detection

Practice:

Analyze login logs

Identify:

Repeated failed attempts
Same IP trying multiple accounts
One account targeted many times

Attack pattern:

Brute force
Credential stuffing
Password spraying

Day 17

Learn:

Access control vulnerabilities

Topics:

IDOR
Broken access control
Privilege escalation

Practice:

Check object IDs in URLs
Compare normal user vs admin access

Goal:

Understand how users access data they should not access

Day 18

Learn:

Burp Suite Repeater

Practice:

Send request to Repeater
Modify parameters
Compare server responses

Focus:

Testing safely and carefully
Observing response differences

Day 19

Learn:

API security basics

Focus on:

JSON requests
API endpoints
Tokens
Authorization headers

Practice:

Inspect API requests using Burp or DevTools

Identify:

Missing authorization
Exposed data
Weak token handling

Day 20

Analyze a mixed web case.

Given:

Web logs
Login attempts
Suspicious URL requests

Write a short 5-line summary:

Attacker IP
Target page
Attack method
Evidence found
Possible impact

Day 21 – CTF Day

Attempt Web + Logs challenges.

Focus on:

Finding clues in source code
Reading HTTP traffic
Understanding login flaws
Interpreting web logs

WEEK 4 – Advanced Web Patterns + Speed
Day 22

Learn:

Command injection basics

Practice:

Identify input fields that interact with system commands in labs

Focus:

Understanding how unsafe server-side input can affect command execution

Day 23

Learn:

Server-Side Request Forgery SSRF basics

Focus:

How a server can be tricked into making requests

Practice:

Study SSRF labs
Identify URL-fetching features

Examples:

Image fetcher
Webhook tester
URL preview feature

Day 24

Learn:

CSRF basics

Focus:

How actions can be triggered without user intention

Practice:

Identify state-changing actions

Examples:

Change password
Update email
Transfer action
Delete account

Day 25

Full web investigation.

Answer:

Attacker IP
Victim account
Target endpoint
Attack method
Evidence from logs
Possible vulnerability

Write a short incident summary.

Day 26

Timed challenge day.

Solve:

4 web challenges under time pressure

Limit:

20–30 minutes per challenge

Focus on speed:

Check source code
Check cookies
Check parameters
Check headers
Check login behavior

Day 27

Redo your hardest web challenge from earlier weeks.

This time, document:

What confused you before
What clue you missed
How to solve it faster next time

Day 28

Simulated mini web breach investigation.

Scenario:

A website was attacked.
You have logs, suspicious requests, and possible compromised users.

Write a 1-page report:

Summary
Timeline
Evidence
Attack method
Affected account or page
Recommendation

Day 29 – Final CTF Prep

Solve 5 fast web challenges.

Target topics:

SQL Injection
XSS
Cookies
Authentication bypass
IDOR
Source code inspection

Goal:

Build speed and pattern recognition.

Day 30 – Final Competition Day

Join a full CTF.

Focus only on:

Web exploitation
Web forensics
Logs
Authentication flaws
API issues