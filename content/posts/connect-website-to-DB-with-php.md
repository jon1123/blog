---
title: "Connect Website to DB With Php"
date: 2023-07-26T14:57:44-07:00

---
# Why 

For the HackTheBox(HTB) Academy class I am working "Bug Bounty Hunter" at the end of the second module they gave the following assignment and I was following a how to make a forum tutorial and got found struggled to connect to the database. This project may not be finished now but I plan to work on the CSS and JavaScrip parts later. For now I am going to do a much simpler website that will be self hosted so that I can do the testing I want for part 8 and 9 in the below list but I want to share what I have been working on the past few weeks. 

I found the most useful parts of this where the refresher on using SQL and the connecting to mariadb with php and the php error message code that was added to the php files and the php -l file.php debugging. Thank you Gabe for your help. 
  
# Assignment 

|**Step**|**To-Do**|
|---|---|
|`1.`|Set up a VM with a web server|
|`2.`|Create an `HTML` page|
|`3.`|Design it with `CSS`|
|`4.`|Add some simple functions with `JavaScript`|
|`5.`|Program a simple web application|
|`6.`|Connect your web application to the database|
|`7.`|Experiment with APIs|
|`8.`|Test your application for various vulnerabilities and security holes|
|`9.`|Try to adjust your code and configurations to close the vulnerabilities|

# Stating the Project

