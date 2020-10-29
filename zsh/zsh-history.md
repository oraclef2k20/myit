
`~/.zshrc`

```zsh

# history
export HISTFILE=${HOME}/.zsh_history
export HISTSIZE=10000
export SAVEHIST=50000
setopt hist_ignore_dups
setopt EXTENDED_HISTORY

#setopt hist_no_store
setopt hist_expand
```