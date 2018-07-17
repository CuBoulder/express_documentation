# Converting A Drupal Module To Backdrop

In the process of evaluating Backdrop as a migration target for the Express service, going through the process of converting modules can be instructive to how Backdrop works and elucidate any potential pain-points or deal-breakers. Backdrop has documented the process of converting Drupal modules (https://api.backdropcms.org/converting-modules-from-drupal), but it is unclear to me how updated those docs are.

## Search Modules And Communicate Intent
The first step is to communicate your intention to convert a module: https://api.backdropcms.org/converting-from-drupal

In the future, there should be a step to see if the Backdrop contrib repository has a similar module that can be modified for use instead of creating a whole new module. For example, there are four sitemap modules that I can see on https://github.com/backdrop-contrib. Even though I ported the XMLSitemap module, I wouldn't reccomend using that module over one of the "simple" ones. 

I'm porting the User External Invite module, and so I searched for "invite" and "user". I only saw https://github.com/backdrop-contrib/autoassignrole, which is taken from the viewpoint of adding local users to the site, whereas the Invite module has the viewpoint that the user is already created but doesn't have any roles. I will probably file an issue about the possibility of adding functionality for granting roles via sending emails with token login links but continue with the example for now.

There are several Express modules on drupal.org that are good starting points. Those modules will be listed in issues that reference this doc. You should follow the steps in the communicate section of the Backdrop help doc.

For me:
- Issue on d.o: https://www.drupal.org/project/user_external_invite/issues/2986102#comment-12689758
- Issue on Backdrop contrib: https://github.com/backdrop-ops/contrib/issues/271

## Cleaning Up Modules Before Porting

A bunch of refactoring stuff probably should be done to your modules first, but there are several scripty ways of just converting Drupal methods to use a Backdrop signature as well as replacing the config system. Those two things usually get you a long way in converting Drupal modules to Backdrop.

We will skip the d.o issue cleanup for now since that can be a bit overwhelming.

## Spin Up Site On Pantheon

Before you even starting looking at the code, you should get a good idea of how Backdrop works. Out friends at Pantheon have made this really easy to do, and I have created a Pantheon site with multi-dev environments for each team member.

Multidev gives you your own branch as well as environment. You can set the code method to "SFTP" instead of "Git" to edit code on the server, but it is reccomended to develop locally.

I would create pages, make layouts, scan the module list, etc. to see how it is similiar yet different from D7.

## Transfer Site Locally 

Once you have played around with your site, you need to export the database and files to import them locally. The files are really important in Backdrop vs. Drupal 7 since configuration and some block content is now stored in JSON files.

## Start Converting A Module