[from video](https://youtu.be/DpfMpc44MXY)

[php documentation](https://www.php.net/docs.php)


## index.php

```html 
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DT
D/xhtml1-transitional.dtd">
<html lang='en-US'>
        <head>
                <title>Forum.</title>
                <meta charset='UTF-8'>
<style>
body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
}
</style>
        </head>
        <body>
                <h1> Welcome to the forum. </h1>
                <form action='login.php' method='POST'>
                        Username: <input type='text' name='username' placeholder='Username'/><
br/>
                        Password: <input type='password' name='password' placeholder='Password
'/><br/>
                        <input type='submit' name=login' value='Log In'/>
                </form>
         </body>
</html>
```

## Database forum 
```sql 
CREATE DATEBASE forum;

CREATE TABELE users (
	id INT NOT NULL AUTO_ICREMENT,
	username VARCHAR(100) NOT NULL,
	password VARCHAR(100) NOT NULL,
	PRIMARY KEY (id)
);

insert into users(id, username, password) values(default, 'jmk', 'jmk');

create table sub_forum(forum_id INT, forum_name varchar(240) not null, forum_discrip text);
Query OK, 0 rows affected (0.066 sec)

explain sub_forum;
+---------------+--------------+------+-----+---------+-------+
| Field         | Type         | Null | Key | Default | Extra |
+---------------+--------------+------+-----+---------+-------+
| forum_id      | int(11)      | YES  |     | NULL    |       |
| forum_name    | varchar(240) | NO   |     | NULL    |       |
| forum_discrip | text         | YES  |     | NULL    |       |
+---------------+--------------+------+-----+---------+-------+
3 rows in set (0.005 sec)

create table post(post_id int, post_title varchar(240) not null, post_author
varchar(240), post_body text, post_type enum( 'o', 'r') default 'o', orig_post_id int, forum_n
ame varchar(240));
Query OK, 0 rows affected (0.446 sec)

explain post;
+--------------+---------------+------+-----+---------+-------+
| Field        | Type          | Null | Key | Default | Extra |
+--------------+---------------+------+-----+---------+-------+
| post_id      | int(11)       | YES  |     | NULL    |       |
| post_title   | varchar(240)  | NO   |     | NULL    |       |
| post_author  | varchar(240)  | YES  |     | NULL    |       |
| post_body    | text          | YES  |     | NULL    |       |
| post_type    | enum('o','r') | YES  |     | o       |       |
| orig_post_id | int(11)       | YES  |     | NULL    |       |
| forum_name   | varchar(240)  | YES  |     | NULL    |       |
+--------------+---------------+------+-----+---------+-------+
7 rows in set (0.004 sec)

insert into sub_forum values(1,'how to build a forum','buillding a forum');
Query OK, 1 row affected (0.006 sec)

 select * from sub_forum;
+----------+----------------------+-------------------+
| forum_id | forum_name           | forum_discrip     |
+----------+----------------------+-------------------+
|        1 | how to build a forum | buillding a forum |
+----------+----------------------+-------------------+

insert into post values(1,'first post', 'jmk', 'this is my first post to test this out','o' ,null, 'how to build a forum');
Query OK, 1 row affected (0.017 sec)

 select * from post;
+---------+------------+-------------+----------------------------------------+-----------+--------------+----------------------+
| post_id | post_title | post_author | post_body                              | post_type | orig_post_id | forum_name           |
+---------+------------+-------------+----------------------------------------+-----------+--------------+----------------------+
|       1 | first post | jmk         | this is my first post to test this out | o         |         NULL | how to build a forum |
+---------+------------+-------------+----------------------------------------+-----------+--------------+----------------------+
1 row in set (0.001 sec)



```

## login.php
```php
<?php
	$username = $_POST['username'];
	$password = $_POST['password'];
	
	echo "User: ".$username;
	echo "Password: ".password;
?>
```

## register.php

```html 
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DT
D/xhtml1-transitional.dtd">
<html lang='en-US'>
        <head>
                <title>Forum.</title>
                <meta charset='UTF-8'>
<style>
body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
}
</style>
        </head>
        <body>
                <h1> Welcome to the forum. </h1>
                <form action='register_parse.php' method='POST'>
                        Username: <input type='text' name='username' placeholder='Username'/><
br/>
                        Password: <input type='password' name='password' placeholder='Password
'/><br/>
                        <input type='submit' name=login' value='Register'/>
                </form>
         </body>
</html>
```

## register_parse.php
```php
<?php
	ini_set('display_errors',1);
	error_reporting(E_ALL);
	mysqli_report(MYSQLI_REPORT_ERROR | MYSQLI_REPORT_STRICT);

	include("connect.php");
	
	$username = $_POST['username'];
	$password = $_POST['password'];
	
	echo "hi";
	
	$sql = "INSERT INTO users (id, username, password)
		VALUES (default, '$username', '$password');
	";
	 echo $sql;
		
	$result = $conn->query($sql);
	
	echo $result ? 'true' : 'false';
?>
```


## connect.php 

```php
<?php

	ini_set('display_errors',1);
	error_reporting(E_ALL);
	mysqli_report(MYSQLI_REPORT_ERROR | MYSQLI_REPORT_STRICT);

	$host = "localhost";
	$user = "root";
	$pass = "root";
	$db   = "forum";
	
	$conn = new mysqli($host, $user, $pass, $db);

	echo "$conn->host_info";
	echo $conn->connect_error;

?>

```

### test.php
I was not able to connect with what i have so i made a test 

```php
<?php

	ini_set('display_errors',1);
	error_reporting(E_ALL);
	mysqli_report(MYSQLI_REPORT_ERROR | MYSQLI_REPORT_STRICT);

	$host = "localhost";
	$user = "root";
	$pass = "root";
	$db   = "forum";
 //	try {
	$conn = new mysqli($host, $user, $pass, $db);

	echo "$conn->host_info";
	echo $conn->connect_error;
	
	$sql = "SELECT id,username FROM users;";

	echo $sql;

	$result = $conn->query($sql);

	echo $result ? 'true' : 'false';

/*	if ($conn->query(($sql) === TRUE)){
		echo ;
	} else {
		echo "Error: " . $sql . "<br>" . $conn->error;
	}
	} catch(Exception $e){
		echo "Message: " . $e->getMessage();
	}*/
	$conn->close();
?>

```

It took a few try's and help from a fried. Thank you Gabe. 

One of the things he helped with is the to add this to the top of each page to get a more usable error message. 
```php
ini_set('display_errors',1);
error_reporting(E_ALL);
mysqli_report(MYSQLI_REPORT_ERROR | MYSQLI_REPORT_STRICT);
```
Also run this to debug your php code.
```bash
$ php -l page.php
```

Now i need to ad some CSS to help it look nice. Then maybe some JavaScript for more functionality. 

