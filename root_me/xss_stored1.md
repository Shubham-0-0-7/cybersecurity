## XSS Stored 1

### what we have to do?
This is a Stored XSS challenge where we need to steal the administrator's session cookie and use it to validate the challenge.  

  - Visited the challenge URL: http://challenge01.root-me.org/
  - Found a forum application with message posting functionality
  - Identified two input fields: Title and Message
  - Status showed: Visitor
First, tested basic XSS payloads to check if user input is properly sanitized:
 
### Test Payload:
```html
<script>alert(1)</script>
```
<img width="1582" height="971" alt="image" src="https://github.com/user-attachments/assets/735111b5-e4f7-49f0-a7fc-9a56366aa62f" />

   - The XSS vulnerability was found in the Message field (not in Title field)
   - The script executed successfully, confirming stored XSS vulnerability

### Setting Up Cookie Capture
Used Pipedream's request bin

Why Use a Request Bin?

A request bin (like Pipedream/Webhook.site) acts as an external server that captures all incoming HTTP requests. We use it because:  
It provides a unique URL to receive stolen data  
Allows us to see the administrator's cookies when they visit our malicious script  
Acts as a simple logging server for the attack  

Setting Up Your Own Request Bin

For Pipedream:

    Go to https://pipedream.com
    Click "New" → "HTTP API"
    Choose "Request Bin"
    You'll get a unique URL like: https://your-unique-id.m.pipedream.net

For Webhook.site:

    Go to https://webhook.site
    You'll instantly get a unique URL
    Keep the tab open to monitor incoming requests

Important: Each person gets their own unique URL. Replace https://eoz2mug7ra0f97e.m.pipedream.net with your personal request bin URL in all payloads.
using payload of all things github repo to find the payload for grabbing data 

<img width="1582" height="971" alt="image" src="https://github.com/user-attachments/assets/00afe60b-c632-425e-852e-2f65d8fc7647" />
link to payload of all things repo : https://github.com/swisskyrepo/PayloadsAllTheThings/  
Now in the above image, we will use the first payload ... replace that link with your own request bin's link  
and then paste that payload in the message field with some title and click send ... after sometime when the admin visit and check your message, that payload will steal the admin's cookie   
<img width="1582" height="971" alt="image" src="https://github.com/user-attachments/assets/6c317c4b-172f-4372-8d1f-710b37078c60" />
paste this ADMIN_COOKIE value in the password to validate the chall
