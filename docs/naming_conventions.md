## What to name a bundle?

Naming code is often referenced as one of the 2 truly difficult parts of coding (along with caching invalidation and off by one errors https://twitter.com/codinghorror/status/506010907021828096). Naming in Express is no exception. While we haven't always followed this pattern, this is the current recommendation.

express_ Any bundle that is not CU specific, but is being designed to work within Express.

cu_ Express specific code that is unlikely to have much value beyond a reference to another group. In D7 this was used for all code writen for CU Boulder.  In D8, the cu_ underscore will indicate that it 

ucb_ this was not used in D7.  In D8, this will indicate that the code is specific to CU Boulder

custom_ for a site specific bundle that is unlikely to be used on more than one site

Modules that are not specific to CU or Express that may be useful as contrib should be named accordingly.  User External Invite and Profile Module Manager are good examples of this.
