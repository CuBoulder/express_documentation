#Express Bundles

Module bundles are a key feature of the Express install profile.  It allows additional functional to be added to what is provided in Express Core by a site owner without the requiring the 'administer modules' permission.  Bundles are also used to enforce the state of site.  Profile Module Manager combines the dependencies of the profile, core, and enabled bundles and reports the site as being in a error state if anything else is enabled.

###Bundles should do the following things:

1. [Define Dependencies](#define-dependencies)
2. [Declare Requirements of Profile Module Manager Install](#declare-reqirements)
3. [Define Permissions](#define-permissions)
4. [Define Inline Help](#define-inline-help)
5. Any alters hook, CSS, js, and tpls (when the bundle is mainly used to add contib)
6. [Add Settings Links](#add-setting-links)
7. Install/breakup express_layout settings variables

###Define Dependencies
The Bundle should define both true Drupal enforced dependencies and optional-dependency for Profile Module Manager. These are defined in the Bundles .info file either using the standard Drupal dependencies[] = or the optional-dependencies[] =.  Optional dependencies can be enabled or disabled without triggering an error in Profile Module Manager.

Profile Module Manager does NOT chase the dependencies like the normal Drupal module enable.  All dependencies must be declared in the bundle's .info.

###Declare Requirements
See https://github.com/CuBoulder/express/blob/dev/modules/custom/cu_social_media_bundle/cu_social_media_bundle.info

```
bundle_cache_clear = 1
bundle_context_clear = 1
```

###Define Permisions

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

Define Inline Help
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

###Add Settings Links 
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
