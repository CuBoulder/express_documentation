# Converting A Drupal Module To Backdrop

In the process of evaluating Backdrop as a migration target for the Express service, going through the process of converting modules is instructive on Backdrop works and elucidates any potential pain-points. Backdrop has documented the process of converting Drupal modules (https://api.backdropcms.org/converting-modules-from-drupal), but it is unclear to me how updated those docs are. Hence, this guide.

## Search Modules And Communicate Intent
The first step is to communicate your intention to convert a module: https://api.backdropcms.org/converting-from-drupal

In the future, there should be a step to see if the Backdrop contrib repository has a similar module that can be modified for use instead of creating a whole new module. For example, there are four sitemap modules that I can see on https://github.com/backdrop-contrib. Even though I ported the XMLSitemap module, I wouldn't reccomend using that module over one of the "simple" ones. It is way too big, and I understand little of the codebase that has probably diverged from the D7 version.

I'm porting the User External Invite module, and so I searched for "invite" and "user" in their contrib organization. I only saw https://github.com/backdrop-contrib/autoassignrole, which is taken from the viewpoint of adding local users to the site, whereas the Invite module has the viewpoint that the user is already created but doesn't have any roles. I will probably file an issue about the possibility of adding functionality for granting roles via emails, but I will continue with porting the module for now.

There are several Express modules on drupal.org that are good starting points. Those modules will be listed in issues that reference this doc. You should follow the steps in the communicate section of the Backdrop help doc.

For me:
- Issue on d.o: https://www.drupal.org/project/user_external_invite/issues/2986102#comment-12689758
- Issue on Backdrop contrib: https://github.com/backdrop-ops/contrib/issues/271

## Cleaning Up Modules Before Porting

A bunch of refactoring stuff probably should be done to your modules first, but there are several scripty ways of just converting Drupal methods to use a Backdrop replacement as well as replacing the config system, meaning mainly `variable_get() variable_set()`. Those two things usually get you a long way in converting Drupal modules to Backdrop.

We will skip the d.o issue cleanup for now since that can be a bit overwhelming.

## Spin Up Site On Pantheon

Before you even starting looking at the code, you should get a good idea of how Backdrop works. Our friends at Pantheon have made this really easy to do, and I have created a Pantheon site with multi-dev environments for each dev team member.

Multidev gives you your own branch as well as environment. You can set the code method to "SFTP" instead of "Git" to edit code on the server, but it is reccomended to develop locally via Git.

I would create pages, make layouts, scan the module list, etc. on your Pantheon site to see how Backdrop is similiar yet different from D7.

## Transfer Site Locally 

Once you have played around with your site, you can export the database and files to import them locally. The files are really important in Backdrop vs. Drupal 7 since configuration and some block content is now stored in JSON files.

Pantheon has really nice backup tools listed under "Database/Files -> Export". Use those to export the database and files from your multidev environment, if you want, but it is easier to just forgo trying to sync your sites and just modify code and push that up. Syncing configuration can be explored in your module conversion or later on once you have the basics down.

Lando has a Backdrop recipe that can be used to host sites locally. They actually have a Pantheon integration recipe, but I've never used it and it might complicate your investigations. Plus, the Backdrop recipe needs testing to make sure it is accurate for other users not interested in hosting on Pantheon. Install Lando first, if you haven't already: https://docs.devwithlando.io/installation/installing.html

Once you are in the root of your project, all you need to do is type `lando start` in your terminal and you should see some URLs to go to. The SSL vanity URL is working for me, https://expressbackdrop.lndo.site/, but your milage may vary. The documentation for the recipe is pretty decent: https://docs.devwithlando.io/tutorials/backdrop.html

Once you click install, you'll notice that the site just starts installing without you having to enter database credentials or anything like that. It is accomplished by using a database protocol URL `$database = 'mysql://user:pass@localhost/database_name';` instead of an array passed back to Drupal.

### settings.local.php

You will have to add a `settings.local.php` file for your files settings. Backdrop does pretty much the same thing as Drupal 8 and creates a config folder in the files directory like `config_940cb2dde1a145fd829660833059657e`. The hashes have to match or the site complains about configuration. This is why syncing your site back and forth to Pantheon might not be a good idea to look into for now.

Add a `settings.local.php` file in the root with the following:

```php
<?php

/**
 * @file
 * Local configuration for your Backdrop site.
 */

$config_directories['active'] = './files/config_12345678/active';
$config_directories['staging'] = './files/config_12345678/staging';
```

After the Lando install, your `settings.php` file should have those `$config_directories` lines in it. Copy them to the local settings file and revert `settings.php` so universal changes can be stored there while you can experiment with local settings on your personal site, i.e. don't commit changes to the `settings.php` file for now.

## Start Converting A Module

You can follow https://api.backdropcms.org/converting-modules-from-drupal to start converting a module. No surprises here. There are also example blog posts to look at: https://www.lullabot.com/articles/porting-drupal-7-modules-to-backdrop. If you want to start a blog post about your journey, go ahead. The blog needs fresh content.

### Running The Tests

A lot of modules will have SimpleTests written for them. You can run those via the Backdrop admin UI on "admin/config/development/testing" once you turn on the Testing module. Running the tests can identify problems in the code base.

### Removing Drupal Compatibility Layer

Backdrop has a Drupal compatibilty layer in the 1.x branch that will be removed by 2.x. That is why it is important to remove that layer as you convert a module. In PHPStorm, I can see which functions are deprecated. I think this is a default PHPStorm feature, but it puts a line through all deprecated functions.

...image from PHPStorm...

If you don't have an IDE that can do this, usually you can search for "Drupal" in your module's codebase. You can also look at "core/includes/drupal.inc" to see the replacement functions. 

### Reporting Any Issues You See In Core/Adding To Docs

As I'm going through the Backdrop docs and trying some things out on my local site, I've seen some issues and wanted to chime in on PHP version support. Backdrop has a separate repo for issues apart from the codebase: https://github.com/backdrop/backdrop-issues. 

You also might see something that would be a good addition or change to a doc. I saw mention of using Lando locally and then an issue about adding that to Backdrop core. The solution was to add a page on the main site, https://backdropcms.org/try-backdrop, but I think users might not get to that page since "Download Backdrop" just starts a download. My suggestion would be to have the "Download Backdrop" link go to https://backdropcms.org/try-backdrop. They have a repo for their main site at https://github.com/backdrop-ops/backdropcms.org/issues that I will file an issue in.

## Merging Into The Pantheon Master Branch

Occasionally there will be code updates that you'll want to merge into the master branch so others can pull those in. I think it'd be nice to make the master branch of the Pantheon site what Express would like in Backdrop. Once you finish a module, you can add it to the master branch. 

On the "Code" tab in a Pantheon site dashboard, it will list any commits from the master branch that you can look at and choose to merge into your codebase. Ideally, you will merge these in as they come so no merge conflicts happen. Each merge should be about a contrib module or set of modules so hopefully there won't be many conflicts.  
