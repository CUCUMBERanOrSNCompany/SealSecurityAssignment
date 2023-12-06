# SealSecurityAssignment
 Researching on the vulnrability CVE-2023-26136
# Research on Vulnerability: CVE-2023-26136

# Description:
Versions of the package tough-cookie before 4.1.3 are vulnerable to Prototype Pollution due to improper handling of Cookies when using CookieJar in rejectPublicSuffixes=false mode. This issue arises from the manner in which the objects are initialized.

https://nvd.nist.gov/vuln/detail/CVE-2023-26136

# Introduction:

# What is Prototype Pollution?
JavaScript has the concept of objects, which is somewhat like dictionaries. An object can hold a set of variables of different types (e.g. string, Boolean, int etc.), the variable name is a key, and its value is the value, if we continue with the analogy of dictionaries and objects. More precisely, the object is analog to the dictionary data structure itself, and each variable is analog to a key-value pair. Objects also has the keyword __proto__ which enables to add additional variables to an object via modifying its prototype. Prototype Pollution is an attack in which the attacker pollutes his object, by adding to it additional variables, via the __proto__ keyword. The below interaction with the browser's console demonstrates the attack.

<img width="255" alt="image" src="https://github.com/CUCUMBERanOrSNCompany/SealSecurityAssignment/assets/70776104/4ea7ff2e-0f50-4ae9-ad8f-180d8363f8c6">

As we can see, we have two users: admin1 and user1. admin1 object has a Boolean variable: "isAdmin", which is set to true. User1 doesn't has this variable altogether. At line 7, we add the isAdmin variable to the user1's prototype, and even though it is evident from line 10 that the variable hasn't been added to the user1's object itself, the console responds positively, this time, when we check for the value of user1.isAdmin. That's because user1 is inheriting the properties of its prototype.

<img width="249" alt="image" src="https://github.com/CUCUMBERanOrSNCompany/SealSecurityAssignment/assets/70776104/c99f284e-13c0-401d-b58f-7809029f50dc">

It is also worth mentioning that the prototype is an object by itself, which in turn inherits properties and methods from its prototype, those creating a prototype chain. The chain ends with a null prototype, therefore a reasonable approach to prevent prototype pollution is to make the object we are working with, to explicitly inherit from a null prototype.

# What are cookies?
Cookies (often known as internet cookies) are text files with small pieces of data Ñ like a username and password Ñ that are used to identify your computer as you use a network. Specific cookies are used to identify specific users and improve their web browsing experience.

Courtesy of Kaspersky: https://www.kaspersky.com/resource-center/definitions/cookies 

# What is CookieJar?
CookieJar is an object for storing cookies.

# The Assignment:

# The vulnerability:
According to the description of the vulnerability, it arises from the way Tough-Cookie initializes Cookies. Since Cookies are objects, they are theoretically at least, be vulnerable to Prototype Pollution.

# The Risks:
By being able to temper with objects and particularly with cookies, via the object's prototype, the attacker can potentially access unauthorized data, execute remote code, cause a denial of service, hijack the session if the website relies on cookies for managing the session, and extracting sensitive data from the cookies themselves.

# The Patch:
The patch was made at the file: memstore.js. According to the issue tracking as well as to the patch introduced at version 4.1.3, in order to patch the vulnerability, we need store the cookies in a map or create the this.idx object. By creating this.idx using: this.idx = Object.create(null); Instead of this.idx = {}, we practice my suggestion on how to prevent Prototype Pollution at the introduction, by inheriting from a null prototype, and cut the prototype chain.

# Testing the vulnerability (index.js):
Snyk had published a Proof of Concept (PoC) for the discussed vulnerability. I built index.js upon it. Wrapped it in a try-catch logic to capture exceptions if they arise, added additional output to keep track of the tests progress and ended up with the required output (e.g. "EXPLOITED SUCCESSFULLY" or "EXPLOITED FAILED").
When running the command: 
npm install tough-cookie@2.5.0 && node index.js
We're getting the following output:

<img width="501" alt="image" src="https://github.com/CUCUMBERanOrSNCompany/SealSecurityAssignment/assets/70776104/6a268bdc-275e-4c34-8248-e1bef2fda700">

When running the command:
npm install ./tough-cookie-2.5.0-PATCHED.tgz && node index.js
We're getting the following output:

<img width="468" alt="image" src="https://github.com/CUCUMBERanOrSNCompany/SealSecurityAssignment/assets/70776104/5d63df2d-1d05-4659-9b7e-ca3bffccb7c9">

At both scenarios (Running with the published 2.5.0 version and running with my patched version), we managed to set up both the normal cookie as well as the exploited cookie, but at the patched version, we failed to access the exploited cookie.

# Summary:
At this assignment I learned about the Prototype Pollution attack, learned about the JavaScript object and been introduced to the Tough-Cookie package.
