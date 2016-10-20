# Pantheon Profile
There is a separate version of Express that Pantheon users can pull in to start an Express site.

## Pantheon drop stuff...

# Using Drush
You can use drush locally to connect to your Pantheon site. Using drush in this way is very useful for quick debugging when the web interface is not working or is not returning good/enough information. To access your site remotely via drush, you'll have to download your drush aliases file from your account dashboard. Then, you can access drush normally, but you have to follow the pattern below. 

    drush @site-name.dev status --strict=0
    
The first part "@site-name.dev" directs drush to perform actions on the dev environemtn of your site. The "--strict=0" option allows you to run commands even when warning messages are generated. In my experience, a command won't execute or return you any feedback if that option is not present.   

# Site Audit Report
Pantheon has a nice feature where you can check best practices on a site from the Pantheon dashboard. There are several checks that can't be fixed on an Express site. So, Pantheon has some variables excluding these checks that can be placed in settings.php.

    $conf['site_audit']['opt_out']['ExtensionsUnrecommended'] = TRUE;

A list of the checks that can be added as exclusions exists here: [list of checks](http://cgit.drupalcode.org/site_audit/tree/Check).
To add a check, you combine the name of the parent directory and then the name of the PHP check file. So from the example above, "Extensions" is the parent directory and "Unrecommended" is the name of the PHP file in that directory. These checks map to the site report more or less but not word-for-word.

The site audit report uses drush to run its checks, but this means that some environmental variables aren't included in that runtime. It is reccomended to use the $_ENV superglobal instead of the $_SERVER super global. So, instead of using:

    if (isset($_SERVER['PANTHEON_ENVIRONMENT'])) {
      .....
    }

it is better to use:
    
    if (isset($_ENV['PANTHEON_ENVIRONMENT'])) {
      .....
    } 
