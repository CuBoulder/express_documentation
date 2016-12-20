Determining if a bundle belongs in Express Core or should be defined as an optional Express+ bundle can be difficult.  

* Does the bundle need to be updated more often than the Express release cycle?  

  Bundles that require more frequent updates including bundles we are beta testing should be Express+. The number of bundles maintained as Express+ in D7 may also grow as we are likely to do fewer D7 releases in 2017.
  
* Is this functionality we are comfortable allowing every site to enable?

If the functionality could potentially cause a resource issue (things like the number of open curl requests or poorly performing queries), we should control/monitor how many sites have this enabled. 

* Does the bundle entice users to enable it on a production site before they discover a commercial service is required and/or some other subjective review?

This is just a waste of resources.  It can add to the overhead of every page load, complicate the UI and the time it takes to run unnecessary update hooks.

* Does the bundle need to be deployed on Pantheon?  

Currently only bundles included in the Express profile are available on Pantheon.  All other bundles need to be manually added and updated. This is an issue because our internal bundle repos don't have a mechanism of informing sites about new versions/security updates.
