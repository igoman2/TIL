## nvm 때문에 iterm의 setup이 느릴 때 ~/.zshrc 
export NVM_DIR=~/.nvm
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" --no-use # This loads nvm
alias node='unalias node ; unalias npm ; nvm use default ; node $@'
alias npm='unalias node ; unalias npm ; nvm use default ; npm $@'

react 무한스크롤
https://zlrlo.github.io/react-virtualized/
