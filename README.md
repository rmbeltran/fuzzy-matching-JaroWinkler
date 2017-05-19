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
```sql
SELECT 
TABLE_A.FIRST_NAME, --First name from the LEFT TABLE
TABLE_A.LAST_NAME, --Last name from the LEFT TABLE
TABLE_A.ADDRESS_1, --First line of the address from the LEFT TABLE
TABLE_B.FIRSTNAME, 
TABLE_B.LAST_NAME, 
TABLE_B.ADDRESS_LINE_1
```
