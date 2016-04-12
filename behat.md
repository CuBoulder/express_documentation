# Writing Tests
## Directory Structure
In order to start writing tests, the first thing you will have to understand is how to create or add to a test file.
+ The actual tests you will be writing are called scenarios and scenario outlines, are driven by step definitions, and are included in a Feature file.
+ Feature files are groups of sceanrios and are located inside of test suites.
+ Tests suites include groups of related features.
+ Contexts help to extend step definitions that are included in the general Behat framework. Contexts are placed in the "bootstrap" folder.
+ The behat.yml configuration file contains all the neccessary configuration for Behat to run features.

To help you visualize this relationship the directory structure looks like this:
```
behat/
 - bootstrap/
   - FeatureContext.php
 - suite_one/
   - module_one
     - feature01.feature
 - suite_two/
   - module_two
     - feature02.feature
 - behat.yml
```

## Suites
In Behat terminology, a suite is a group of features that relate to a specific part of your codebase. The Web Express suites are broken up according to bundles so that a test run can target only a specific bundle rather than testing the whole codebase.

If a new bundle is added to the Express codebase but doesn't have a suite folder, you should add one to the codebase with the name of the bundle. The name of the directory should be the machine name of the bundle, e.g "cu_news_bundle".

## Features
Features are groups of related scenarios stored in a ".feature" file. Typically, they will encompass a particular set of functionality, like testing user login scenarios. One test might check to make sure certain roles can login while another test might check to see that other roles or anonymous users can't login. To better organize features, they are grouped into folders. Behat auto-discovers all features within the root test folder, so you can nest the features in as many folders as you desire, but in the Express repository, a convention is followed to group features.

The Express test suite is broken down by "suite > module > test.feature" and an example would be: "cu_photo_gallery_bundle > photo_gallery > photo_gallery_access.feature". The idea with the feature file name is to be as descriptive as possible and to try and use the Single Responsibility Principle. Since there is no limit on the number of feature files you can include, you can be as atomic in your test creation as possible.

Having feature naming conventions also benefits test coverage by standardizing what should be covered when adding new functionality to the codebase. In the Express test suite, there are currently standardized tests for:
* Node/Bean access tests
* Node/Bean creation tests

