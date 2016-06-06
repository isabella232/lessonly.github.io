
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
* Keep commits to single logical changes. (e.g. Gemfile update should include Gemfile.lock as well)
* Prefer to use `git commit` instead of `git commit -m "Commit message"`. This is because
`git commit -m` encourages one line commit messages. Commit messages should read like
history. See below for message template.

### Messages
* Limit the subject line to 50 characters or less. This allows for quick scanning of short concise commits.
* Capitalize the subject line.
* Do not end the subject line with a period. It should function as the title for the commit and is unnecessary.
* When adding a body paragraph to a message, separate it from the subject with a blank line. Additionally, wrap text at 72 characters
* Aim for using imperative mood for subject line (Up for debate). Git's built-in convention for messages are written
in the imperative. `Merge branch 'some-feature'` so using them in our messages makes them simplier to read.
* A good rule of thumb for the subject line of your commit is to complete the following sentence: "If applied, this commit will ___your subject line here___"
* A commit message should answer the following questions
  * Why is this commit important?
  * How does this commit address the larger issue at hand?
  * Are there any side effects to this commit? (optional)

## Configuration
* Require local git install to ignore filemode (i.e. chmod) changes
```shell
$ git config core.fileMode false
```

### Commit message template

Having a standard message template ensures that history reads in the same format
from developer to developer. It also builds off our already in use ISSUE_TEMPLATE
and PULL_REQUEST_TEMPLATE for Github.

To setup a message template create a new file called `.gitmessage` in your root
directory. Add the following script to it.

```shell
# Subject line (50-characters or less)

# Description (word wrap at 72-characters)
Why?

*

How does it address the issue?

*

Are there any side effects?

*
```

Finally, edit your ~/.gitconfig file and add the following section that points
to the location of the template.

```shell
[commit]
        template = ~/.gitmessage
```

### Useful Aliases
* Here are some useful aliases to help speed up git workflows
```shell
# Single line log graph
git config --global alias.slg "log --oneline --decorate --graph"

# For critiquing in love
git config --global alias.praise "blame"

# Pretty log (with colors)
git config --global alias.pl "log --pretty=format:'%C(yellow)%h%C(reset) - %an [%C(green)%ar%C(reset)] %s'"

# Unstage changes that haven't been committed
git config --global alias.unstage "reset"

# Uncommit a commit that is already in the tree without removing the files it modified
git config --global alias.uncommit "reset --soft HEAD^"
```
