layout: true

---
class: middle, center

### Git Pumped

![:scale 40%](images/hans-franz.jpg)

### My Favorite Git Tricks

---
class: middle, left

# Shortened Aliases

We type these a lot, so make them short.

```sh
alias g=git
```

Showing my age, some of these are subversion aliases!

```ini
[alias]
st = status -sb
ci = commit --verbose
up = !git pull --rebase || ( git stash save && git pull --rebase && git stash pop )
br = branch
co = checkout
df = diff
dfs = diff --staged
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

---
class: middle, left

# Git flow / mainline / dev

```ini
[br]
main = master

[alias]
br-main = !git config --local --get br.main || git config --global --get br.main
br-main-set = !sh -c 'git config --local --add br.main $1' -

br-dev = !git config --local --get br.dev || git br-main
br-dev-set = !sh -c 'git config --local --add br.dev $1' -
```

???

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
