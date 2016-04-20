
The Express install profile includes 2 cores; cu_core and cu_testing_core.  These cores allow institution and environemnt specific customizations within the standardized core.  cu_core will likely be renamed ucb_core or something along those lines.  cu_testing_core is used in the automated testing run on Travis that every PR must pass before being merged.  Teh core you want to install can be declared when installing with drush by using...

```drush si express --db-url=mysql://root:@127.0.0.1/drupal --account-name=admin --account-pass=admin --site-mail=admin@example.com --site-name="Express" express_profile_configure_form.express_core_version=cu_testing_core --yes```

**Versioning of Cores**

Because Drupal 7 doesn't have real support for semantic versioning, we combine the version of the Express install profile with the version of the core used.  So version 2.2 of Express running version 2.0 of a core would be considered 2.2.0.  The 2.2 version of Express _should_ support all core 2.x core releases. The next update would only be made to core and get a version of 2.1 making the full release 2.2.1.
