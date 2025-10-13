## Natas 5

### Theory 
http is a stateless protocol: each request is independent and the server does not keep session state by default. to provide a continuous experience (for example, a logged-in session) servers send small pieces of data called http cookies to the client. the browser stores these cookies and sends them back with subsequent requests so the server can associate requests with a session or particular user.   
a cookie is just a key=value pair (plus optional attributes like domain, path, expires, http-only, secure). because cookies live on the client side, a user (or an attacker) can inspect and modify them unless the server uses additional protections. depending on how the server interprets the cookie value, simple changes on the client side can change the server’s behavior — for example toggling an authentication flag.  
in this level the application decides whether you are “logged in” based on a cookie value. by examining and altering that cookie you can change the perceived session state and gain access if the server relies solely on client-supplied cookie data.  

### Solution
1. open the burp suite
2. navigate to proxy tab
3. turn on the intercept and open http://natas5.natas.labs.overthewire.org/
4. right click and send it to repeater
5. in the inspector section you will find request headers section
6. in that cookie value will be loggedin=0
7. change that to zero and apply changes then send the request ... in the response section you will see the password for natas 6

<img width="1245" height="798" alt="image" src="https://github.com/user-attachments/assets/d50e6c3f-3abd-45f9-bbef-84858be1e2dc" />

