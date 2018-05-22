# Writing Tests

   * [Writing Tests](#writing-tests)
      * [Directory Structure](#directory-structure)
      * [Suites](#suites)
      * [Features](#features)
      * [Scenarios](#scenarios)
      * [Step Definitions](#step-definitions)
      * [Scenario Outlines](#scenario-outlines)
      * [Tags](#tags)
   * [Running Tests](#running-tests)
   * [Debugging Tests](#debugging-tests)
   * [Travis CI Integration](#travis-ci-integration)
   * [Sauce Labs Integration](#sauce-labs-integration)
   * [Guidelines When Writing Tests](#guidelines-when-writing-tests)
   * [Optimizing Tests](#optimizing-tests)
   * [Troubleshooting](#troubleshooting)
      * [Naming](#naming)
      * [Pause Tests On Sauce To Debug](#pause-tests-on-sauce-to-debug)


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

To run Behat tests, you must be in the root folder where the `behat.yml` configuration file is located. To run the whole test suite you would just type `./bin/behat` into your terminal. You can export the path to the binary to run behat from anywhere, `behat`, but since the context files are usually included with the binary, that step is overkill if you only use Behat on this project. If you only wanted to run a particular test suite, you can type `./bin/behat --suite=name_of_suite`. To run a particular tag you can type `behat --tags @tag_name`. There are also a couple of conditionals that only matter when tests are run on Travis CI.

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

For more instructions on running tests locally: https://github.com/CuBoulder/express/blob/dev/tests/behat/README.md

# Debugging Tests
Occasionally, you'll want more information from the test run for debugging purposes. In that case, you can have Behat post output in verbose mode `./bin/behat --verbose`. The `.travis.yml` file ends up running tests in verbose mode. There are many options you can pass to the Behat executable, and you can see some of those in the `behat-tests.sh` file. 

```yaml
echo "Running Express headless tests..."
./bin/behat --stop-on-failure --strict --config behat.travis.yml --verbose --tags ${EXPRESS_HEADLESS_BEHAT_TAGS}
```

`behat --help` will give you all of the options you can use potentially for debugging.

Another useful debugging tool is to use step definitions to capture infomation you can't see while the test is running. There are a couple of step definitions that you can use for debugging purposes.

```gherkin
# Checks the previous response for a string.
Then the response should contain "<h1>Example Title</h1>"

# Breaks the current test until a key is pressed.
And I break

# Prints out the last response.
Then print last response

# Print current URL. You can see this in --verbose mode.
Then print current URL
```

## Sauce Labs

With the Sauce Labs integration, you can watch tests that fail and take over interacting with the site before the failure occurs. You can add `And I break` to formally do so or just click in the broswer window of the video recording while the test is running to take control. The Sauce Labs connection will time out and so you don't have much time to look at what's going on.

Adding a `@javascript` tag to a test, even if it doesn't need one, can be a greate way to run that test in isolation.

## Local Debugging

You should ALWAYS start debugging a test locally. You can use information in the Travis logs, but blindly making a change and then rerunning via Travis takes a lot of time. There are instructions on how to set up testing locally here: https://github.com/CuBoulder/express/blob/dev/tests/behat/README.md

![screen shot 2018-05-22 at 1 49 13 pm](https://user-images.githubusercontent.com/3640707/40387008-83de345e-5dc8-11e8-8c5f-4e09a5e31ecb.png)

A test failure on Travis CI will look like the screenshot above. The `--verbose` option shows you a lot of information. 
- The place in the code where the test failed: `features/Express/aaa_role_sync/aaa_role_sync.feature:7`
- The exception thrown at the error: `Form field with id|name|label|value "edit-secure-permissions-sync-roles" not found.`
- The response code: `HTTP/1.1 200`
- The URL where the test failed: `http://127.0.0.1:8057/admin/config/people/secure_permissions`
- The driver being used: `GoutteDriver`
- The method being called on failure: `FeatureContext::checkOption()`

At the end of each test run, you will see a report of passed, failed, and skipped tests. You also might see an "undefined" number of steps. On Travis CI, the tests will hang at that point since you press `1` to automatically generate missing step definitions. 

Once you choose to generate them, you will get stubs to put into `FeatureContext.php`. Or you can see that a commonly used step was misspelled, grep for that scenario, and fix the typo. 

![screen shot 2018-05-22 at 1 55 08 pm](https://user-images.githubusercontent.com/3640707/40386992-79fb2f0a-5dc8-11e8-88e8-fd05f6bb01d3.png)

Once you fix the tests, make sure to use the commit flags of `===build and ===js` to run all of the test suite before merging a PR into the default branch.

# Travis CI Integration
The Express Behat test suite is run via Travis CI on every commit attached to a PR in the Express Github repository and also run when a PR is merged into dev. When the test run completes, a pass or fail notice will be sent back to the particular commit or PR and you will be able to see this on the PR page.

![screen shot 2018-03-29 at 12 50 05 pm](https://user-images.githubusercontent.com/3640707/38108002-cbcd27cc-3350-11e8-8a60-b174e1fe75a2.png)

Currently, there are two test runs performed in parallel: one without JavaScript tags `@~exclude_all_bundles&&~broken&&~javascript` and one with JavaScript tags `@javascript&&~exclude_all_bundles&&~broken`. You can use commit messages to trigger different types of test runs on Travis.

```bash
# Add "===js" to you commit message on Travis to run JS tests as well as headless tests.
./bin/behat --config behat.travis.yml --verbose --tags '~@exclude_all_bundles&&~@broken&&@javascript'

# Add "===build" to build Express on every commit attached to a PR.
drush si express --db-url=mysql://root:@127.0.0.1/drupal --account-name=admin --account-pass=admin --site-mail=admin@example.com --site-name="Express" --yes

# Otherwise, the site will be imported from an export of the site after install.
drush sql-cli < $HOME/cache/express.sql

# Add "!==express" to not run Express Behat tests on bundle repos.
# Otherwise, both the Express tests and the bundle tests will be run for your commit.

```

When a PR is merged into the dev branch, a new site will be built as well as havign the JS tests run.

The `.travis.yml` file contains all of steps needed to run the test suite on Travis CI, and the `tests/travis-ci` folder contains assets and scripts that are needed for Travis CI integration. Different Drupal settings are required to run the tests on Travis CI, and so that settings file is placed in the travis-ci folder and copied to the right place during the `.travis.yml` script run.

# Sauce Labs Integration

There are two drivers that can be used to run the Behat features: Goutte and Selenium. The Goutte driver is "headless" and performs requests as you would see while using the command line. The Selenium driver connects to an actual browser and sends instructions that the browser implements. The Selenium driver is only used when the `@javascript` tag is placed on a feature or scenario. All of the tests that don't include that tag will be run by the Goutte driver.

Originally, the Selenium server was setup on Travis CI; however, due to versions of browsers changing and trying to keep that in sync with Selenium server versions, the tests broke frquently only from Selenium server configurations. There were also issues with PHP versions and Behat dependencies dropping PHP 5.3 support, which we ran until recently. Even though some of these issues have been resolved, Sauce Labs was chosen as a Selenium server testing provider for ease of use.

There are already instructions on running tests locally that explain how the Express site connects to Sauce Labs durning a test run, https://github.com/CuBoulder/express/tree/dev/tests/behat. Travis CI has an easy way of starting up the "Sauce Connect Proxy" binary, and setup there will be different than manually starting the proxy locally.

# Guidelines When Writing Tests

- **No general wait steps ever.** Pick an element on the page and wait for that to appear using `I wait for the <element> element to appear` step definition. The only exception are things like the count-up shortcode that are time-based.
- **Scenario Outlines are to be used when there are multiple variables that need tested.** If only one variable is used then it slows the test down by repeating steps before the variable is used. Add multiple lines that map to each variable in that case instead of using Scenario Outlines.
- **No need for `@api` tag.** This was used in the past for a driver that integrated with Drupal, but that driver is no longer used.
- **Think about how the test works when no bundles are enabled.** Example: People filter labels in the Express feature folder. That won't work if the People Bundle isn't enabled. As bundles are moved out of the Express repository, tests need to take that into account.
- **Ask for content you need to be there for test runs rather than creatign side effects.** While it can be tempting to create content and then use that in a future scenario, changes to the test suite can render those dependent tests useless later on. It is better to think about content you need for a test but don't need to test it's creation, e.g. adding a block row with pre-defined blocks, and file an issue in the Express repo for a developer to seed that content.
- **If you need to use content in a future scenario, use a path alias.** Creating a node early on in the feature file with a unique alias, e.g. "my-test-needs-01234", makes it nearly impossible to create alias collisions with other nodes and makes it really easy to target in tests later on.

# Optimizing Tests

@todos:
- Optimize memory utilization. Travis has I think 4GB of memory that can be used with PHP, OpCache, MySQL, and maybe even Memcache if that were setup again.
- Expand the test matrix. Right now only two different test runs are triggered on every PR commit, but splitting up the JS test run would be the most beneficial addition to the test suite matrix. The JS test take far longer to run than ones using the default Goutte driver.

# Troubleshooting

### Naming
Remember not to name any testing module "_bundle" or else it appears in the bundle screen. https://github.com/CuBoulder/express/issues/1480 

### Pause Tests On Sauce To Debug
If your test is failing, you can temporarily add a `@javascript` tag to debug it on Sauce Labs. The `And I break` step definition will pause the test and then you can take over the site to see what's going on. Sauce Labs also records screenshots of tests runs that are held for 30 days. 
