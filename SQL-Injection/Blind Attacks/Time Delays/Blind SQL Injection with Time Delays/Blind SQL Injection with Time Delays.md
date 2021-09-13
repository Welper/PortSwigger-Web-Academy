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
- Main goal with these SQLi's
    - Get noticable change in responses so that we can brute force the password with conditionals
        - Have not gotten to this step yet :-(
NOTE: syntax for time delay varies greatly among database versions, so reference datasheet as needed






