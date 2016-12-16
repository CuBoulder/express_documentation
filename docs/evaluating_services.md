Every year we are asked to more external services with Web Express. These are services that are offered by both 3rd party vendors (ie. Goolge Analytics) as well as services provided by other CU units (ie. UIS’s ClassesSearch API).  

Determining which services to integrate into our service is extremely important to the stablity of our service.  While service integration can add additional functionality Web Express users would benefit from, an unstable and/or unsupported service will have a negative impact on all Web Express users if it becomes a drain on our server, bandwidth, development and/or support resources.  

We now have examples of both good and bad integrations.  Integrating services like Google Analytics, Vimeo, Twitter, and Facebook continue to work well and require little explanation or support.  Services like the Class Search and Community Funded required far more development and support time that we estimated.

The following criteria for evaluating services was developed after comparing integrations that have gone well with ones that have gone poorly.  

* Does the service provide accurate documentation?
* Is the service used by a large number of other sites? (Are we one of the first?)
* Is there a community where issues with the service are openly discussed?  (Are we seeing feedback from real users or just marketing material?)
* Does the service provide a status page and log outages with explanations?
* Does the service provide roadmaps and timeline for updates?
* Does the service provide access to test and provide feedback about updates before they are released?
* Does the service provide support?  (At both the developer and end user level?)
* Can we track the status of support requests?
* Does the service have an existing Drupal integration that is reports several installs and appears to be well supported?

Answering no to one of two of these wouldn’t mean we’d refuse to integrate the service, but answering no to all or most of these should be a red flag that we could have issues with this service.
