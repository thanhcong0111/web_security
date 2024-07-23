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
