layout: true

---
class: middle, center

### Git Pumped

![:scale 40%](images/hans-franz.jpg)

### My Favorite Git Tricks

???

**important** Do a `git pop` before starting so that `git up` demo is effective

**important** Open up Sourcetree, half split to behind the presentation

---
class: middle, center

![:scale 100%](images/hold-onto-your-butts.png)

This is going to be a lot 😁

---
class: middle, left

# Shortened Aliases

We type these a lot, so make them short.

```sh
# Add to your ~/.zshrc
alias g=git
```

Showing my age, some of these are subversion aliases!

```ini
# Aliases are defined in ~/.gitconfig
[alias]
st = status -sb
stl = status --long
co = checkout
ci = commit --verbose
br = branch
```

???

- Only alias in my zshrc is the shortened git alias
- All the git aliases should be in gitconfig (tab completion, etc.)
- I shorten all the most common things I do
- Add a slight variation to the standard commands like `status` and `commit`

```sh
$ cd ~/work/projects/augury/augury
$ g st
$ g co master
$ g co new-branch
```

- Let's apply a stash and stage part of that change
- Now let's see an example of status
- Shortened status for a quick check
- Long status to see all the info

```sh
$ g stash apply
$ g add -p
$ g st
$ g stl
```

---
class: middle, left

# Shortened Aliases

```ini
[alias]
df = diff
dfs = diff --staged
```

???

Now let's look at a diff of the index and the staged change

```sh
$ g df<tab>
$ g df
$ g dfs
```

---
class: middle, left

# Update

```ini
[alias]
up = !git pull --rebase || ( git stash save && git pull --rebase && git stash pop )
```

???

- A more advanced alias for something simple.
- Do a `pull --rebase` but also `stash` and `pop`, if there are changes

```sh
$ g reset --hard
$ g co master
$ g sap
$ g up
$ g reset --hard
```

---
class: middle, left

# Fancy logs

```ini
[alias]
# Better log commands
lg = log --graph --abbrev-commit --date=relative --pretty=format:'%Cred%h%Creset %Cgreen(%cr)%Creset %Cblue%an%Creset %s %C(yellow)%d%Creset'
# Use bang to be able to re-use the alias above (with pretty format) and add to it
lga = !git lg --branches --remotes
lgp = !git lg -p
```

???

- I use these a lot
- The `lg` alias is similar to what you'd find in a GUI
- It is searchable and easy to parse

```sh
$ g lg
```

**SHOW** Sourcetree as an example

The other aliases build on this

```sh
$ g lga
```

`lgp` shows you the actual commit as well

```sh
$ g lgp
```

You can still use these as regular log commands, for example showing commits between two tags

```sh
$ g lg v1.0.1..v1.0.2
$ g lgp v1.0.1..v1.0.2
```

---
class: middle, center

**Bear with me**

???

This next bit gets a bit complicated

---
class: middle, left

# Git flow / mainline / dev

```ini
[br]
main = master
```

```ini
[alias]
br-main = !git config --local --get br.main || git config --global --get br.main
br-main-set = !sh -c 'git config --local --add br.main $1' -

br-dev = !git config --local --get br.dev || git br-main
br-dev-set = !sh -c 'git config --local --add br.dev $1' -
```

Setting a different mainline

```sh
$ cd path/to/my/repo
$ git br-main-set main
```

???

- This gets a bit complicated, but helps with other aliases
- Dealing with `master` vs `main` and also with development lines
- I've added a custom section called `[br]` with the default
- I've added an alias to set this config locally on a repo
- Most repos I work with are still `master`
- Some are `main`, so I can change it
- If `dev` isn't set, then it'll fall back to main

---
class: middle, left

# Updating feature branches

```ini
[alias]
upd = !git pull --rebase origin $(git br-dev) || ( git stash save && git pull --rebase origin $(git br-dev) && git stash pop )
upm = !git pull --rebase origin $(git br-main) || ( git stash save && git pull --rebase origin $(git br-main) && git stash pop )
```

???

- Similar to my `up` alias
- So now I can update from one of these lines
- We don't have any develop branches, so not as important
- But when I switch between projects with `main` and `master` I still use the same alias for updating my local branch

