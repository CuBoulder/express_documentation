# Writing Tests
## Directory Structure
In order to start writing tests, the first thing you will have to understand is how to create or add to a test file.
+ The actual tests you will be writing are called scenarios and scenario outlines that are driven by step definitions and included in a feature file.
+ Feature files are groups of scenarios and are located inside of test suites.
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
Features are groups of related scenarios stored in a ".feature" file. Typically, they will encompass a particular set of functionality, like testing user login scenarios. One test might check to make sure certain roles can login while another test might check to see that other roles or anonymous users can't login. To better organize features, they are grouped into folders. Behat auto-discovers all features within the root test folder, so you can nest the features in as many folders as you desire, but in the Express repository, a convention is followed for grouping features.

The Express test suite is broken down by "suite > module > test.feature" and an example would be: "cu_photo_gallery_bundle > photo_gallery > photo_gallery_access.feature". The idea with the feature file name is to be as descriptive as possible and to try and use the Single Responsibility Principle. Since there is no limit on the number of feature files you can include, you can be as atomic in your test creation as possible.

Having feature naming conventions also benefits test coverage by standardizing what should be covered when adding new functionality to the codebase. In the Express test suite, there are currently standardized tests for:
* Node/Bean access tests
* Node/Bean creation tests

## Scenarios
Scenarios are the actual meat and potatoes of Behat tests. Scenarios direct the Behat driver to do exactly what you want in a test while remaining readable to the average human. They usually follow a format of: Given "setup action" And "action" Then "expected result."

```gherkin
  Scenario: An anonymous user shouldn't be able to create Photo Gallery content.
    Given I am on "node/add/photo-gallery"
    Then I should see "Access Denied"
```

