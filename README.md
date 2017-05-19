# Fuzzy Matching using the Jaro Winkler PLSQL Utility
The following is a high level description of how the Fuzzy Matching algorithm can be utilized when dealing with data quality issues like duplicates, orphaned records etc. For any corporation the ability to identify their customers and their repeat activity is key to provided them the best experience possible. One of the issues that is common especially with larger corporations is the issue of not having a consistent record of a single customer. 

## Why is Customer Data Quality such a common issue?
When a customer goes to a website to process an order or if they respond to a marketing offering there is a high likelyhood that the customer or person dealing with the customer will enter the wrong or slightly incorrect information that will be used to reconcile that customer with an existing relatioship and record. Manual data entry or too much friction at the touchpoint are mostly to blame.

## What is Jaro Winkler?
The Jaro algorithm is a measure of characters in common, being no more than half the length of the longer string in distance, with consideration for transpositions. Winkler created this algorithm to support the idea that differences near the start of the string are more significant than differences near the end of the string. Jaro and Jaro-Winkler are suited for comparing smaller strings like words and names.
Because Jaro Winkler performs better with names, addresses and emails it is the perfect algorithm to identify and merge Customer records that might be duplicates or mismatched.

## Jaro Winkler to the rescue
The process of cleaning up data quality issues is much more involved that just running this utility, but it is a good start nonetheless. Also, the fact that it is incorporated in the PLSQL utilties enables one to utilize this during the batch (ETL) process.

## Utilizing the Jaro Winkler utility
From my experience the more fields you try to match on a single record, the better and more reliable your fuzzy matching results will be.

### Running the Algorithm
#### This is how you would typically run the fuzzy matching query:
In this query my goal is to get a list of fuzzy matches (non-exact matches) that have a high likelyhood of being actual matches. One of the challenges of fuzzy matching is that you must compare each record in one table to each record in abother table, which can be a very taxing query on your server. In order to remedy this we will get creative with our JOINs. 

Instead of comparing each record to each other record, we will JOIN each table to the other based on the first letter of the name or if you want to be even more aggressive you can join them based on the first two letters in the name, although you will sacrifice accuracy.

Also in order to ensure the algorithm performs consistently you will need to convert all fields to UPPER or LOWER case.
```sql
SELECT 
TABLE_A.FIRST_NAME, --First name from the LEFT TABLE (our source of truth table)
TABLE_A.LAST_NAME, --Last name from the LEFT TABLE (our source of truth table)
TABLE_A.ADDRESS_1, --First line of the address from the LEFT TABLE (our source of truth table)
TABLE_B.FIRSTNAME, --First name from a RIGHT TABLE (our table with potential dupes, mismatches -could be the same table)
TABLE_B.LAST_NAME, --First name from a RIGHT TABLE (our table with potential dupes, mismatches -could be the same table)
TABLE_B.ADDRESS_LINE_1 --First name from a RIGHT TABLE (our table with potential dupes, mismatches -could be the same table)
FROM TABLE_A
JOIN TABLE_B ON (UPPER(SUBSTR(TABLE_A.FIRST_NAME,1,1)) = UPPER(SUBSTR(TABLE_B.FIRSTNAME,1,1)))-- Convert to UPPERCASE and JOIN based on the first letter in the string
WHERE UTL_MATCH.JARO_WINKLER_SIMILARITY (UPPER(A.REPORTED_ORG_NAME), UPPER(B.PARTY_NAME)) > 90 --This WHERE clause limits the results to only records that are Exact or Fuzzy matches. 
AND UTL_MATCH.JARO_WINKLER_SIMILARITY (UPPER(TABLE_A.LAST_NAME), UPPER(TABLE_B.LASTNAME)) > 90 --You can play with the match score to ensure you have the right level of aggressiveness.
AND UTL_MATCH.JARO_WINKLER_SIMILARITY (UPPER(TABLE_A.ADDRESS_1), UPPER(TABLE_B.ADDRESS_LINE_1)) > 85
```
