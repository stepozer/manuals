# SSH Keys

You can read about public key authentication [here].

To create your public and private SSH keys on the command-line:
```sh
ssh-keygen -t rsa
```
Result:
```sh
user@user:/home/user/.ssh$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
```
Your public key is now available as .ssh/id_rsa.pub in your home folder. Then you need upload your public key into remote server:
```sh
ssh-copy-id <username>@<host>
```
You can make sure this worked by doing:
```sh
ssh <username>@<host>
```
[here]: <https://help.ubuntu.com/community/SSH/OpenSSH/Keys>