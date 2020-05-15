# Ruby Environment Setup

How to properly setup your Ruby environment using `rbenv`.

## Purpose

Using some sort of Ruby version management is highly recommended as opposed to using the system version of Ruby (which often requires `sudo` access to install dependencies).

## Installing `rbenv`

### 1. Install [Homebrew](https://brew.sh) if it's not already installed:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)""
```

### 2. Install `rbenv`:

```bash
brew install rbenv
```

### 3. Initilaize `rbenv`:

```bash
rbenv init
```

**NOTE:** Don't forget to also add `eval "$(rbenv init -)"` to your `~/.bash_profile`, as suggested by the output of the above command.

### 4. Install a current version of Ruby:

```bash
rbenv install 2.6.3
```

**NOTE:** You can use the `rbenv install --list` command to list available Ruby versions.

### 5. Configure `rbenv` to use the newer version of Ruby:

```bash
rbenv global 2.6.3
```

### 6. Reload your `~/.bash_profile` (or restart Terminal):

```bash
source ~/.bash_profile
```

### 7. If on macOS Catalina and using Zsh (Z shell), you may encounter a write permissions error. Here's how to fix it.

Add the following to `.zshenv`:

```
export PATH="$HOME/.rbenv/bin:$PATH"
```

Add the following lines to `.zshrc`:

```
source $HOME/.zshenv
eval "$(rbenv init - zsh)"
```

Restart terminal.

## References

The information in this guide is based on [this excellent article](https://jasoncharnes.com/install-ruby/) by Jason Charnes.

The information for installing rbenv on Zsh [is this article](https://programmingzen.com/installing-rbenv-on-zsh-on-macos/) by Antonio Cangiano.
