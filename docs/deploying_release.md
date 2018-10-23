# Multienvironment workflow for deploying an Express release using [Atlas](https://github.com/CuBoulder/atlas)

As you are delpying, you can watch Atlas progress in two places, both are recommended.

1. SSH onto the relevant Atlas server, become `osr_web_deploy` and run `tailatlas`. Credentials are in the password store.
1. Watch the `general` channel in the Atlas slack instance.

## Process

1. Create a [release branch](https://github.com/CuBoulder/express_documentation/blob/master/docs/git_strategy.md#start_release).
1. Deploy the branch to DEV as the `current` profile.
1. Delete all `available` sites on DEV so that they can be recreated with the new profile.
    * `where={"status":{"$in":["available"]}}`
1. Use the Webcentral site on DEV to update Webcental and the Homepage instances on DEV. The rest of the instances are most likely for feature branches and should be left alone.
1. Deploy the branch to TEST, **do not** make it `current`.
1. Use the Webcentral site on TEST to update launched sites that are not part of the training set `where={"status":"launched","update_group":{"$ne":13}}`.
1. [Finalize the release](https://github.com/CuBoulder/express_documentation/blob/master/docs/git_strategy.md#finalize_release).
1. Update the code item on DEV.
1. Update the code item on TEST and make it `current`.
1. Delete all `available` sites on TEST so that they can be recreated with the new profile.
    * `where={"status":{"$in":["available"]}}`
1. Update the remaining sites on TEST in batches defined by `status` and `update_group`
    * `where={"status":"installed","update_group":0}`
    * `where={"status":"installed","update_group":1}`
    * `where={"status":"installed","update_group":2}`
    * `where={"status":"launched","update_group":13}`
1. Deploy the code item to PROD and make it `current`.
1. Delete all `available` sites on PROD so that they can be recreated with the new profile.
1. Update the sites on PROD in batches defined by `status` and `update_group`
    * `where={"status":"installed","update_group":0}`
    * `where={"status":"installed","update_group":1}`
    * `where={"status":"installed","update_group":2}`
    * `where={"status":"launched","update_group":0}`
    * `where={"status":"launched","update_group":1}`
    * `where={"status":"launched","update_group":2}`
    * `where={"status":"launched","update_group":3}`
    * `where={"status":"launched","update_group":4}`
    * `where={"status":"launched","update_group":5}`
    * `where={"status":"launched","update_group":6}`
