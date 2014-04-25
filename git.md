Git Setup
=========
this is a quick documentation on installing github, setting ssh key for passwordless commit/push, and how to add and remove files. 

### Setup

install git
```
sudo apt-get install git
```
set variables
```
git config --global user.name "Name Name"
git config --global user.email "usr@gmail.com"
```

### Auth Keys

ssh keys
```
cd ~/.ssh/
ssh-keygen -t rsa -C "usr@gmail.com"
```

copy the public key to the _ssh keys_ on github website
```
cat githubkey.pub
rm githubkey.pub  #removing public key
```

load private into system, then check connection
```
ssh-add githubkey
ssh -T git@github.com
```

### Working Directory

go into working directory, and init it
```
cd repo
git init
touch README
git add README
git commit -m 'adding README to repo'
```

**erronus add**
```
git remote add origin https://github.com/usr/repo.git
git push origin master

*fails because one, path is wrong, two we have files already in repo*

git remote remove origin
```

add correct directory using ssh link given
```
git remote add origin git@github.com:usr/repo.git
```

*remmember to pull our files present first*
```
git pull origin master
git push origin master
		 <name> <branch>
```

### add or remove

add all the files commit and push
```
git add .
git commit
git push origin master
```

remove files
```
git remove file
git rm README
git commit -m 'removing lagging readme file'
git push
```
