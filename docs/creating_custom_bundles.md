# Express Bundles

## What Are Bundles 

## Express Core Or Custom Bundle?

Determining if a bundle belongs in Express Core or should be defined as an optional Express+ bundle can be difficult.  

* Does the bundle need to be updated more often than the Express release cycle?  

  Bundles that require more frequent updates including bundles we are beta testing should be Express+. The number of bundles maintained as Express+ in D7 may also grow as we are likely to do fewer D7 releases in 2017.
  
* Is this functionality we are comfortable allowing every site to enable?

If the functionality could potentially cause a resource issue (things like the number of open curl requests or poorly performing queries), we should control/monitor how many sites have this enabled. 

* Does the bundle entice users to enable it on a production site before they discover a commercial service is required and/or some other subjective review?

This is just a waste of resources.  It can add to the overhead of every page load, complicate the UI and the time it takes to run unnecessary update hooks.

* Does the bundle need to be deployed on Pantheon?  

Currently only bundles included in the Express profile are available on Pantheon.  All other bundles need to be manually added and updated. This is an issue because our internal bundle repos don't have a mechanism of informing sites about new versions/security updates.

## Creating Custom Bundles

In order to keep the Express core repository as lean as possible, addtional bundles for specific functionality that might 
not be needed on all sites are now placed in their own repositories. In order to create your custom bundle, you need to 
consider where the code is coming from. 

### Code Is New

### Code Is Coming From Express

## Releasing Custom Bundle Code

There are many custom bundles, a.k.a plus bundles, that exist in the Express ecosystem. We make issues, plan sprints, and merge code in, but we currently don't have any good process for releasing the code once it's ready to deploy. Previously, a developer had to be in the know as to when new code should be pushed to production and which sites needed updating with the new code.

Taking into account the fact that Drupal already has a mechanism in place to check for code updates, we will now utilize this mechanism so that indicual sites can report when they need their code updated instead of needing to have a developer remember where new code needs placed. 

For Drupal modules living on drupal.org, the core Update module takes care of checking for new code. However, for our Drupal modules that live on GitHub, we have no way for an individual site to check for new code. Luckily, Stanford Web Services has come to the rescue with their [External Repository Update Status module](https://github.com/SU-SWS/erus), or ERUS.

With one line to a bundle's info file, the ERUS module checks for updated code and displays out-dated modules via the Drupal GUI or on the commandline via Drush. A "project status url" key needs to be added to every module's info file you want to keep updated. 

```
name = Your Module
description = "A description of your module."
package = My-modules
core = 7.x
version = 7.x-1.0
project = your_module
project status url = https://github.com/org-account/your-module
```

Along with the "project status url" key, the "version" and "project" keys are neccessary for ERUS to give you an accurate report of which modules need updated.

### How To Succesfully Update A Custom Bundle's Code

1. Finish your sprint and prepare the repository for a release like you normally would. 
2. Make a tag in the Drupal format for modules, e.g. "7.x-1.1".
3. Make a release in GitHub using the tag from step two. 
4. Download the release files, e.g. "module-7.x-1.1.tar.gz".
5. Run md5 on your downloaded file, e.g. "md5 module-7.x-1.1.tar.gz".
6. Edit your release notes and add the checksum, e.g. "md5: ceb3af66ab78e65def488a175a54d5d4".
7. Go to "admin/reports/updates" to check if your custom module reports needing an update. 

### Future Updates

* In the future, the manual steps for adding the checksum to the release page might be automated.
* Currently, the ERUS module only supports GitHub projects, which is where most Express code is hosted, but additional external repository hosts might be added in the future.
