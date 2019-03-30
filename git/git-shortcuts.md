# GIT ShortCuts

As I use GIT from command line on a daily basis, shortcuts are really helpfull and time saving.
Just run the following commands on PowerShell (Windows) or Terminal (Unix):

```
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.br branch
git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"
git config --global alias.type 'cat-file -t'
git config --global alias.dump 'cat-file -p'
```