The "Scenario" line should succintly describe what is being tested in one sentence. The scenario steps that follow are matched via regular expressions in "FeatureContext.php" files. Most of the steps used in the Express test suite come from the [Mink extension](https://github.com/Behat/MinkExtension).

```bash
# List available step definitions.
./bin/behat -dl
```

## Step Definitions
Occasionally, it makes sense to create your own step definitions when there aren't pre-defined steps that fit what you are trying to test. User-created step definitions can also be used to enhance the performance of the overall test suite.

One example used in the Express test suite is a spinner function. Most of the time the test suite fails, it is because of a JavaScript step not loading fully and trying to assert some element exists or is visible on the page. With a "spinner function", you can check for an element you know signifies the page has loaded, and then proceed with the test. The spinner step definition is actually comprised of two functions.

```php
  /**
   * @When I wait for the :arg1 element to appear
   * 
   * Wait for an element to appear before continuing a test.
   *
   * @param string $arg1
   *   The CSS selector you are waiting to appear.
   */
  public function iWaitForTheElementToAppear($arg1) {
    $this->spinner(function($context, $arg1) {

      $el = $context->getSession()->getPage()->findById($arg1);

      if ($el !== NULL && $el->isVisible()) {
        return true;
      }

      return false;
    }, $arg1);
  }

  /**
   * Accepts and element and "spins" until the element appears.
   * 
   * @param string $lambda
   *   The function to test for truthiness.
   * @param string $element
   *   The CSS selector used for truthiness.
   * @param int $wait
   *   The timeout to wait for before the step fails.
   *
   * @return bool
   * @throws \Exception
   */
  public function spinner($lambda, $element, $wait = 60) {
    for ($i = 0; $i < $wait; $i++) {
      try {
        if ($lambda($this, $element)) {
          return true;
        }
      } catch (Exception $e) {
        // do nothing
      }
      sleep(1);
    }

    $backtrace = debug_backtrace();

    throw new Exception(
      "Timeout thrown by " . $backtrace[1]['class'] . "::" . $backtrace[1]['function'] . "()\n" .
      $backtrace[1]['file'] . ", line " . $backtrace[1]['line']
    );
  }
```

In the DocBlock, the step is defined. Behat uses double quotation marks to deliniate where a regular expression begins and ends. As you can see in the example above, there are two regular expressions for two variables that are passed to the step function. The name of the step function is not that important, but a convention is to use the words in the step definition without the variables.

The step function will then take the variables, perform some action, and hand back the result to the session handler for the next step in the scenario. The step function can also throw an exception if a part of the function returns an error. It is always best to wrap I/O and query parts of your function with exceptions so you can know exactly where your tests are failing. In the `spinner()` function above, an exception is thrown when the timeout is reached containing information about the function called and where it appears in the code.

One thing to remember when writing step definitions is that Behat uses double quotations to mark where regular expressions begin and end. So, if you must use double quotes in your step deifnition, then you need to escape them in order for your test to run properly.

## Scenario Outlines
Scenario outlines are just like regular scenarios with the addition of being able to run many conditions at once. If you are testing whether several user roles can login to your website, it makes sense to include all of those roles in one scenario rather than having to write a scenario per role. Another example would be if you had many variables to test on a certain configration form.

```gherkin
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
Tags are listed above scenarios or at the top of feature files and can be used for many purposes. The most common purpose is to group scenarios so that they can be targeted later when running Behat tests. For example, you can use a `@clean_install` tag to separate tests that should only be run when your site is in a clean install state: `behat --tags @clean_install`.

You can also use tags to exclude tests. Let's say you know certain tests are failing when you run your test suite, but you can't fix them immediately and don't want them cluttering your test results. You could add a `@broken` tag and then exclude them from the test suite run: `behat --tags ~@broken`.

The `@javascript` tag is a special, reserved tag used when you need to test a scenario that uses JavaScript. Chances are, if you're manipulating items on a page or filling out a form with popups or tab, you'll need to use the JavaScript tag. This tells Behat to use the Selenium driver run the test rather than the default driver.

**NOTE:** You should ALWAYS try to avoid using the `@javascript` tag when possible. JS tests take a long time to run and can clog tunnels open for running the tests on an external Selenium server.

# Running Tests

To run Behat tests, you must be in the root folder where the `behat.yml` configuration file is located. To run the whole test suite you would just type `./bin/behat` into your terminal. You can export the path to the binary to run behat from anywhere, `behat`, but since the context files are usually included with the binary, that step is overkill if you only use Behat on this project. If you only wanted to run a particular test suite, you can type `./bin/behat --suite=name_of_suite`. To run a particular tag you can type `behat --tags @tag_name`.

```bash
# Run the whole test suite.
./bin/behat

# Run one suite.
./bin/behat --suite=name_of_suite

# Run only certain tags.
./bin/behat --tags @tag_name

# Run one tag but not another.
./bin/behat --tags "@good_tag&&~bad_tag"
```

If you get an error about no Drupal installation existing, then you'll want to change the root location Behat looks to find a Drupal site.

# Debugging Tests
Occasionally, you'll want more information from the test run for debugging purposes. In that case, you can have Behat post output in a verbose mode `./bin/behat --verbose`.

Another useful debugging tool is to use step definitions to capture infomation you can't see while the test is running. When using the Selenium Driver, which emulates a real browser, it is useful to see what the browser is seeing when a test fails.

```php
  /**
   * @AfterStep
   * 
   * Take a screenshot after a failed JS test.
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
The step definition defined above takes a screenshot if the test fails and sticks it in a `tmp` directory. You then can get information from that screenshot, like if an element is present on the page when a step fails.

# Travis CI Integration
The Express Behat test suite is run via Travis CI on every commit attached to a PR in the Express Github repository and also run when a PR is merged into dev. When the test run completes, a pass or fail notice will be sent back to the particular commit or PR and you will be able to see this on the PR page.

[screenshot]

Currently, there are two test runs performed in parallel: one without JavaScript tags `@~exclude_all_bundles&&~broken&&~javascript` and one with JavaScript tags `@javascript&&~exclude_all_bundles&&~broken`. You can use environmental variables on Travis CI to split test runs into a matrix.

```yaml
env:
  - BEHAT_TAGS="@~exclude_all_bundles&&~broken&&~javascript"
  - BEHAT_TAGS="@javascript&&~exclude_all_bundles&&~broken"
  
# ...stuff

# Run Behat tests in parallel.
# Tags are defined in the "env" key at the top of this file.
- cd ../behat
- ./bin/behat --config behat.travis.yml --verbose --tags $BEHAT_TAGS

```

The `.travis.yml` file contains all of steps needed to run the test suite on Travis CI, and the `tests/travis-ci` folder contains assets and scripts that are needed for Travis CI integration. Different Drupal settings are required to run the tests on Travis CI, and so that settings file is placed in the travis-ci folder and copied to the right place during the .travis.yml script run.

# Optimizing Tests
TBD...

Remember not to name any testing module "_bundle" or else it appears in the bundle screen. https://github.com/CuBoulder/express/issues/1480 