## Scenarios
Scenarios are the actual meat and potatoes of Behat tests. Scenarios direct the Behat driver to do exactly what you want in a test while remaining readable to the average human. They usually follow a format of: Given "setup action" And "action" Then "expected result."
```
  Scenario: An anonymous user shouldn't be able to create Photo Gallery content.
    Given I am on "node/add/photo-gallery"
    Then I should see "Access Denied"
```
The "Scenario" line should succintly describe what is being tested in one sentence. The scenario steps that follow are matched via regular expressions in "FeatureContext.php" files. Most of the steps used in the Express test suite come from the [Drupal Behat extension](https://github.com/jhedstrom/drupalextension).

## Step Definitions
Occasionally, it makes sense to create your own step definitions when there aren't pre-defined steps that fit what you are trying to test. User-created step definitions can also be used to enhance the performance of the overall test suite.

One example used in the Express test suite is to create a bean or node after creating beans or nodes has been tested by a particular user role. Once the node or bean creation form has been tested once, it isn't that useful to keep checking if that part of the codebase is working. Running through the form with Behat steps takes time, and programatically creating content is much faster. To aid in speeding up the test suite, the following step definition was created.
```
  /**
   * @When /^I create a "(?P<bean_type>(?:[^"]|\\")*)" block with the label "(?P<label>(?:[^"]|\\")*)"$/
   */
  public function iCreateABlockWithTheLabel($bean_type, $label) {
    // Create Block.
    $values = array(
      'label' => $label,
      'type'  => $bean_type,
    );
    $entity = entity_create('bean', $values);
    $saved_entity = entity_save('bean', $entity);
    // Go to bean page.
    $session = $this->getSession();
    $session->visit('block/' . $entity->delta);
  }
```
In the DocBlock, the step is defined. Behat uses double quotation marks to deliniate where a regular expression begins and ends. As you can see in the example above, there are two regular expressions for two variables that are passed to the step function. The name of the step function is not that important, but a convention is to use the words in the step definition without the variables.

The step function will then take the variables, perform some action, and hand back the result to the session handler for the next step in the scenario. The step function can also throw an exception if a part of the function returns an error. It is always best to wrap I/O and query parts of your function with exceptions so you can know exactly where your tests are failing. To improve the step function listed above, you could add an exception handler for when the entity is saved.
```
  /**
   * @When /^I create a "(?P<bean_type>(?:[^"]|\\")*)" block with the label "(?P<label>(?:[^"]|\\")*)"$/
   */
  public function iCreateABlockWithTheLabel($bean_type, $label) {
    // Create Block.
    $values = array(
      'label' => $label,
      'type'  => $bean_type,
    );
    $entity = entity_create('bean', $values);

    try {
      $saved_entity = entity_save('bean', $entity);
    } catch (ErrorException $e) {
      throw new \Exception($e->getMessage);
    }

    // Go to bean page.
    $session = $this->getSession();
    $session->visit('block/' . $entity->delta);
  }
```

One thing to remember when writing step definitions is that Behat uses double quotations to mark where regular expressions begin and end. So, if you must use double quotes in your step deifnition, then you need to escape them in order for your test to run properly.

## Scenario Outlines
Scenario outlines are just like regular scenarios with the addition of being able to run many conditions at once. If you are testing whether several user roles can login to your website, it makes sense to include all of those roles in one scenario rather than having to write a scenario per role. Another example would be if you had many variables to test on a certain configration form.
```
  Scenario Outline: Certain user roles should be able to create Photo Gallery content.
    Given I am logged in as a user with the <role> role
      And I am on "node/add/photo-gallery"
    Then I should see <message>

    Examples:
    | role            | message                 |
    | content_editor  | "Create Photo Gallery"  |
    | site_owner      | "Create Photo Gallery"  |
    | administrator   | "Create Photo Gallery"  |
    | developer       | "Create Photo Gallery"  |
    | edit_my_content | "Access Denied"         |
```
The variables Behat uses in the scenario are listed under an "Examples:" section. The header row replaces variables in the scenario that are listed in between carets like "<role>".

## Tags
Tags are listed above scenarios and can be used for many purposes. The most common purpose is to group scenarios so that they can be targeted later when running Behat tests. For example, you can use a "@clean_install" tag to separate tests that should only be run when your site is in a clean install state: "behat --tags "@clean_install".

You can also use tags to exclude tests. Let's say you know certain tests are failing when you run your test suite, but you can't fix them immediately and don't want them cluttering your test results. You could add a "@broken" tag and then exclude them from the test suite run: "behat --tags "~@broken".

The "@javascript" tag is a special, reserved tag used when you need to test a scenario that uses JavaScript. Chances are, if you're manipulating items on a page or filling out a form with popups or tab, you'll need to use the JavaScript tag. This tells Behat to use the Selenium driver run the test rather than the default driver.

The "@api" tag is also a special, reserved tag. It is used in every test in the Behat suite, because it provides Drupal-specific step definitions for logging in and cleans up the test environment after a scenario has been run.

# Running Tests

To run Behat tests, you must be in the root folder where the "behat.yml" configuration file is located. To run the whole test suite you would just type "behat" into your terminal. If you only wanted to run a particular test suite, you can type "behat --suite=name_of_suite". To run a particular tag you can type "behat --tags @tag_name".

# Debugging Tests
Occasionally, you'll want more information from the test run for debugging purposes. In that case, you can have Behat post output in a verbose mode "behat --verbose".

Another useful debugging tool is to use step definitions to capture infomation you can't see while the test is running. When using the Selenium Driver, which emulates the Firefox browser, it is useful to see what the browser is seeing when a test fails.
```
  /**
   * @AfterStep
   */
  public function takeScreenShotAfterFailedStep($scope) {
    if (99 === $scope->getTestResult()->getResultCode()) {
      $driver = $this->getSession()->getDriver();
      if (!($driver instanceof Selenium2Driver)) {
        return;
      }
      file_put_contents('/tmp/test.png', $this->getSession()->getDriver()->getScreenshot());
    }
  }
```
The step definition defined above takes a screenshot if the test fails and sticks it in a "tmp" directory. You then can get information from that screenshot, like if an element is present on the page when a step fails.

# Travis CI Integration
The Express Behat test suite is run on every commit and PR that is pushed to the Express Github repository via Travis CI. When the test run completes, a pass or fail notice will be sent back to the particular commit or PR. Right now, there are still several false postives that end up breaking tests but are only timeout errors. Those tests were marked with a "@max_execution_time" tag and put in a separate test run where they all passed.

The actual Travis settings and script are located in the .travis.yml file. That file contains all of steps needed to run the test suite on Travis CI and the "tests/travis-ci" folder contains assets that are needed for Travis CI integration. Different Drupal settings are required to run the tests on Travis CI, and so that settings file is placed in the travis-ci folder and copied to the right place during the .travis.yml script run.

# Optimizing Tests
TBD...
