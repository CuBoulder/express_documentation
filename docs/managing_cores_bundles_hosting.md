
## Overview

In versions > 2.8.3, the Express install profile supports 3 types of modules; _core, _hosting, and _bundles. These 3 types allow institution and environment specific customizations within to a standardized core.

Anytime the Express profile is run on a different or new environment, it is encouraged to add a _hosting module for it.  Express includes a [express_check_known_hosts() function](express_check_known_hosts(https://github.com/CuBoulder/express/blob/dev/express.profile#L228) that will identify Travis and Pantheon based on environmental veriables.

## Differences:
* cu_core will eventually be renamed ucb_core or something along those lines when the Express profile provides one more layer of core abstraction. This core installs CU Boulder related configuration and contains update hooks relating to CU Boulder funcitonality and configuration changes. This supports adding additional campus/unit level cores like ucd_core, uis_core, uccs_core that could reuse the _hosting modules.
* travis_hosting is used in the automated testing run on Travis that every PR must pass before being merged.  
* pantheon_hosting allows the Express profile to be run on Pantheon's hosting. This mainly entails not enabling certain modules like memecache and LDAP that aren't supported on Pantheon while enabling other, Pantheon specific modules and alternative authentication configurations.
* ng_hosting is the default hosting configuraiton for CU Boulders current "next generation" hosting.
* web3_hosting will be used on the new Web 3.0 hosting. 

## Installation

The core you want to install can be declared when installing with drush by using the "express_profile_configure_form.express_core_version=" parameter. 

```
drush si express --db-url=mysql://root:@127.0.0.1/drupal --account-name=admin --account-pass=admin --site-mail=admin@example.com --site-name="Express" express_profile_configure_form.express_core_version=cu_testing_core --yes
```

## Versioning of Cores

Because Drupal 7 doesn't have real support for semantic versioning, we combine the version of the Express install profile with the version of the core used.  So version 2.2 of Express running version 2.0 of a core would be considered 2.2.0.  The 2.2 version of Express _should_ support all core 2.x core releases. The next update would only be made to core and get a version of 2.1 making the full release 2.2.1.
