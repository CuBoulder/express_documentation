# Coding Standards and Best Practices

* Don't use hook_form_alter when targeting a specific form. Use https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_form_FORM_ID_alter/7
* Coding standards are in theory the same ones as on https://www.drupal.org/docs/develop/standards, but in practice they come from the .codeclimate.yml file in the root of the Express repository. That YAML file is run on every PR and CodeClimate reports back whether it thinks any coding standards are being violated. No PR should be merged in with a failing CodeClimate report in theory, but in practice that ends up not always being the case :( The answer is always to fix your report or modify that YAML configuration file to remove errant checks. 
