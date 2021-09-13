# General Notes
- Previously, we tried to discover vulnerabilities based on responses to inputs. What happens if no unique responses are provided?
- We can try to trigger specific SQL errors
    - xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a (False)
    - xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a (True)
        - CASE -> if WHEN clause if true the THEN clause executes
            - if false then ELSE clause is executed
        - convoluted way for doing the 'a'='a' stuff from before, but allowing for custom input for error discovery
- Once we know that the errors are changing (SQLi), then we can start stealing the password
    - xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a
- Might need to try a few errors to get something
    - Look up SQL Errors and inputs needed to trigger them

# Goal: Login as Admin

# Useful Lab Notes
- Custom error messages are returned
- Does not change if query returns zero rows 
    - Conditional queries no work here
- Table: users
- Columns: username / password

# Preliminary Work
- Find the Tracking Cookie
    - ![Tracking Cookie]()
- Entered False payload to ensure that webpage still loads
    - Returned 500 (oof...)
- Tried xyz' AND 'a'='a
    - Returned 200 (yay)
- Tried xyz' AND 'b'='a
    - Returned 200 (proof that conditional responses are insufficient)
- Might be a database version issue with some keywords
    - Went through the Cheat Sheet to no avail
- Will try looking at solutions
    - Expected to get 500 response when the divide by zero was triggered, but it is happening without it as well
    - Tried the password busting payload with > 'z' and still got a 500 error, so idk what to do


 ## *** Postponed until I figure it out *** ##


