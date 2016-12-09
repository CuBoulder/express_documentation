# Git Strategy
This is based on Gitflow.

[Quick Visualization](https://docs.google.com/a/colorado.edu/drawings/d/1RAm8Xrvke9r-N1uMoWq_18XQBFo4fhVZ_lPw7t4k0w0/edit?usp=sharing)

## Permanent Branches
* __Master__ - Deployed manually to PROD.
    Commits to this branch are tagged releases. Named `master`. Tags are in the pattern `x.y.z`.
* __Dev__ - Deployed periodically to DEV.
    Named `dev`.

## Temporary Branches
* __Release__ - Deployed manually to TEST.
    Branched from `dev`. Named in the pattern `release/[x.y.z]`.
* __Hotfix__ - Deployed manually to TEST.
    Branched from `master`. Named in the pattern `hotfix/[issue-key]` (issue-key is like FIT-1234).
* __Feature__ - Not deployed.
    Branched from `dev`. Named in the pattern `feature/[issue-key]`.
* __Bug__ - Not deployed.
    Branched from `release-[x.y.z]`. Named in the pattern `bug/[issue-key]`.

## Commands
* Start a Feature

    ```bash
    cd [path/to/git/repo]
    git fetch --all
    git checkout -b feature/[issue-key] dev
    git push --set-upstream origin feature/[issue-key]
    ```

* [Start a Bug](#bug)

    ```bash
    cd [path/to/git/repo]
    git fetch --all
    git checkout -b bug/[issue-key] release/[version.to.release]
    git push --set-upstream origin bug/[issue-key]
    ```

* Start a Hotfix

    ```bash
    cd [path/to/git/repo]
    git fetch --all
    git checkout -b hotfix/[issue-key] [tag.for.last-release]
    git push --set-upstream origin hotfix/[issue-key]
    ```

* Start a Release

    ```bash
    cd [path/to/git/repo]
    git checkout dev
    git pull
    git checkout -b release/[version.to.release] dev
    git push --set-upstream origin release/[version.to.release]
    ```

* Merge a Feature

    ```bash
    cd [path/to/git/repo]
    git fetch
    git checkout dev
    git pull
    git merge --no-ff feature/[issue-key]  # Same as merging Github Pull Request
    git push origin dev
    ```

* Merge a Bug

    ```bash
    cd [path/to/git/repo]
    git checkout release/[version.to.release]
    git pull
    git merge --no-ff bug/[issue-key] # Same as merging Github Pull Request
    git push origin release/[version.to.release]
    git checkout dev
    git pull
    git merge --no-ff bug/[issue-key]  # Same as merging Github Pull Request
    git push origin dev
    ```

* Merge a Hotfix

    ```bash
    cd [path/to/git/repo]
    git checkout master
    git pull
    git merge --no-ff hotfix/[issue-key] # Same as merging Github Pull Request
    git tag -a [version.to.release]
    git push origin master
    git push origin --tags
    git checkout dev
    git pull
    git merge --no-ff hotfix/[issue-key]  # Same as merging Github Pull Request
    git push origin dev
    ```

* Finalize a Release

    ```bash
    cd [path/to/git/repo]
    git checkout master
    git pull
    git merge --no-ff release/[version.to.release]  # Same as merging Github Pull Request
    git tag -a [version.to.release]
    git push origin master
    git push origin --tags
    git checkout dev
    git pull
    git merge --no-ff release/[version.to.release]  # Same as merging Github Pull Request
    git push origin dev
    ```

# Rules of thumb for "Should I merge this?"
* `master` does __not__ get merged into any other branch.
* Hotfix branches merge into `master` and `dev` only.
* Bug branches merge into `release` and `dev` only.
* Feature branches merge into `dev` only.
* `dev` can be merged into feature branches.

# Reading and Source material
* http://jeffkreeftmeijer.com/2010/why-arent-you-using-git-flow/
* https://gist.github.com/JamesMGreene/cdd0ac49f90c987e45ac
