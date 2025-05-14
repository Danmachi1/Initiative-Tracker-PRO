Initiative Tracker PRO
Initiative Tracker PRO is a tool designed to assist in managing initiative orders during tabletop role-playing games (TTRPGs).​

Features
Track and manage initiative orders for players and non-player characters.

User-friendly interface for quick adjustments during gameplay.

Save and load initiative lists for recurring sessions.​

Getting Started
Prerequisites
A modern web browser (e.g., Chrome, Firefox, Edge).​

Installation
Clone the repository:​

bash
Copy
Edit
git clone https://github.com/Danmachi1/Initiative-Tracker-PRO.git
Navigate to the project directory:​


Open Initiative Tracker PRO.html in your preferred web browser.​

Usage
Add characters by entering their names and initiative scores.

Reorder the list as needed to reflect changes during gameplay.

Save the current initiative order for future sessions.​

Contributing
Contributions are welcome! Please fork the repository and submit a pull request with your enhancements.​

License
This project is licensed under the MIT License. See the LICENSE file for details.​

Note: The above README is based on limited information available from the repository. For a more detailed guide, additional information about the project's functionality and usage would be beneficial

1. 

main(String[] args)

Entry point.

Decrypts args if necessary.

Parses state, object store, from-date, and document class list.

Loops over document classes to:

Cancel checkout of reserved docs.

Copy values from single → multi-value properties.

2. 

MultiValuestSweep_generic(String userName, String password, String objectStore, String state)

Constructor: connects to FileNet using the right URI for the state.

Stores the ObjectStore for later use.

3. 

connect(String userName, String password, String uri)

Connects to the FileNet CE using CPE URI.

Sets UserContext.

4. 

getURI()

Converts placeholder uri to match environment based on state:

pr → fncpe-p

ut/si → fncpe-d

else → fncpe-t

5. 

getObjectStoreName(String baseName)

Appends the uppercase state to the object store name (e.g., CPB → CPBUT).

6. 

searchDocuments(String statement)

Executes a CBR SQL query.

Returns up to 500 matching documents (IndependentObjectSet).

7. 

cancelCheckout(String search)

Finds all reserved (checked out) docs from a query.

Cancels checkout, avoids update errors.

8. 

copyToMultiValueProperties(String search, String clientId, String caseId)

Executes a query for docs.

Loops over them to:

Copy clientId and caseId from single to multi-value.

Save only if updated.

9. 

copyPropertyValue(Properties properties, String singleProp, String multiValueProperty)

If both properties exist:

Gets value from singleProp.

If not in multiValueProperty, appends it and updates.

10. 

checkPropertiesExist(...)

Checks both properties exist to avoid NullPointerExceptions.

11. 

encrypt(String clearText)

 and 

decrypt(String encryptedText)

Uses DES3 to encrypt/decrypt credentials.

You will remove these — not allowed anymore.

II. HOW TO UPDATE FOR UT + CPB + TaxType (AuditEntry)



1. 

Replace all usage of client_id / case_id with tax_type



Update this block in main() (inside the for loop):

String clientId = "TaxType";
String caseId = null; // If not needed, ignore caseId logic
2. 

Update SELECT/WHERE CLAUSES



You’ll need new clauses, e.g.:

private static final String SELECT_TAX_CLAUSE =
    "select [Id], [TaxType], [TaxTypes]";  // adjust symbolic names as needed

private static final String WHERE_TAX_CLAUSE =
    " where ([TaxType] is not null AND [TaxType] <> '' AND [TaxTypes] is null) AND [DateCreated] >= " + PARAM_FROM_DATE + "T050000Z";
Then:

searchStatement = SELECT_TAX_CLAUSE + FROM_CLAUSE + WHERE_TAX_CLAUSE;
And add .replaceFirst(PARAM_FROM_DATE, fromDate).

3. 

Adjust copyToMultiValueProperties call

sweep.copyToMultiValueProperties(searchStatement, "TaxType", null);
In the method, skip caseId if null.

4. 

REMOVE ENCRYPTION



Delete or comment out:

encrypt()

decrypt()

The two lines in main():

args[0] = encrypt(args[0]);
args[1] = encrypt(args[1]);
Instead, pass credentials in plain form securely via batch job or parameter vault, depending on your deployment policy.

5. 

Update Field Names (if needed)



If TaxType is not the exact symbolic name:

Confirm in FileNet Workplace XT/ICN.

Replace in query + method parameters accordingly.

6. 

Update uri value if needed



Ensure:

private String uri = "t3://fncpe.isvcs.net:10101/FileNet/Engine";
matches your UT CPB FileNet endpoint. If not, change accordingly.

Summary: Changes to Make

Area

Action

main() args

Use plain credentials, parse for TaxType

Field Names

Replace client_id, case_id with TaxType

Queries

Build SELECT_TAX_CLAUSE, WHERE_TAX_CLAUSE

Copy Logic

Pass TaxType to copyPropertyValue(...)

Encryption

Remove both encrypt() and decrypt()

State

Make sure state=ut, object store is CPBUT, URI matches


.
