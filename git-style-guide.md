
This git guide is based around community best practices and this excellent blog post: http://chris.beams.io/posts/git-commit/

## Branching 
* Namespace branches based off of your *initials* and a *short description*.
```shell
# Good
$ git checkout -b jmf-update-signup-page

# Not so good
$ git checkout -b do-something
```
* Prefer to use hypens between words.

* Feature branches should be namespaced off of the epic they are based on (e.g. `settings-redesign`)
```shell
# Good
$ git checkout -b jmf-settings-redesign-visual-indicator

# Not so good
$ git checkout -b jmf-visual-indicator
```

## Commits
* Commit *early and often*. This naturally leads to smaller commits which are both easier to understand and revert if needed.
* Keep commits to single logical changes.

### Messages
* Limit the subject line to 50 characters or less. This allows for quick scanning of short concise commits.
* Capitalize the subject line.
* Do not end the subject line with a period. It should function as the title for the commit and is unnecessary.
* When adding a body paragraph to a message, separate it from the subject with a blank line.
* Aim for using imperative mood for subject line (Up for debate). Git's built-in convention for messages are written 
in the imperative. `Merge branch 'some-feature'`. 
* A good rule of thumb for the subject line of your commit is to complete the following sentence: "If applied, this commit will ___your subject line here___"

## Configuration
* Require local git install to ignore filemode (i.e. chmod) changes
```shell
$ git config core.fileMode false
```
