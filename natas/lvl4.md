## Natas 4

### Theory
The communication between the client (our machine) and the server (that hosts the website) is done by a request-response. We sent a request for a certain page and the server sends the response with the content. Generally, they will follow different protocols and structures, which depend on the service. In this case, it is HTTP (Hypertext Transfer Protocol). The request generally includes the request method (GET, POST,…), the requested URL and the protocol version. However, it can also include additional, potentially needed information, through so-called request headers. There are many such fields, some more common than others. Relevant ones for this challenge are ‘Authorization’, which would include the credentials for the website and ‘Referer’, which is the URL/webpage from which the request is sent. Generally, all this is handled by our browser and we do not have to worry about it. It is however possible to manipulate requests.  

### Solution 
1. open the burpsuite  
2. turn the intercept on  
3. then click on the "refresh page" button
<img width="668" height="212" alt="image" src="https://github.com/user-attachments/assets/a74c8b62-4173-4711-baa7-c9e0554d7685" />
4. send the request to the repeater  
5. open the repeater tab and edit the "Referer:http://natas4.natas.labs.overthewire.org/" to "Referer:http://natas5.natas.labs.overthewire.org/"
6. then send the request again ...
7. you will see that you have been authorised and you will get the password of natas 5  
