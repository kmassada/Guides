Git Setup
=========

this is a quick documentation on installing github, setting ssh key for passwordless commit/push, and how to add and remove files. 

### Setup

install git

```shell
sudo apt-get install git
```

set variables

```shell
git config --global user.name "Name Name"
git config --global user.email "usr@gmail.com"
```

### Auth Keys

ssh keys

```shell
cd ~/.ssh/
ssh-keygen -t rsa -C "usr@gmail.com"
```

copy the public key to the _ssh keys_ on github website

```shell
cat githubkey.pub
rm githubkey.pub  #removing public key
```

or permanently add key

```shell
echo 'IdentityFile ~/.ssh/githubkey' >> ~/.ssh/config
```

load private into system, then check connection

```shell
ssh-add githubkey
ssh -T git@github.com
```

### Working Directory

go into working directory, and init it

```shell
cd repo
git init
touch README
git add README
git commit -m 'adding README to repo'
```

**erronus add**

```shell
git remote add origin https://github.com/usr/repo.git
git push origin master

*fails because one, path is wrong, two we have files already in repo*

git remote remove origin
```

add correct directory using ssh link given

```shell
git remote add origin git@github.com:usr/repo.git
```

*remmember to pull our files present first*

```shell
git pull origin master
git push origin master
		 <name> <branch>
```

### use cases

#### add all the files commit and push

```shell
git add .
git commit
git push origin master
```

#### remove files

```shell
git remove file
git rm README
git commit -m 'removing lagging readme file'
git push
```
#### setup collaboration
create eclipse project
- init outside of working dir. 
- place .ignore inside working dir. 
- name "origin" something else

```shell
git init
vi .gitignore
-------------
.*
bin/classes
-------------
git add -A 
git remote add project git@github.com:usr/repo.git
```

#### remove all previous commits
```shell
rm -rf .git
git init
#any desired change
git remote add project git@github.com:usr/repo.git
git push -u --force project master
```

#### made a change and want to revert to last commit
this resets your working directory and the repo to the code you had before your last commit

```shell
git reset --hard HEAD~1
#any desired change
```

#### made a commit, but need to change what you just did to that commit 
pulls the commit, resets the files you commited, you can keep making your changes, any commit you make is a new commit

```shell
git reset HEAD~1
#any desired change
```

#### made a commit, you need to perhaps add new files, and make light changes but original commit isn't too wrong
pulls the commit, does not reset index, leaves all the same. only difference is now, you can make again few changes and commit again, in this case, you can add files, but not remove them from commit.

```shell
git reset --soft HEAD~1
#any desired change
git commit -c ORIG_HEAD 
```

#### create branch off master, move forward, and back

first create branch off a given hash
```shell
git pull origin master
git checkout <hash>
git checkout -b slave
```

compare master head to current branch
```shell
diff master --stat 
```

get more stats on on current branch
```shell
git log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%C(bold blue)<%an>%Creset' --abb
rev-commit
```

commit your local changes
```shell
git add info.txt
git commit -m 'test file'
git push fuel-fund cgdevel
```
merge up
```shell
git merge <hash> -n
```