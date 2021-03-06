# General Notes
- Some websites handle conditionals and errors gracefully, so we do not really get any info
- However, SQL might still be possible
    - Inject some code with a delay -> '; IF (1=2) WAITFOR DELAY '0:0:10'--
        - Notice the ending comments to nullify rest of code
        - Waiting for 10 secs
        - First quotation mark serves as end for preceding statement -> Then, we inject own code
        - Since 1 != 2, the reponse should be normal
        - Changing it to 1=1 lets the magic happes
    - If it delays, then we have SQLi!
- Payload: '; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'-- 
    - If the number of usernames with "Administrator" and have a password beginning with 'm' is ONE, then the delay will let us know that we guessed correctly
- Main goal with these SQLi's
    - Get noticable change in responses so that we can brute force the password with conditionals
        - Have not gotten to this step yet :-(
NOTE: syntax for time delay varies greatly among database versions, so reference datasheet as needed

# The Lab
## Goal: Cause a 10 second delay

## Time Delay Exploitation
- Given:
    - Using tracking cookie to differentiate users
    - This cookie is used in SQL Query that checks its value
        - We will inject our stuff into this query via the cookie
- Tried '; (1=2) WAITFOR DELAY '0:0:10'-- (:x:)
- Tried '; (1=1) WAITFOR DELAY '0:0:10'-- (:x:)
    - no delay :-(
    - ^ for Microsoft versions
    - Also tried unconditional version: '; WAITFOR DELAY '0:0:10'-- (:x:)
- Trying other versions (unconditional)
    - PostgreSQL: fake'||pg_sleep(10)-- (:white_check_mark:)
        - ![time delay success](./time-delay.png)
            -  notice the turtle icon to show that it took a while
        - Tried conditional syntax but unsuccessful
        - '||' is string consatentation in PostgreSQL
            - Tried putting it between each word in conditional payload to no avail (plus signs did not work either)

- General strategy for future:
    - Try conditional stuff from different SQL versions
        - trying # for comment and || or + for whitespace elimination
    - Try unconditional payloads for each SQL version
        - same variations as above
- Might try || for prior labs which I was unable to solve




