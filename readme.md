# Encrypted Vars File Ansible

A quick and easy way to use encrypted var files in your playbook using any unix like platform. I am using a mac.

# How to use

Before you begin using this method you will need to setup your vault password file and shell environmental variable.

First the vault password file:

    echo -n "MySecretPassword999" > ~/.my-vault-password
    chmod 0400 ~/.my-vault-password

Now add the following line to your shells resource file. 

I am using ZSH which is default on a mac these days, you might well be using bash.

    echo "export $VAULT= ~/.my-vault-password" >>  ~/.zshrc
    source  ~/.zshrc

if you use bash:

    echo "export $VAULT= ~/.my-vault-password" >>  ~/.bashrc
    source  ~/.bashrc

## The encrypted vars file

Unless you can guess my password , the file `encrypted/vars.yml` is not going to work for you.

Use the vars file from `example/vars.yml` 
Copy it into encrypted folder and encrpyt it , as show below

    cp example/vars.yml encypted/
    ansible-vault encrypt --vault-password-file=$VAULT encrypted/vars.yml


## The playbook

As the name suggests test_playbook.yml is a simple test to write out data that has been encrypted from the vault. 

In this the play book calls the vars file: `encrypted/vars.yml`

We can decrypt this file as we run the playbook by adding the parameter: 

     --vault-password-file=/path/to/vault

If the playbook works, we should end up with a file at /root/test.txt

## The template

The template contains the following data:

    Super Secret Password is {{ password }}

## Running the playbook

    ansible-playbook test_playbook.yml --vault-password-file=$VAULT

## Example Run

    neil@mac avault % ansible-playbook test_playbook.yml --vault-		   password-file=$VAULT

    PLAY [masters] ******************************************************************************************************************************************************************************
    
    TASK [Gathering Facts] **********************************************************************************************************************************************************************
    ok: [192.168.0.200]
    TASK [vaulttest : Write /root/test.txt as template, with decrypted vars] ********************************************************************************************************************
    changed: [192.168.0.200]
    
    PLAY RECAP **********************************************************************************************************************************************************************************
    192.168.0.200              : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

## So whats in the encrypted file?

Im glad you asked, here it is:

    [root@srv ~]# cat test.txt
    Super Secret Password is isitsafe?I think so

## Edit the vars file while its still encrypted

    ansible-vault edit --vault-password-file=$VAULT encrypted/vars.yml

Once saved, the updated file is re-encrypted. 


# Sample vars file

You can find the un-encrypted plain text version of vars.yml in the **example** folder

