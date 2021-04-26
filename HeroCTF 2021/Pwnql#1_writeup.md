# PwnQL #1
### Category : Web
### Description : Login as admin to get the flag.
### URL : http://chall1.heroctf.fr:8080
### Solution

Going to the website gives a login page. If you look at the source code then you will see this comment `<!-- Hello dev, do not forget to remove login.php.bak before committing your code. -->`.
Navigating to `/login.php.bak` downloads the file for us.

login.php.bak
```
<?php

require_once(__DIR__  . "/config.php");

if (isset($_POST['username']) && isset($_POST['password'])) {
    $username = $_POST['username'];
    $password = $_POST['password'];

    $sql = "SELECT * FROM users WHERE username = :username AND password LIKE :password;";
    $sth = $db->prepare($sql, array(PDO::ATTR_CURSOR => PDO::CURSOR_FWDONLY));
    $sth->execute(array(':username' => $username, ':password' => $password));
    $users = $sth->fetchAll();

    if (count($users) === 1) {
        $msg = 'Welcome back admin ! Here is your flag : ' . FLAG;
    } else {
        $msg = 'Wrong username or password.';
    }
}
```

Looking at the query we see that the password field is using the LIKE operator wich means we could use wildcards like '%' and '_'.

Which means '%' can match the password exactly, thus the cred is `admin:%`

### Flag : Hero{pwnQL_b4sic_0ne_129835}
