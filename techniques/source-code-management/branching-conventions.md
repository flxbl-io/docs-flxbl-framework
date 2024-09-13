# Branching Conventions

Flxbl follows the below branching conventions when they are creating branches.

| Branch      | Used For                                                                                                                              | Restrictions                                                                                                                                                                                                                                                                                                                   |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| main        | The key that is actively used for development and should be always in a deployable state                                              | No direct merges to this branch is allowed. Please read more information on branching model.                                                                                                                                                                                                                                   |
| feature/XYZ | A feature branch that a developer is working on . The XYZ denotes the issue id in your issue registry or User Story in your ALM tool. | Long lived feature branches are discouraged. As soon as a unit of a work (if its a large feature, as soon as a meaningful part is completed, a pull request is to be raised for merging into main) . Feature branch merges are typically not allowed to be merged into release branches. Check branching model for exceptions. |
| bugfix/XYZ  | A bug fix branch that a developer is working on. The XYZ denotes the issue id in your issue registry or User Story in your ALM tool.  |                                                                                                                                                                                                                                                                                                                                |
| chore/XYZ   | Updates to pipelines and other scripts, not necessarily related to the functionality delivered to the platform.                       |                                                                                                                                                                                                                                                                                                                                |

## Commit Message

We typically prefer (feature/bugfix/chore) branches be merged to the main/master or release branches using squash as the option. When it is being merged, most git repository systems allow to combine and edit the commit messages. The commit messages from multiple commits in feature branch should be cleaned up and should follow the similar format

```
[XYZ] Short Commit Message
- Commit Details
- Commit Details
```

In the above example \[XYZ] denotes the work item number in the issue registry and is utilized by Flxbl tools to generate meaningful change logs.
