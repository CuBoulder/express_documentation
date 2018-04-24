
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

Now that you've written some tests and ran them successfully locally, we can get on with this show...If you haven't done either of those things, follow the steps mentioned in the previous links.

GitHub has a lot of integrations with external services that are triggered by Git webhooks. For example, every time someone pushes to a branch, you can setup a webhook to do pretty much anything. GitHub uses webhooks to make calls to external services when commits are pushed to Express branches. Currently, Express uses Travis CI to run Behat tests and CodeClimate to statically analyze code.

### Travis CI

The `.travis.yml` file is where all the magic happens. You can take a sample template from https://github.com/CuBoulder/cu_digital_campaign_bundle/blob/dev/.travis.yml and copy it into the root of the bundle repo. You will need to make some changes before attempting to run any tests.

#### Add Integration On GitHub And Travis CI

The tests will not run automatically. You need to enable the integration on both GitHub and Travis CI. 


Each repo has a settings tab. If you can't see that tab at the root of a repo, e.g. https://github.com/CuBoulder/cu_digital_campaign_bundle/, then you can't add any integrations. Please contact a team member to sort out repo permissions.

If you do see "Settings", you need to go to "settings/installations" in the repo and you'll see a screen that looks like...

...add pic of integration screen

You can choose "Travis CI" from the select list. I thought you had to do more, but that is only if you want Travis to do something on GitHub after the test run completes.

Now on Travis, go to your profile page, e.g. https://travis-ci.org/profile/alexfinnarn, and you'll see a list of repositories with which you have access to enable integrations. You may have to click on the CU Boulder organization, but then you should see a toggle for the bundle you are adding tests to. Once the toggle is set, you are good to go.

#### Sauce Labs Environmental Variables

Sauce Labs is used to run the JavaScript Behat tests, as opposed to the regular headless Behat tests. To make the integration with Sauce Labs work, you need to enter `SAUCE_USERNAME` and `SAUCE_ACCESS_KEY` on the Travis settings page, e.g. https://travis-ci.org/CuBoulder/cu_digital_campaign_bundle/settings. 

#### Default Branch

```yaml
branches:
  only:
    - dev
```

For the Express repo, the default branch is `dev`. This means that GitHub will automatically assume you want to merge into the dev branch when you open a pull request, a.k.a PR. Git starts out with only a `master` branch when a repository is initalized but no `dev` branch. 

CU Boulder [follows a version of Gitflow](https://github.com/CuBoulder/express_documentation/blob/master/docs/git_strategy.md) that each bundle should also follow. You may need to create a `dev` branch and set it to be the default branch on GitHub. If you can't do that for some reason, change `dev` to your branch of choice where you see the YAML above in the `.travis.yml` file.

Travis CI will only check the default branch when attempting to run tests. So, if you don't see any of your tests running initially, check and make sure you have a PR into the default branch.

#### Bundle Variables

```bash
  export BUNDLE_NAME="cu_digital_campaign_bundle"
  export ADD_MODULES="cu_ab_test cu_dc_tests"
  export EXPRESS_JS_BEHAT_TAGS="~@exclude_all_bundles&&~@broken&&~@bundle_conflict&&@javascript"
  export EXPRESS_HEADLESS_BEHAT_TAGS="~@exclude_all_bundles&&~@broken&&~@javascript"
  export BUNDLE_BEHAT_TAGS="~@exclude_all_bundles&&~@broken"
```

There is a section in the `.travis.yml` file where variables are exported to be used in other scripts. All of those variables can be changed, but you will always need to modify `BUNDLE_NAME` and `ADD_MODULES` if you have modules that are not installed by default but must be enabled for tests. It is often a good idea to "seed" the database with some content that relates to a test you want to perform rather than always manually creating it.

For the Behat tags, you might encounter some Express tests that fail when the bundle is enabled but work fine when only Express core is installed. You can manually exclude those tags from the `EXPRESS_HEADLESS_BEHAT_TAGS` and the `EXPRESS_JS_BEHAT_TAGS` or choose to add a tag to the Express repo.

For example, when I added tests to the Campus Map Bundle, most tests looking for content on the front page failed. So, instead of excluding all of those tags related to the tests, I added a `@testing_frontpage` tag to tests that...tested the front page content. My reasoning was that other tests could target that tag either for exclusion or inclusion. 

Originally, I was going to add a `@campus_map_conflict` tag to those tests, but that doesn't tell you anything about the current test. Tags should always describe the scenario and not how that scenario plays into other contexts. It makes sense to me to declare that a test touches the front page but not that it conflicts with other code. Your milage may vary...but do what makes sense and ask teammates when you are unsure of which way to go.


### CodeClimate
