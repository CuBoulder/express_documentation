## When to Use Entity vs. Node?

We introduced custom entities with express_layout in Express 2.0.  Since then, we created many more entities and have established a pattern of using entity for any non-authored content. This includes things like tracking pixels and courses. We try to limit node content types to more traditional "page like" content where things like authoring and publishing status are important. 

It is more work to define the UI for a custom entity, but that content also isn't cluttering the default admin/content display. Entities also don't use node hooks and so are a cleaner way of adding code to Drupal. When developing, entities should be thought of first and then switch to a node when user interaction comes into play. 

Also to consider is the Drupal contrib ecosystem. Contrib modules tend to use the node entity type since it is used so often and is in core. If your code is going to rely on a lot of contrib code, then that might be a reason to consider using nodes. 

## When to Use Features

Features is an excellent prototyping tool, but most parts of the Feature export can be moved up a level into the module code making it easier to capture customizations or extension of initial functionality. Defining a node type and fields with a Feature is a great example. Capturing that in a Feature makes it more difficult to add additional fields to that node type with another Feature or custom module.

The D7 version of Express is a mix of Feature exports and completely custom modules. Use of the "features" directory within Express is deprecated.  All modules should be added to contrib or custom directories.

While it is easier to capture settings with Strongarm, we prefer to set and update variables in the install and update hooks. If the setting that is defined in Features via Strongarm should be the same for each site, then Features is a great option. If the variable might be changed by a user, then Features is a horrible solution. 

It is easy to export more variables than you need in Features, and some variables, like timestamps, end up in feature exports that later cause overridden states on all sites. Once you have created a feature, look it over and try to catch any variable that might always be different on sites before you commit the code. 

