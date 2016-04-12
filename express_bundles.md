Creating an Express Bundle

Module bundles are a key feature of the Express service.  It allows additional functional to be added to what is provided in Express Core by a site owner without the requiring the 'administer modules' permission.

They are

Bundles should do the following things:

1. Define Dependencies
2. Define Permissions using Secure Permissions hook
3. Define Inline Help
4. Any Additional CSS, js, and tpls*
5. Add Settings Links to admin/settings Under Bundle Section*
6. Install/breakup express_layout settings variables

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

Add Settings Links to admin/settings Under Bundle Section
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
