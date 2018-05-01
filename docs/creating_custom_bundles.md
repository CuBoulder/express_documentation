# Express Bundles

   - [What Are Bundles](#what-are-bundles)
     - [Bundles should do the following things](#bundles-should-do-the-following-things)
     - [Define Dependencies](#define-dependencies)
     - [Declare Requirements](#declare-requirements)
     - [Define Permissions](#define-permissions)
     - [Define Inline Help](#define-inline-help)
     - [Add Settings Links](#add-settings-links)
   - [Express Core Or Custom Bundle?](#express-core-or-custom-bundle)
   - [Creating Custom Bundles](#creating-custom-bundles)
     - [Code Is New](#code-is-new)
     - [Code Is Coming From Express](#code-is-coming-from-express)
   - [Creating Bundle Tests](#creating-custom-bundles)
   - [Tagging Custom Bundles](#tagging-custom-bundles)
     - [Creating A Tag](#creating-a-tag)
       - [Postman patch code example for tagging](#postman-patch-code-example-for-tagging)
     - [Labels](#labels)
       - [Postman patch code example for labeling](#postman-patch-code-example-for-labeling)
     - [Full Postman Create code example](#full-postman-create-code-example)
   - [Releasing Custom Bundle Code](#releasing-custom-bundle-code)
     - [Info File](#info-file)
       - [ERUS Module Update Status](#erus-module-update-status)
       - [Bundle Tag](#bundle-tag)
     - [How To Succesfully Update A Custom Bundle's Code](#how-to-succesfully-update-a-custom-bundles-code)

## What Are Bundles 

Module bundles are a key feature of the Express install profile.  It allows additional functional to be added to what is provided in Express Core by a site owner without the requiring the 'administer modules' permission.  Bundles are also used to enforce the state of site.  Profile Module Manager combines the dependencies of the profile, core, and enabled bundles and reports the site as being in a error state if anything else is enabled.

### Bundles should do the following things

1. [Define Dependencies](#define-dependencies)
2. [Declare Requirements of Profile Module Manager Install](#declare-requirements)
3. [Define Permissions](#define-permissions)
4. [Define Inline Help](#define-inline-help)
5. Any alters hook, CSS, js, and tpls (when the bundle is mainly used to add contib)
6. [Add Settings Links](#add-settings-links)
7. Install/breakup express_layout settings variables

### Define Dependencies
The Bundle should define both true Drupal enforced dependencies and optional-dependency for Profile Module Manager. These are defined in the Bundles .info file either using the standard Drupal dependencies[] = or the optional-dependencies[] =.  Optional dependencies can be enabled or disabled without triggering an error in Profile Module Manager.

Profile Module Manager does NOT chase the dependencies like the normal Drupal module enable.  All dependencies must be declared in the bundle's .info.

### Declare Requirements
See https://github.com/CuBoulder/express/blob/dev/modules/custom/cu_social_media_bundle/cu_social_media_bundle.info

```
bundle_cache_clear = 1
bundle_context_clear = 1
```

### Define Permissions

```
function cu_social_media_bundle_secure_permissions($role) {
  $permissions = array(
    'administrator' => array(
      'create any facebook_activity bean',
      'create any facebook_like_button bean',
      'create any social_links bean',
      'create any twitter_block bean',
      'delete any facebook_activity bean',
      'delete any facebook_like_button bean',
      'delete any social_links bean',
      'delete any twitter_block bean',
      'edit any facebook_activity bean',
      'edit any facebook_like_button bean',
      'edit any social_links bean',
      'edit any twitter_block bean',
      'view any facebook_activity bean',
      'view any facebook_like_button bean',
      'view any social_links bean',
      'view any twitter_block bean',
      'administer cu share',
    ),
    'anonymous user' => array(
      'view any facebook_activity bean',
      'view any facebook_like_button bean',
      'view any social_links bean',
      'view any twitter_block bean',
    ),
    'authenticated user' => array(
      'view any facebook_activity bean',
      'view any facebook_like_button bean',
      'view any social_links bean',
      'view any twitter_block bean',
    ),
    'content_editor' => array(
      'create any facebook_activity bean',
      'create any facebook_like_button bean',
      'create any social_links bean',
      'create any twitter_block bean',
      'delete any facebook_activity bean',
      'delete any facebook_like_button bean',
      'delete any social_links bean',
      'delete any twitter_block bean',
      'edit any facebook_activity bean',
      'edit any facebook_like_button bean',
      'edit any social_links bean',
      'edit any twitter_block bean',
      'view any facebook_activity bean',
      'view any facebook_like_button bean',
      'view any social_links bean',
      'view any twitter_block bean',
    ),
    'developer' => array(
      'create any facebook_activity bean',
      'create any facebook_like_button bean',
      'create any social_links bean',
      'create any twitter_block bean',
      'delete any facebook_activity bean',
      'delete any facebook_like_button bean',
      'delete any social_links bean',
      'delete any twitter_block bean',
      'edit any facebook_activity bean',
      'edit any facebook_like_button bean',
      'edit any social_links bean',
      'edit any twitter_block bean',
      'view any facebook_activity bean',
      'view any facebook_like_button bean',
      'view any social_links bean',
      'view any twitter_block bean',
      'administer cu share',
    ),
    'edit_my_content' => array(
      'view any facebook_activity bean',
      'view any facebook_like_button bean',
      'view any social_links bean',
      'view any twitter_block bean',
    ),
    'site_owner' => array(
      'create any facebook_activity bean',
      'create any facebook_like_button bean',
      'create any social_links bean',
      'create any twitter_block bean',
      'delete any facebook_activity bean',
      'delete any facebook_like_button bean',
      'delete any social_links bean',
      'delete any twitter_block bean',
      'edit any facebook_activity bean',
      'edit any facebook_like_button bean',
      'edit any social_links bean',
      'edit any twitter_block bean',
      'view any facebook_activity bean',
      'view any facebook_like_button bean',
      'view any social_links bean',
      'view any twitter_block bean',
      'administer cu share',
    ),
  );

  if (isset($permissions[$role])) {
    return $permissions[$role];
  }
}
```

### Define Inline Help
```
/**
 * Implements hook_express_help()
 *
 * Add contextual help for news functionality.
 *
 * @param $variables
 *   Array with information about help hooks
 * @return $variables
 */
function cu_news_bundle_express_help($variables) {

  $variables['node/add/article'][] = array(
    'title' => 'Adding Article Help',
    'short_title' => 'Article',
    'url' => 'http://www.colorado.edu/webcentral/tutorial/v2/add-article',
  );
  $variables['node/add/article-list-page'][] = array(
    'title' => 'Adding Article List Page Help',
    'short_title' => 'Article List Page',
    'url' => 'http://www.colorado.edu/webcentral/tutorial/v2/add-article-list-page',
  );
  $variables['block/add/article-grid'][] = array(
    'title' => 'Adding Article Grid Block Help',
    'short_title' => 'Article Grid Block',
    'url' => 'http://www.colorado.edu/webcentral/tutorial/v2/article-grid-block',
  );
  $variables['block/add/articles'][] = array(
    'title' => 'Adding Article List Block Help',
    'short_title' => 'Article List Block',
    'url' => 'http://www.colorado.edu/webcentral/tutorial/v2/article-list-block',
  );
  $variables['block/add/article-slider'][] = array(
    'title' => 'Adding Article Slider Block Help',
    'short_title' => 'Article Slider Block',
    'url' => 'http://www.colorado.edu/webcentral/tutorial/v2/article-slider-block',
  );

  return $variables;
}
```

### Add Settings Links 
to admin/settings Under Bundle Section
```
/**
 * Implements hook_menu().
 */
function people_content_type_menu() {
  $items = array();
  $items['admin/settings/people'] = array(
    'title' => 'People',
    'description' => 'Menu Options Related to Feedback',
    'position' => 'right',
    'page callback' => 'system_admin_menu_block_page',
    'access arguments' => array('administer express settings'),
    'file' => 'system.admin.inc',
    'file path' => drupal_get_path('module', 'system'),
    'weight' => 1,
  );
  $items['admin/settings/people/settings'] = array(
	'title' => 'People Filter Labels',
	'type' => MENU_NORMAL_ITEM,
	'page callback' => 'drupal_get_form',
	'page arguments' => array('people_content_type_admin_settings_form'),
	'access arguments' => array('administer express settings'),
  );
  return $items;
}
```


## Express Core Or Custom Bundle?

Determining if a bundle belongs in Express Core or should be defined as an optional Express+ bundle can be difficult.  

* **Does the bundle need to be updated more often than the Express release cycle?** - Bundles that require more frequent updates including bundles we are beta testing should be Express+. The number of bundles maintained as Express+ in D7 may also grow as we are likely to do fewer D7 releases in 2017.
  
* **Is this functionality we are comfortable allowing every site to enable?** - If the functionality could potentially cause a resource issue (things like the number of open curl requests or poorly performing queries), we should control/monitor how many sites have this enabled. 

* **Does the bundle entice users to enable it on a production site before they discover a commercial service is required and/or some other subjective review?** - This is just a waste of resources. It can add to the overhead of every page load, complicate the UI and the time it takes to run unnecessary update hooks.

* **Does the bundle need to be deployed on Pantheon?** - Currently only bundles included in the Express profile are available on Pantheon. All other bundles need to be manually added and updated. This is an issue because our internal bundle repos don't have a mechanism of informing sites about new versions/security updates.

## Creating Custom Bundles

In order to keep the Express core repository as lean as possible, addtional bundles for specific functionality that might 
not be needed on all sites are now placed in their own repositories. In order to create your custom bundle, you need to 
consider where the code is coming from. 

### Code Is New

- Add a license. You can copy the one from the main Express repo.
- Add a readme pointing back to this file and something about what the bundle does. If there are tutorials for it, link to those as well.
- Write tests before you release anything.

### Code Is Coming From Express

- Add a license. You can copy the one from the main Express repo.
- Add a readme pointing back to this file and something about what the bundle does. If there are tutorials for it, link to those as well.
- Copy any tests over and write new ones.
- Make sure to plan for deprecating the code in the Express repo and enabling the bundle on all sites using it in production.

## Creating Bundle Tests

Please be nice and do this. For easier browsing, the instructions have been moved to: https://github.com/CuBoulder/express_documentation/blob/master/docs/creating_bundle_tests.md

## Tagging Custom Bundles

Profile Module Manager supports two types of bundles: core and admin.

To use PMM's bundle pages, you can use one of two options:
  - Machine Name
    - Module's machine name ends in "_bundle" = Core Bundle page
    - Module's machine name ends in "_admin_bundle" = Admin Bundle page
    
Our CU Profile Module Manager adds three additional bundle pages:
  - Add-on Bundles
  - Request Bundles
  - Beta Bundles
  
There's only **one** way to display a bundle on any of the pages from CU-PMM:
  - Add a label and tag to the Atlas code asset for the bundle (see: "Creating A Tag" below)
  
### Troubleshooting Bundle Page Display Issues

If you do not see your bundle showing up at all, check Atlas for proper lable and tag--it must have both.

If your bundle shows up in the wrong bundle page, check Atlas for multiple versions of the code asset and ensure proper labels/tags are present.

If your bundle shows up in both core/admin **and** either addon/request/beta, then the code is wrong and needs fixing...

### Creating A Tag

Tags are setup as the following:
  - Core bundles page tag is "core_bundles"
  - Add-on bundles page tag is "addon_bundles"
  - Request bundles page tag is "request_bundles"
  - Beta bundles page tag is "beta_bundles"
  - Admin bundles page tag is "admin_bundles"

You should use the Lil' Shrugger application hosted on Pantheon to add tags to code assets. https://github.com/CuBoulder/lil_shrugger/wiki/Code

If the bundle does not have a bundle_group assigned or an atlas tag, it will not show up on any of the listing pages.

### Bundle Lifecycle

![Evaluation -> Protype -> Beta -> Refine -> Public -> Deprecated](https://github.com/CuBoulder/express_documentation/blob/master/docs/bundle_lifecycle.png)

All bundles should start off being tagged as "beta_bundles". The developer's responsibility is to create the code, and then the QA and support teams will let developers know when the bundles need to be moved to "addon_bundles" or "request_bundles". For "admin_bundles" a developer can make that call and add the tag. 

#### Postman patch code example for tagging
```
{
  "meta":{
    "tag": ["addon_bundles","anothertagforexample"]
  }
}
```

Tags can also be edited and created for code assets using the hosted version of Lil' Shrugger on Pantheon. 

### Labels

Labels are the user-friendly title of the module. This will appear in the card as the title on the enable bundle list pages. Any time the name of the module appears to the user, it should use the label atlas field instead of the name atlas field.

#### Postman patch code example for labeling
```
{
  "meta":{
    "label": "Readable Module Name"
  }
}
```

Labels can also be edited and created for code assets using the hosted version of Lil' Shrugger on Pantheon. 

### Full Postman Create code example
```
{
  "git_url": "git@github.com:CuBoulder/cu_alumni_bundle.git",
  "commit_hash": "27a9d2738b553170dc3c6bebb31d9abc14c5a6df",
  "meta":{
    "version": "7.x-1.1",
    "code_type": "module",
    "name": "my_bundle",
    "label": "Readable Module Name",
    "tag": ["addon_bundles","anothertagforexample"],
    "is_current": true,
    "dependency": ["588b90a736447521fd349bcc","588b90a036447521fd349bc2"]
  }
}
```

Code assets can also be fully created or edited using the hosted version of Lil' Shrugger on Pantheon. 

## Releasing Custom Bundle Code

### Info File
There are several keys in the the bundle's info file that need to be considered when creating or updating bundle code. The ERUS update and bundle tags need to be included in every bundle in order for that bundle to be deployed correctly. 

#### ERUS Module Update Status
There are many custom bundles, a.k.a plus bundles, that exist in the Express ecosystem. We make issues, plan sprints, and merge code in, but we currently don't have any good process for releasing the code once it's ready to deploy. Previously, a developer had to be in the know as to when new code should be pushed to production and which sites needed updating with the new code.

Taking into account the fact that Drupal already has a mechanism in place to check for code updates, we will now utilize this mechanism so that individual sites can report when they need their code updated instead of needing to have a developer remember where new code needs placed. 

For Drupal modules living on drupal.org, the core Update module takes care of checking for new code. However, for our Drupal modules that live on GitHub, we have no way for an individual site to check for new code. Luckily, Stanford Web Services has come to the rescue with their [External Repository Update Status module](https://github.com/SU-SWS/erus), or ERUS.

With three additional lines to a bundle's info file, the ERUS module checks for updated code and displays out-dated modules via the Drupal GUI or on the commandline via Drush. A "project status url" key needs to be added to every module's info file you want to keep updated. 

```
name = Your Module
description = "A description of your module."
package = My-modules
core = 7.x
version = 7.x-1.0
project = your_module
project status url = https://github.com/org-account/your_module
```

Along with the "project status url" key, the "version" and "project" keys are neccessary for ERUS to give you an accurate report of which modules need updated.
- The "project" key should be the machine name of your module which should match the GitHub repository's name.
- The "version" key should match the Drupal version naming convention, e.g. "7.x-1.0". Every time you update a bundle a version, you need to up the version number and make a full release for the update check to work. 


### How To Succesfully Update A Custom Bundle's Code

1. Finish your sprint and prepare the repository for a release like you normally would. 
2. Make a tag in the Drupal format for modules, e.g. "7.x-1.1".
3. Make a release in GitHub using the tag from step two. 
4. Download the release files, e.g. "module-7.x-1.1.tar.gz".
5. Run md5 on your downloaded file, e.g. "md5 module-7.x-1.1.tar.gz".
6. Edit your release notes and add the checksum, e.g. "md5: ceb3af66ab78e65def488a175a54d5d4".
7. Optional & Broken - Add "Release type:" to the release notes with a value of "Bug fixes", "New features", or "Security update". The default release type is "Bug Fixes". ex. "Release type: New features" 
8. Go to "admin/reports/updates" to check if your custom module reports needing an update. 
