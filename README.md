# Summary
 * [Exercises - picoCTF](#exercises---picoctf)
 * [Another materials and references](#another-materials-and-references)

# Exercises - picoCTF

SQLiLite - https://play.picoctf.org/practice/challenge/304?category=1&page=1&search=sql

More SQLi - https://play.picoctf.org/practice/challenge/358?category=1&page=1&search=sql

## SQLiLite

### Description

Can you login to this website? Try to login here.

<details> 
  <summary><h3>Solution</h3></summary>
  When trying to login with any credentials, the site will show the SQL query used to validate the information. It looks like this:
  
  ```sql
  SELECT * FROM users WHERE name='nametest' AND password='passwordtest'
  ```
  
  Knowing the query, we only need to inject a condition that is valid every time and ignore the rest of the code. For this, we type the username: ' OR 1=1 --
  
  ```sql
  SELECT * FROM users WHERE name='' OR 1=1 --' AND password='passwordtest'
  ```
  
  Once logged in, we inspect the source code of the page to find the flag:
  
  ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/71fe26d3-4b32-4e36-8b29-642e600a346d/2ae02d6c-fdda-4c0a-8c95-da35e71fccde/image.png)

</details>

## More SQLi

### Description

Can you find the flag on this website. Try to find the flag here.

<details> 
  <summary><h3>Solution</h3></summary>

  When trying to login with any credentials, the site will show the SQL query used to validate the information. It looks like this:
  
  ```sql
  SQL query: SELECT id FROM users WHERE password = 'passwordtest' AND
   username = 'nametest'
  ```
  
  Knowing the query, we only need to inject a condition that is valid every time and ignore the rest of the code. For this, we type the password: ' OR 1=1 --
  
  ```sql
  SQL query: SELECT id FROM users WHERE password = '' OR 1=1 
  -- 'AND username = 'nametest'
  ```
  
  Once logged in, we have access to a list of offices, displaying their cities, addresses and phone numbers. There is a search bar that can be used to look for a specific office according to its city.
  
  Through that bar, we can perform another SQL injection using UNION to add another command to the query that searches for the offices by city. 
  
  The exercise’s only hint reveals that the database is SQLite, but we could discover by trying the different payloads presented in the section Database version discovery in this cheat-sheet: https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/.
  
  Knowing that we’re dealing with SQLite, we want to know what are the other tables in the database to find the flag. In order to do this, there is another clean-sheet that can help us: [https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL Injection/SQLite Injection.md](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/SQLite%20Injection.md).
  
  We’re looking for the sqlite_master table, which is a table automatically created by the system to store information about the other tables.
  
  So, we type: ' UNION SELECT 1,1,sql FROM sqlite_master -- in the search field. Note that the two 1s after the select are used to match the number of columns returned from the query.
  
  ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/71fe26d3-4b32-4e36-8b29-642e600a346d/fd0f48e5-7396-4d34-8339-7bb34ba5b45c/image.png)
  
  Looking at the result, we know that the “more_table” has a column called “flag”. To take a look, we simply need to search: ' UNION SELECT 1,flag,id FROM more_table --.
  
  ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/71fe26d3-4b32-4e36-8b29-642e600a346d/bd1202c9-2877-43f1-b9df-6e42eb422d9a/image.png)

  </details>

# Another materials and references

https://app.hackinghub.io/hubs/Interactive-sql-injection - 3 free modules to get started with SQL and practice SQLi with union attacks and blind attacks.

[https://demo.testfire.net](https://demo.testfire.net/login.jsp) - A vulnerable site simulating an online bank where it is possible to practice SQLi.

## Cybersecurity companies blog articles

https://portswigger.net/web-security/sql-injection#what-is-sql-injection-sqli 

https://www.crowdstrike.com/en-us/cybersecurity-101/cyberattacks/sql-injection-attack/

https://nordvpn.com/blog/what-is-sql-injection/

https://owasp.org/www-community/attacks/SQL_Injection

https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html

## Youtube videos

What is SQL - https://www.youtube.com/watch?v=et1n7-UxI2M

Running an SQL Injection Attack - Computerphile - https://www.youtube.com/watch?v=ciNHn38EyRc

SQL Injections are scary!! (hacking tutorial for beginners) - https://www.youtube.com/watch?v=2OPVViV-GQk&t=2s

SQL Injection Prevention: Security Simplified - https://www.youtube.com/watch?v=WONbg6ZjiXk

IT Security Tutorial - Preventing SQL injections - https://www.youtube.com/watch?v=S4qtRFsuKVY
