# CU_profile_module_manager

## Tags

Tags are setup as the following:
  - Core bundles page tag is "core_bundles"
  - Add-on bundles page tag is "addon_bundles"
  - Request bundles page tag is "request_bundles"
  - Beta bundles page tag is "beta_bundles"
  - Admin bundles page tag is "admin_bundles"

Bundle groups in info files (bundle_group = 'admin_bundles') mimic atlas tags exactly.

Only use bundle groups if you need to move a bundle out of the "Core bundles" page. For example, if the "Chemistry titles" bundle is displaying under "Core bundles" and is tagged in atlas as "addon_bundles"--it will display on both pages. In order to have it only display on the Add-on bundles page, we need to add a bundle_group of "addon_bundles" to its info file.

If the bundle has no "bundle_group" assigned in the info file, it will show up in the "Core bundles" page. This is to maintain original usage of "bundle" at the end of the module's filename/machine name as the means of populating the profile_module_manager list page.

If the bundle does not have a bundle_group assigned or an atlas tag, it will not show up on any of the listing pages.

### Postman patch code example
`{
  "meta":{
    "tag": ["addon_bundles","anothertagforexample"]
  }
}`

## Labels

Labels are the user-friendly title of the module. This will appear in the card as the title on the enable bundle list pages. Any time the name of the module appears to the user, it should use the label atlas field instead of the name atlas field.

### Postman patch code example
`{
  "meta":{
    "name": "Readable Module Name"
  }
}`
