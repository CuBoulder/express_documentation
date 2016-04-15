We are using https://bitbucket.org/davereid/drush-patchfile to manage the patches applied to core and contrib.  This requires all developers working on Express to use a version of nglocal that has the Drush Patchfile command preconfigured or configure it in their own environment.

If you already have composer configured, you can install using

cd ~/.drush && composer require davereid/drush-patchfile:dev-master

You may need to drush cc before drush will reconzie the new command. Once the command is installed, you must configure the location of the patch.make relative to the Drupal root in your drushrc.php.

$options['patch-file'] = 'profiles/cu_fit/patches.make';

There are several useful commands in patchfile, but these are the most important to our workflow.

drush patch-add [project] [url of patch]

drush dl secure_permissions-7.x-2.x-dev --destination=profiles/cu_fit/modules/contrib
