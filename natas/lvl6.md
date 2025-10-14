## Natas 6

## Solution 
when you will click "view sourcecode" ... source code will showup there you will see `<? ?>`
this is php  
PHP in HTML is enclosed in the following tags:  `<? ?>` or `<?php ?>`. Variables start with a $ sign and the content of forms sent with a POST request can be accessed with the special $_POST variable.  
Another important part for this level is that it is possible to include code from other files into a file, by using include or require.  

since we can see one thing named `includes/secret.inc`   
so visit `http://natas6.natas.labs.overthewire.org/includes/secret.inc`  

you will see a blank page ... because since it does not show us an error, something is happening, PHP code is running, which does not include any visual output   

so inspect and you will find the secret key like this:
```bash
?
$secret = "****************";
?
```

now you got the secret and then paste it in the box ... you will get password to natas 7 ... and there you go!
