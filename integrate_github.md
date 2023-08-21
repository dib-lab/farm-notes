# Integrating GitHub & the farm

This section will contain the information for creating, connecting, and testing your ssh key-pair between the remote computer and GitHub.

## Step 1: Creating an SSH key-pair

Let's generate those keys! Place the following command into your terminal and change the email to your GitHub account's email.

```
( cd $HOME/.ssh && ssh-keygen -t rsa -b 4096 -C "your_email@example.com" && cd $HOME)
```

After running the command, you should receive the prompt:

```
Enter file in which to save the key (/home/baumlerc/.ssh/id_rsa):
```

Here add a descriptor to the file name or leave it blank (by hitting enter without any text) for the SSH keys to be named "id_rsa" and "id_rsa.pub"
I am going to give my keys a descriptive name.

```
Enter file in which to save the key (/home/baumlerc/.ssh/id_rsa): a-name-i-will-remember-that-this-key-is-for-github-access
```

You will now be prompted to create a passphrase. Leave this blank by hitting `enter` on your keyboard. 

```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

The final output should be similar to this:
```
Your identification has been saved in a-name-i-will-remember-that-this-key-is-for-github-access
Your public key has been saved in a-name-i-will-remember-that-this-key-is-for-github-access.pub
The key fingerprint is:
SHA256:On9N22rdLVIpUqQQ/1Hnytm9H5TKcAnsfNDA8P/B5Es your_email@example.com
The key's randomart image is:
+---[RSA 4096]----+
|        .oo.  . .|
|        ..o.+. o |
|         ..B.. ..|
|          +.=o=+o|
|        S  =.=+Eo|
|       .  . B B +|
|      o    + O *.|
|       o  . = + =|
|        .. ..o ..|
+----[SHA256]-----+
```

Great! Now you have a private and public SSH key in your `.ssh` directory on Farm. You can see these files with the following command:

```
ls .ssh
```

## Step 2: Connecting the Public key to GitHub

To utilize this key for GitHub access you will need to copy your public key and give it to GitHub.

Start by accessing the public key file (the SSH key that ends in `.pub`) contents with:

```
cat $HOME/.ssh/a-name-i-will-remember-that-this-key-is-for-github-access.pub
```

Copy the output that looks something like:
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCuGB9i8OyR4YlCH/pDTxWUcz8CS+zAz2WUqaKDyKkDs5uUbo9hqQSIKrAmKyTvRtK3TQVN2QQtqr9KznwvFRJ5UfcBvgerZSb/ZaVisr7xxAbszv7bn4NT9S1rwBAdzedYxEbKBFXHhQ/KfCYayrcaA7Je3h8Gtl41Ss0SYn4M0b4UYlrzjzIGc6dK5CsoUU0B1+GJaSsmMD8WpiCm1O0E4f7MTlCQ1fpzNZpr+Hui4DBLUBYpBj6ehZpv97Su2jAFFECW70rVjIiEb2BAgXdYtPA/SgnjIrpWCSF2G3eV4TUkFrSyXK0tYmiM/ZA81JY83DBf7UBOEW2h+6DE8A5uRLyK9mbbPDg8BBPAOtpZ+m6VRnwn8eM0YQgHkHPTdONx0L/yan1pJ/PHwefq+JsL18e3VdxDBQ5vEsKxb3c7FIIM/l2agyyzsSYkvo5hizjFEPqnrvUf5VtdPR3STWNM3KjsAFF7SlZGXkRcFBYuvwU6jwt46YdpltoLdcvbZ7LNcT8NRmb8tm8hjpwIzbKjl3fkwUJsDSL/m4JWDYByztf4PIKWacpTbPG/H8X8VPFg2QOxzfhOIQDIJatKYb0lCv18VZOup5vziFOAhAmpEhXuCb3EgIBHyTzB/vR0aLo/V4tZ7J+1dCaAyJY3My5Br1AphR+tzwcxyibyzhJH/w== your_email@example.com
```

Now navigate to your GitHub profile key setting at https://github.com/settings/keys.

Click the `New SSH key` box in the upper right.

Add a descriptive title in the `Title` section (i.e. Farm or Farm-key)

Paste your copied public key into the `Key` section.

Finally, click the `Add SSH key` button at the bottom of the site.

## Step 3: Testing your connection

After completing the previous two steps you should now be connected to GitHub with your Farm account. This same process may be used with your local computer for access when Farm is undergoing its regular maintenance (or for projects that do not require Farm access). 

Before moving on to better things, it's good practice to test what you just did. Let's check your connection with: 

```
ssh -T git@github.com
```

You should receive the following output.

```
Hi ccbaumler! You've successfully authenticated, but GitHub does not provide shell access.
```

If you need to debug your connection, try the more verbose output of:

```
ssh -vT git@github.com
```

For further reading on the subject of SSH key generation, and Github SSH key settings.
- https://www.ssh.com/academy/ssh/keygen
- https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
- https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account

# Deprecated credential integration, kept for posterity?

This section will go through how to integrate your GitHub username and password with the Farm (so you don't have to type them in every time you `push` to github). This material is taken from [this stackoverflow  page](https://stackoverflow.com/questions/35942754/how-to-save-username-and-password-in-git-gitextension) and [this Git page](https://git-scm.com/docs/git-credential-store).


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
