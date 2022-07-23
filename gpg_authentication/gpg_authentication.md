# Setting up gpg authentication keeping separate work and personal projects


This short blog post introduces the reader to the PGP key signatures, and suggests a way of creating a signature for your work user, for your private user and how to swap between them.

### PGP minimal intro

A PGP key signature (Pretty Good Privacy) is a generic protocol that associates a signature to an encryption protocol, such as RSA, DSA or ElGamal. In this intro we will use RSA for git, but the method is generalisable for any.

The signature consists of a fingerprint alongside with a username and user email. The fingerprint is a mini public key that authenticates the author of an exchange of keys.

The owner of the signature will be able to sign files, documents, and in particular to sign commits via PGP protocol. In the normal workflow each time you are signing a document or git commit, the CLI will ask you for your passphrase to authenticate you. We will see at the end that it is possible to cache the passphrase, which is recommended only when you are the only person accessing the machine where the passphrase is cached.


## Requirements

- you are familiar with git command line and gitlab / github interface.
- you installed the gpg command line interface ([Mac](https://formulae.brew.sh/formula/gnupg), [Linux](https://www.poftut.com/install-use-gpg-encrytion-linux-order-encrypt-decrypt-files-folder/) or PowerShell - Windows). GPG stands for Gnu Privacy Guard and implements the OpenPGP standard as defined by RFC4880.


## Create and add a PGP key signature to github or gitlab

The procedure of creating and adding a PGP key signature is similar to the one you may have previously done to add an RSA key to your [github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) or [gitlab](https://docs.gitlab.com/ee/ssh/) account (no worries if you have not done it before). The difference is that instead of using the ssh-agent you will be using the gpg CLI, and you will be asked to pair the configurations of your 


1. create a new RSA (read only) key-pair with fingerprint (PGP key signature) via gpg the CLI command
```bash
gpg --full-generate-key
```
This will bring you to a prompt asking to input the kind of key that you want and its expiry period, followed by your email, username, and a passphrase. Recommended settings for a key to sing commits is `RSA (sign only)` followed by `4096` for the keysize (see the appendix if you need to undo the key creation). 

2. If you are using gitlab add the pgp public key to the page [https://gitlab.com/-/profile/gpg_keys](https://gitlab.com/-/profile/gpg_keys), if you are using github use the page [https://github.com/settings/keys](https://github.com/settings/keys).

You can see the list of created key pairs with `pgp -k`, and you can export the public key to a file with `gpg --export -a "email@address.com" > public.key`, then copy the content of `public.key` to clipboard to export it to github/gitlab.


3. Now the missing step is to tell git that it has to start using the created key to sign the commits. Change the user setting on the local machine with
```
git config --global user.email <same email used when creating the key-pair>
git config --global user.name <same username used when creating the key-pair>
git config --global user.signingkey <your fingerprint - copy paste from gitlab gpg page>
git config --global commit.gpgSign true
```

## Swap between work and personal profile

At this point I had the problem of swapping between multiple gitub/gitlab profiles, for work, personal project, etc.
With the commands above I can create as many gpg keys with as many username and emails. To quickly tell git to swap between them I created a bash script for each profile in the sub-folder `~/.git-accounts-settings/`:

```bash
# ~/.git-accounts-settings/company_A.sh
git config --global user.email <same email used when creating the key-pair>
git config --global user.name <same username used when creating the key-pair>
git config --global user.signingkey <your fingerprint - copy paste from gitlab gpg page>
git config --global commit.gpgSign true
```


```bash
# ~/.git-accounts-settings/company_B.sh
git config --global user.email <another email, same used when creating the key-pair>
git config --global user.name <another username used when creating the key-pair>
git config --global user.signingkey <your fingerprint - copy paste from gitlab gpg page>
git config --global commit.gpgSign true
```

```bash
# ~/.git-accounts-settings/personal.sh
git config --global user.email <personal email>
git config --global user.name <personal user>
git config --global commit.gpgSign false
```

Each time I want to swap across profiles, I can call the script with `bash ~/.git-accounts-settings/<my profile>.sh`.

And to quickly swap between them, the commands can be aliased with shorter commands:

```bash 
# in the .bashrc
alias set_git_config_company_A="bash ~/.git-accounts-settings/company_A.sh"
alias set_git_config_company_B="bash ~/.git-accounts-settings/company_B.sh"
alias set_git_config_personal="bash ~/.git-accounts-settings/personal.sh"
```

## Cache the passphrases

To avoid typing the passphrase each time a commit requires to be signed, it is possible to specify a caching duration the gpp agent config file, under `~/.gnupg/gpg-agent.conf`.

For caching the passphrase for 400 days, create the config file with these two lines, where 34560000 is 400 times the number of seconds in a day.

```bash
default-cache-ttl 34560000
maximum-cache-ttl 34560000
```

## Appendix 0: list key creation


To undo the key creation of step 1 you can retrieve the list of existing keys with `gpg -k`, hen copy the key public id to clipboard, that is a string like this dummy `43525435HJJH5K2H3KJHK3452KJH65NBMBV` in the output
```
pub   ed25519 2022-02-18 [SC] [expires: 2024-02-18]
      43525435HJJH5K2H3KJHK3452KJH65NBMBV
uid           [ultimate] Sebastiano Ferraris <seb@email.com>
sub   cv25519 2022-02-18 [E] [expires: 2024-02-18]
```
Finally delete public and private key with:
```
gpg --delete-secret-key 43525435HJJH5K2H3KJHK3452KJH65NBMBV
gpg --delete-key 43525435HJJH5K2H3KJHK3452KJH65NBMBV
```

## Appendix 1: troubleshooting on mac

If git commit fails to authenticate the git commit, with the following error message

Then you have to redirect the `GPG_TTY` key to the local `tty` with:
```
export GPG_TTY=$(tty)
```
If this solves the problem, you will have to append it to your `.bashrc`.


## Appendix 2: export public and private keys

How do I know my public key?

This command will export an ascii armored version of the public key:
```
gpg --output public.pgp --armor --export username@email
```

Also to export the secret key, there is a similar command:
```
gpg --output private.pgp --armor --export-secret-key username@email
```

## Appendix 3: trigger gpg passphrase linux

On some linux distributions, the prompt to insert the gpg passphrase [does not pop up](https://stackoverflow.com/questions/37763170/git-signed-commits-how-to-suppress-you-need-a-passphrase-to-unlock-the-secret) when you create a new commit. Git simply refuses to add a new non-signed commit.
So you will be in the situation where you have some code to commit, you know your passphrase, you have the gpg-agent on so you not need to retype the passphrase each time, but nobody is asking you for your passphrase.

A workaround is to trigger gpg to ask you for your passphrase for another reason (e.g. signing a file), after which your passphrase is stored in the gpg-agent and you will be free to create signed commits, as the passphrase is automatically retrieved. The list of commands would be:
```
git commit -am "new stuff"  # this commit is not added and does not trigger the passphrase prompt
cd 
touch z_tmp.txt  # creating a dummy file to authenticate
gpg -s z_tmp.txt  # this trigger the passphrase (and a y/n question to confirm your choice)
cd <repo you were working>
git commit -am "new stuff"
```

To turn this workaround into a oneliner, you can create the dummy file `z_tmp.txt` in the root directory (as above), and then add the following line to your bash profile:
```
alias gpg_trigger='gpg -s ~/z_tmp.txt'
```
So the next time, instead of running all the commands of the previous block, you can simply call the newly created alias `gpg_trigger`.
