# Github Specific Process

Our process depends heavily on [labels defined in Github](https://github.com/CuBoulder/express/labels).

## Epics

We use the ZenHub approach to Epics. “A theme of work that contain sub-tasks required to complete the larger goal. The concept stems from the agile principle that tasks should be broken down into small, manageable chunks; that way, you're able to ship impactful changes more often and ultimately gain more control over the release cycle. In agile development, a ‘user story’ is the smallest unit of work, and an Epic is essentially a ‘big’ user story.”

Epics are the only issue type that require `evaluate` labels.

## General Feature Request Flow

1. Create a [new Issue](https://github.com/CuBoulder/express/issues/new) following the template.
    * Apply the following labels: `Epic`, `evaluate-1:Director Approval` and all `evaluate-2` labels.
1. Epic is reviewed by the **Director**.
    * If it is approved, the Director will remove the `evaluate-1:Director Approval` label.
1. Epic is reviewed by relevant team member.
    * As reviews are done, team members will either leave a comment and apply the `evaluate-1:More Information` label or remove the relevent `evaluate-2` label.
1. Once all `evaluate` labels are removed, an Epic can be considered for inclusion in an upcoming Sprint.
1. Issue is added to a Sprint (not necessarily the _next_ sprint) and assigned to a **Developer**.
1. **Developer** scopes out the work and creates additional Issues of `type:task` as needed and defines deployment of the Expic as `deploy:Bundle`, `deploy:Express` or `deploy:Other` and adds `qa:pre-merge` or `qa:post-merge` based on when it would be best for the Epic to be reviewed.  Some bundle updates can often be reviewed premerge while other changes require updates to the Express and muliple bundles.  Those updates should be labeled `qa:post-merge`.
    * Issues of `type:task` may be added to the current sprint.
1. Issues of `type:task` are assigned to a **Developer** who writes code.
    * If Developers want to commit code to branches for each Issue of `type:task`, use a branch named after the Epic as an integration branch.
1. If someone wants to preview an Epic for any of the following reasons, apply the label [request:Feature Branch](https://github.com/CuBoulder/express/issues?q=is%3Aopen+is%3Aissue+label%3A%22request%3AFeature+Branch%22) to the Epic and assign additional users as applicable.
1. A **QA Technician** will create an instance with the relevant code for all issues labelled [request:Feature Branch](https://github.com/CuBoulder/express/issues?q=is%3Aopen+is%3Aissue+label%3A%22request%3AFeature+Branch%22) once the developer adds [ready-for:Feature Branch Review](https://github.com/CuBoulder/express/issues?q=is%3Aopen+is%3Aissue+label%3A%22ready-for%3AFeature+Branch+Review%22)
1. The **Developer** creates a Pull Request against the `dev` branch of Express when the code is ready for automated testing and technical reviews. PR are merged when code is ready regardless of additional review or documentation.
1. A **Developer** who was not involved in writing the code will review the code using the GitHub PR review tools.
1. When all PR feedback is addressed, a **Developer** who was not involved in writing the code will merge the PR.
1. The feature will be deployed as part of the next release.

---

## Evaluate labels

### Tier 1

* [evaluate-1:Director Approval](https://github.com/CuBoulder/express/labels/evaluate-1%3ADirector%20Approval)
  * Responsible: Jo
  * Review criteria:
    * Feature meets the goals of the Service.
* [evaluate-1:More Information](https://github.com/CuBoulder/express/labels/evaluate-1%3AMore%20Information)
  * Responsible: User who created the Epic

### Tier 2

* [evaluate-2:Accessibility](https://github.com/CuBoulder/express/labels/evaluate-2%3AAccessibility)
  * Responsible: Kevin C or a Developer
  * Review criteria:
    * ...
* [evaluate-2:Automated Testing](https://github.com/CuBoulder/express/labels/evaluate-2%3AAutomated%20Testing)
  * Responsible: Alex or James
  * Review criteria:
    * Can we write sufficent tests to cover this feature
* [evaluate-2:D8 Upgrade Path](https://github.com/CuBoulder/express/labels/evaluate-2%3AD8%20Upgrade%20Path)
  * Responsible: Kevin R or Owen
  * Review criteria:
    * As on ??/??/2017, all new features added to the Express install must have an upgrade path to D8
* [evaluate-2:Documentation Needs](https://github.com/CuBoulder/express/labels/evaluate-2%3ADocumentation%20Needs)
  * Responsible: Emily
  * Review criteria:
    * Determine if documentation will need to be create or updated and create task in appropriate system if needed.
* [evaluate-2:Performance Impact](https://github.com/CuBoulder/express/labels/evaluate-2%3APerformance%20Impact)
  * Responsible: James
  * Review criteria:
    * Increased load on DB or Webservers
    * Decrease in cache utilization
    * Scale issues
* [evaluate-2:Security Concerns](https://github.com/CuBoulder/express/labels/evaluate-2%3ASecurity%20Concerns)
  * Responsible: Kevin R or James
  * Review criteria:
    * SQL Injection
    * XSS
    * Ability to deface site
    * Ability to expoloit to create in content in an unintended way.
* [evaluate-2:Support Concerns](https://github.com/CuBoulder/express/labels/evaluate-2%3ASupport%20Concerns)
  * Responsible: Emily or Cathy
  * Review criteria:
    * Increased support load
* [evaluate-2:Usability](https://github.com/CuBoulder/express/labels/evaluate-2%3AUsability)
  * Responsible: Kevin C
  * Review criteria:
    * Ability of our users to understand the feature without extensive training

---

## Use of Type labels

`type:Task`, `type:Bug`

* An Epic can stand alone as a `type:Task` and a single branch and Pull Request (PR) or have several tasks associated to it with their own branches and PRs.
* Code should only be committed to branches for `type:Task` or `type:Bug`.
* Epics, Tasks and Bugs all use different templates.

## Use of Improvement Labels

These tags are mainly for reporting the number of tasks that contributed to an improvements vs. bugs. If you are unsure of which type of improvemnt, use the general improvement tags. 
