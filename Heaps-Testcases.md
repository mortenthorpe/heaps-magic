#Potential tests, and why#
## Rationale ##
Two types of tests, ideally:

- Unit tests
  * Useful in cases where strictly logical calculations are to be tested, such as subroutines for making deposits, asserting the expected increase to funds to the actual values to be returned.
  * Not useful where a fully available application-stack is required to be available; E.g. Testing that a controller can corectly fetch a specified user or other persisten-stored entity. Unit tests shall be runnable with only the application-layer is in place, no requirements of data-stores or transient data (sessions etc.).
  
- Behavior-driven development tests (BDD)
  * Useful as proof (typically deployed as continuous deployment conditionals) that Reqeust and responses function as expected.  All cases need to be handled including handling tests for expcted HTTP-status-codes for al cases of valid and invalid responses, according to requests made.  Preparing controllers for this type of cases typically requires making mockable entities, and mock requests and having the controllers use these - Today achieved in PHP using Dependency Injection Containers, and 'depedency inversion principle' (Symfony, Pimple, PHP-DI etc.).


Unit-tests can be *addressed indirectly* by further specifying behavior-driven tests, their entry-conditions and values, and their outcomes.

## Unit tests ##
In referring to te test-task definitions, and the description of Unit test I've given above, I suggest these for unit-testing:

- US-MA-3
  * Input data is the deposited amount, output (multiplied amount) is asserted to be equal to the deposited amount as multiplied by a factor `3`
  * Special cases is negative amount, zero amount
  * Testing for approaching PHP maximum allowable for floatvals (https://www.php.net/manual/en/language.types.float.php) is theoretical
- US-MA-6 - Input data is deposited amount greater than zero, and also an deposit-stored being and amount at `500kr`, assert output to be capped at 500kr
  * Test with deposit-stored being `500kr`, deposited amount being zero, then asserting that output amount is equal to deposit-stored
  * Test with deposit-stored being `500kr`, deposited amount being negative, then asserting that output amount is equal to deposit-stored
  * Test with deposit-stored being `500kr`, deposited amount being positive, then asserting that output amount is equal to deposit-stored
- US-MA-8 - Input data is date-timestamp, and a withdrawal amount greater than zero:
  * Test case that the Date-time-logic of the platform is such that [TODAY]@'00.00:00' is either the `ending of today` or the `beginning of the following day` (e.g. colloquially tomorrow morning).
  * Test-case where time of day is 22:59 (e.g. before midnight, same day)
  * Test-case where time of day is exactly 00.00:00 (e.g. at midnight) - Rexpected asserted result shall match the exptected outcome of the first test here, resolving 00.00 to `ending of today` (result: deny withdrawal), or `beginning of the following day` (result: permit withdrawal)
  

... And these for `BDD`:

- US-MA-1
  * Test to confirm that a specifically requested user is matched by the specifc response including the magic-account with a relation to that specific user.
- US-MA-2
  * Test that the user with the account is different than the one recieving the deposit, result should be a HTTP response failure indicating error (HTTP-403).
  * Test that sure that the requested user is the one being responded with an updated magic-account amount corresponding to the previous depositied-amount total, adding to it the current request-deposit.
- US-MA-4
  * Make sure that the user is admin, or equal to a role where permission to add promotional amounts is granted.
  * Make sure that adding promotional funds to a desired magic-account, results in a response indicating that the same magic-account has a newly created Promotion relation.
  * Make sure that adding a Promotion twice results in a HTTP-error response, indicating that the same Promotional (maybe using a unique promo-code as reference) cannot be added multiple times.
- US-MA-5
  * Setup scenario with multiple MagicAccounts for at least two users (test-users), having different random account deposits, promotionals and debits.
  * After withdrawal operation (close magic-accounts action) - Make sure that all magic-account deposited-amounts belonging to the requested user (one of the two above), are set to a zero value.
  * Test that only the requested user account have been affected, and not the other test-users.
- US-MA-7
  * Setup scenario with a single test-user... Initial deposited amount is zero kr. for every scenario below.
  * Test that depositing 150 kr., results in a HTTP error - Amount must be exactly '100 kr'
  * Test that depositing 20.25kr, then 79.75kr with the same date-day specified, results in a HTTP success - also indicating total deposited-amount to be `100kr`
  * Test that depositing 60.75 kr, then 80kr with different date-day specified, results in a HTTP error - Amount must be exactly '100 kr' per day
  * Test that depositing 25kr, then 80kr with the *same date-day* specified, results in a HTTP error - Indicating exceeding account deposit, and also indicating total deposited-amount to be `100kr`
- US-MA-9
  * Setup scenario with multiple users holding magic-accounts in the data store.
  * Expose the result of the data-selection of a single users magic-account transactions from the datastore, as a structured data-response (JSON/XML)
  * Setup matching criteria for expected outcome, validating the selected users expected results from the scenario-setup data, with that fetched in the output.  Validation can be done as object/property comparisons, choosing fields and relations to validate by value.
  * Valdate that references to users other than the requested one are *NOT a part of the output* (ca be done by parsing traversal of expected user-reference IDENTITY fields in the data-output, making sure that all values in that output are the same as the IDENTITY for the requested user.
  
