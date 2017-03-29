## When to Use Entity vs. Node?

We introduced custom entities with express_layout in Express 2.0.  Since then we created many more entities and have established a pattern of using entity for any non-page content or content that is linked to an external source.  This includes tracking pixels and courses.  We try to limit node content types to more traditional "page like" content. 

It is more work to define the UI for a custom entity, but that content also isn't cluttering the default admin/content display.

## When to Use Features

Features is an excellent prototyping tool, but most parts of the Feature export can be moved up a level into the module making it easier to capture customizations or extension of initial functionality.  Defining a node type and fields with a Feature is a great example.  Capturing that in a Feature makes it more difficult to add additional fields to that node type with another Feature or custom module.

The D7 version of Express is a mix of Feature exports and completely custom modules. Use of the "features" directory within Express is depredated.  All modules should be added to contrib or custom directories.

While it is easier to capture settings with Strongarm, we prefer to set and update variables in the install and update hooks.

