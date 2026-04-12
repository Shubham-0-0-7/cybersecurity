initially i did directory brute forcing using feroxbuster but no file with name flag.txt was there .. so then i read the ques again
<img width="842" height="906" alt="Screenshot 2026-04-12 at 15 59 23" src="https://github.com/user-attachments/assets/edd47729-cdd5-4f49-bd8c-910400682eae" />

so there is something regarding the firmware update and reached this page .. you can see the line `<Version>1.2.2.1</Version>` is getting reflected below  
and also this is xml  
<img width="1582" height="969" alt="image" src="https://github.com/user-attachments/assets/a72a77e6-b07d-4c6d-843c-28fdb8bb3107" />

by looking at this we can see that we can exploit this using xxe injection that is `XML External Entity (XXE) injection` 
i went straight thru a repo called payload of all things and went into xxe section : https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XXE%20Injection/README.md 

and yeah here we go .. got the flag with payload  
<img width="1582" height="969" alt="image" src="https://github.com/user-attachments/assets/9af16c7e-0048-4e28-9f4a-7764e80666d1" />


