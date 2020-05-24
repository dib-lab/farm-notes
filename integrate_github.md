# Integrating GitHub & the farm

This document will go through how to integrate your GitHub username and password with the Farm (so you don't have to type them in everytime you `push` to github). This material is taken from [this stackoverflow  page](https://stackoverflow.com/questions/35942754/how-to-save-username-and-password-in-git-gitextension) and [this Git page](https://git-scm.com/docs/git-credential-store).


## Step 1: Store Credentials 

Navigate to one of your github repos (or clone one and navigate into it). Make sure this directory is _NOT_ behind the master.
 
```
git config credential.helper store 
git push
```

You should be prompted to enter your user name and password––enter them! 

> ```
> Username: <type your username>
> Password: <type your password>
> ```

Now you should see a `.git-credentials` file in your home directory! This will allow you to use `git` a bit more freely but double check that you are the only one with permission to view the file:


## Step 2: Check Permissions

```
cd ~
ls -al .git-credentials
```

and you should see something like:

> ```
> -rw------- 1 sejoslin sejoslin 42 May 22 08:26 .git-credentials
> ```

make sure you see `-rw-------` permissions associated with the `.git-credentials`. If you do not then change the permissions on this file with:

```
chmod 600 .git-credentials
```

Be free and `git push` the day away!
