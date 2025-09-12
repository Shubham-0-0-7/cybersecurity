## Statement
Decode the string.

## Solution
we will use command line or terminal commands to encode the string  

```bash
echo "your string here" | xxd -r -p
```

### Explanation
echo just simply prints out the stirng you gave  
'|' takes the output from echo and gives to the next command -> which is xxd  
xxd is a command-line tool to create hex dumps and reverse them  
-r → reverse operation (hex → binary/text)  
-p → "plain" mode  

and there you go!!
