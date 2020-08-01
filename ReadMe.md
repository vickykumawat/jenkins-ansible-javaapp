# Setup Jenkins with Ansible for deployment

1. Start Jenkins server, connect using SSH inside Jenkins Master.

```
$ sudo apt update -y
$ sudo apt install ansible -y
```

2. Visit Jenkins Dashboard, Install new plugin "Ansible Plugin" Use "Install without Restart"

3. From Jenkins dashboard, goto "Global Tool Configuration" > Setup path for Ansible "/usr/bin"

4.  Create an additional virtual machine in same VPC / VNet as jenkins master. Lets call it "Sample1" 

5. Use SSH connection with Jenkins Master, to create a new SSH Keypair and Use "private-key" in Jenkins credentials and public-key tobe copied to "Sample1"

```
$ cd /home/$USER
$ mkdir temp
$ ssh-keygen 
Path for Public Key: /home/$USER/temp/id_rsa.pub
Pass Phrase: <ENTER>
$ cd temp
$ ls
## Lets copy PUBLIC Key to Sample1
## Private IP of Sample1 : 10.0.1.5
$ ssh-copy-id -i /home/$USER/temp/id_rsa.pub mahendra@10.0.1.5
Accept the server thumbprint: YES
## Change File permissions of SSH Keys
$ chmod 600 id_rsa
$ chmod 600 id_rsa.pub
## Test the connectivity with 10.0.1.5
$ ssh -i /home/$USER/temp/id_rsa mahendra@10.0.1.5
## You should be now connected
$ exit
## Display contents of PRIVATE KEY on Screen
$ cat id_rsa
## Just COPY entire private key
```

6.  Open Jenkin dashboard > Manage Credentials > Global Credential Manager "Jenkins" > Add Credentials

    Choose option "SSH Username with private key"

    ```yaml
    ID: webhostid
    Description: SSH Credentials for Web Host
    Username: mahendra
    Private Key: Enter Directly
        PASTE the key copied from SSH Terminal
    ```

    Save the credentials


7.  Login in GitHub and FORK this repository 'https://github.com/mahendra-shinde/jenkins-ansible-javaapp'

8.  From inside the remote server, get private ip address (Use AWS Console or Azure Portal)

    ```
    $ hostname -i
    ```

9.  Get inside the FORKED repository (create in step#7) and edit file 'deployment/evniornment.ini'

    Replace these line:
    
    ```yml
    [webhosts]
    10.0.1.5 ansible_user=mahendra
    ```

    With these [Please replace 172.17.9.2 with Private IP of Your VM, and 'azureuser' with Username of VM ]:
    ```yml
    [webhosts]
    172.17.9.2 ansible_user=azureuser
    ```

10. After making changes, just use "Commit " button.

11. Modify the Jenkinsfile inside your repository and Update line #11. You need to replace repository URL. Then use "Commit" button.

12. Login in Jenkins Dashboard, and create a new "Pipeline Project"

    ```yml
    Description: Ansible integration demo
    Pipeline:   
            Pipeline from SCM
            SCM: Git
            Repository URL: YOUR-REPO-URL
    ```

13. Save the Job, and use "Build Now"

14. Verify the build console and once build is SUCCESS, use PUBLIC-IP of Target VM.

    `http://PUBLIC-IP:8080/`