```sh
$ g co since-id
$ g lg
```

Note when that was, 6 months ago

```sh
$ g upm
$ g lg
```

---
class: middle, left

# Resolving Conflicts

```ini
[alias]
resolve = !${EDITOR:=vi} $(git status --porcelain | awk '/^UU / {print $2}' | head -1)
resolved = !git add $(git status --porcelain | awk '/^UU / {print $2}' | head -1)
```

???

- As we saw when updating, we get conflicts
- This happens a lot in a busy codebase
- Helpers to deal with each conflicted file
- Some shell magic to find the file, then stage the fix

```sh
$ g st
$ g resolve
```

Find the conflict

```sh
$ g resolved
$ g st
```

---
class: middle, left

# Diffing

```ini
[alias]
# Show the changes on the current branch compared to development branch
dfd = !git show `git merge-base HEAD origin/$(git br-dev)`..HEAD --reverse
dfda = !git diff `git merge-base HEAD origin/$(git br-dev)`..HEAD
dfdlc = !git diff `git merge-base HEAD origin/$(git br-dev)` HEAD --stat

# and compared to the mainline
dfm = !git show `git merge-base HEAD origin/$(git br-main)`..HEAD --reverse
dfma = !git diff `git merge-base HEAD origin/$(git br-main)`..HEAD
dfmlc = !git diff `git merge-base HEAD origin/$(git br-main)` HEAD --stat
```

???

- Another example of the split between `dev` and `main`
- This allows me to see my changes vs a these flows

See each commit against main line

```sh
$ g dfm
```

See the whole changeset against main line

```sh
$ g dfma
```

See stats about the current vs main

```sh
$ g dfmlc
```

---
class: middle, left

# Rebasing

```ini
[alias]
rbsd = !git rebase -i `git merge-base HEAD origin/$(git br-dev)`
rbsm = !git rebase -i `git merge-base HEAD origin/$(git br-main)`

rbc = rebase --continue
rba = rebase --abort
```

???

Interactive rebasing without having to count commits

```sh
$ g co since-id-rebase-me
$ g rbsm
```

**edit** a commit so we can do the next step and abort

Quick helpers to continue or abort

```sh
$ g rba
```

---
class: middle, left

# Pristine

```ini
[alias]
pristine = !git fetch && git reset --hard origin/$(git symbolic-ref --short HEAD)
```

???

- Sometimes rebasing goes horribly wrong
- Or there is a long running feature branch you haven't checked in on in a while
- This gets the very latest from the remote as of this moment!

---
class: middle, left

# WIP

```ini
[alias]
wip = !git add -u && git commit -m 'watch me @wip, watch me [skip ci]'
```

???

- Dual purpose
- Save the current edits, but not new files to a commit
- Avoid any CI runs in most tools

```sh
$ g co new-branch
$ g sap
$ g wip
```

---
class: middle, left

# The reverse wip

```ini
[alias]
pop = !git --no-pager log -n 1 HEAD && git reset HEAD^
```

???

Pop the last commit off and show the log, so you know what it was

```sh
$ g pop
```

---
class: middle, left

# Navigating Branches

```ini
[alias]
cob = checkout -b

com = !git checkout $(git br-main)
cod = !git checkout $(git br-dev)

cof = !git checkout $(git branch | grep -v '*' | fzf)
```

Go back to the previous branch (built-in)

```sh
$ g co -
```

???

Now the **last** thing I want to show you.

Easy creation of branches

```sh
$ g cob fancy-branch
```

Quick ways to get back to the mainline branches, using my special sauce from earlier

```sh
$ g com
```

Use fzf to go to an existing branch

```sh
$ g cof
```

Switch back to the branch you were on, shell style like `cd`

```sh
$ g co -
```

---
class: top, left

# Links

<https://github.com/claytron/dotfiles/blob/master/.gitconfig>

---
class: middle, center

# FIN

[/ Links ---------------------------------------------------------------- /]: #
[twitter-claytron]: https://twitter.com/claytron
[/ ---------------------------------------------------------------------- /]: #
