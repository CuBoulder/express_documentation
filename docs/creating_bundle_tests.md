
# Bundle Tests

Hooray, you are about to write some tests and prevent dumb bugs from getting into your code in the future! Take a minute to pat yourself on the back. When people talk about testing code, they generally separate it into two categories: unit and functioanl testing. 

Unit tests are for minute bits of code that do one thing. You don't need to bootstrap an entire application to run unit tests and they provide more immediate feedback for developers modifying code. 

Functional tests can insure that your code changes will play well with the entire bootstrapped application once merged together. Since these are less precise tests, i.e. they don't have to test your indiviudal functions, and take longer, most people say to have way more unit tests than functional ones. 

I am here to tell you that...LIES! For the Express application, unit tests are pretty worthless since we use so much contributed code. No code change we make exists in isolation. It must work on a Drupal site and that Drupal site must have a lot of additional code on it. Oh yeah...plus I did write a couple of lines I want to test...

So for those reasons, unit tests won't be written for the Express project until many more functional tests are written.

## What Is Behat?

To find out more about Behat and how to go about writing tests in general, you can look at: https://github.com/CuBoulder/express_documentation/blob/master/docs/behat.md

## Local Setup

You should start writing your Behat tests locally and then run them on the Travis CI setup. For instructions on how to set up and run the test suite locally, please follow: https://github.com/CuBoulder/express/blob/dev/tests/behat/README.md

## GitHub Integration

Now we can get on with this show...

### Travis CI

### CodeClimate
