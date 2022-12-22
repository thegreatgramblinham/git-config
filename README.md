# Overview
This repository contains instructions for setting up a new git client machine or a private git server.

Instructions are created for, and have been tested on, Raspberry Pi OS (32bit)

# Creating a private git server
To begin, ensure that your host system is up to date.
```
sudo apt update
sudo apt upgrade
```

Then grab git itself:
```
sudo apt install git
```

Next we are going to create a 'git' user on our machine:
```
sudo adduser git
```
The password can be as secure as you need it. It can be a robust series of characters or simply the username again. Either way, it's up to you and evaluating the security of the network you are exposing the server onto.

Afterwards switch to that user for the rest if the setup:
```
su git
```

Next, under the 'git' user home directory, we need to create our authorized ssh key store.
```
mkdir .ssh && chmod 700 .ssh
touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
```
Now we need to get public keys for each user of this repository so that they can authenticate and update the content. Scroll down to the "Generate SSH keys" section, if needed.

User ssh keys can be then appended to the 'authorized_keys' file, *one per line.* This will be the entire contents of the '.pub' key file.

Now that we have one or more authorized users, we can create our first repository for them to access!
```
# Move to where we will host our repositories
cd /srv/git
# Make the repo directory (this is how we will create any repo going forward)
mkdir projectname.git
cd projectname.git
# Create the empty repo
git init --bare
```
Note: If /srv/git does not exist yet, switch back to a user with sudo powers and:
```
su <sudo user>
sudo mkdir /srv/git
sudo chown --recursive git /srv/git
```

Now from your user machine, they can push the first commit!
```
cd projectname-folder
git init
git add .
git commit
git remote add origin git@<server IP address>:/srv/git/projectname.git
git push origin master
```

That's it! Now any authorized user/key can pull down these changes from the server.

For additional information [check out the official git documentation.](https://git-scm.com/book/en/v2/Git-on-the-Server-Setting-Up-the-Server)

# Generate SSH keys
Generating SSH keys is pretty straightforward. Simply run:
```
ssh-keygen
```
By default, the created key will be stored in your ~/.ssh directory (id_rsa & id_rsa.pub). It is possible to specify another location if you wish.

Entering a passphrase is optional. If you choose to, that passphrase will have to be entered for the key to be used. This is an additional layer of security if you want it. SSH keys without passphrases are usually generated for unattended operations like backups, but run the risk of anyone being able to access what it protects if the secret key is compromised.

Your key files should now be created at accessible in your ~/.ssh directory. The '.pub' file is the public key and the matching file with no extention is your private key. The latter should not be distributed.