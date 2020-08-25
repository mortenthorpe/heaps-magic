#Selected Controllers for implementation, reasoning#

### Choices made ###
I chose `US-MA-2` and `US-MA-5`, see details below for rationaliation.

### Choices Rationalization ###

- `US-MA-2`, and not `US-MA-1`, as `US-MA-1` is trivially a definition, indirectly covered by `US-MA-2`. Furthermore `US-MA-2` leads naturally onto `US-MA-6` and `US-MA-7`, so they can be covered as TODO-annotations inside the pseudo-code for `US-MA-2`
- `US-MA-5`, and not `US-MA-8`
   * `US-MA-8` is indirectly addressable in `US-MA-5`, as logic should include limiting deposit-payouts to occur before a given time-of-day (e.g. '00.00:00')
- NOT `US-MA-3`, as this is a bit weakly defined, while the assumption that 'get all of my deposits multiplied by a factor X', probably means that this shall occur ONLY when the *first-access for that user of that account occurs*.  Furthermore, what is to appen with the users available amounts multiplication for usera with multiple accounts?
- NOT `US-MA-4`, as this is fairly separate from other logic, and can be covered in a 'Behavior driven testcase'.
- NOT `US-MA-9`, as this is merely visual-output proof, that the system functions as intended, while this type of `proof` should in any implementation be handled by a combination of unit- and BDD-tests.


