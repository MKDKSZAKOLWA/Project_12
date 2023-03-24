# (STEP 22) PROJECT 12 ANSIBLE REFACTORING, ASSIGNMENTS & IMPORTS

## ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)


In this project you will continue working with *ansible-config-mgt* repository and make some improvements of your code. Now you need to refactor your Ansible code, create assignments, and learn how to use the imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook – it allows you to organize your tasks and reuse them when needed.


Side Self Study: For better understanding or Ansible artifacts re-use – read this article.

https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse.html



## Code Refactoring
Refactoring is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.

In your case, you will move things around a little bit in the code, but the overal state of the infrastructure remains the same.

Let us see how you can improve your Ansible code!


## Step 1 – Jenkins job enhancement

Before we begin, let us make some changes to our Jenkins job – now every new change in the codes creates a separate directory which is not very convenient when we want to run some commands from one place. Besides, it consumes space on Jenkins serves with each subsequent change. Let us enhance it by introducing a new Jenkins project/job – we will require Copy Artifact plugin.


1. Go to your *Jenkins-Ansible* server and create a new directory called *ansible-config-artifact* – we will store there all artifacts after each build.

To do this first go to the Server by performing the tasks below.

Click Terminal > New Terminal

On the right side at the bottom click Git bash. The screen below will be displayed.

![Git Bash 1](./Images/Git%20Bash%201.png)

![Git Bash 2](./Images/Git%20Bash%202.png)



CD into downloads to confirm the location where the pem key (PBL_All_Projects.pem).

Run `ls`

![Contents of downloads](./Images/Contents%20of%20downloads.png)


Run the code below. You will need to repeat the steps below if you turned off your systems.

 ```
eval `ssh-agent -s`
```

It will show the agent PID

![ssh agent](./Images/ssh%20agent.png)
   
     

Now you need to add the private key.

` ssh-add <path-to-private-key>`

Run;

 `ssh-add PBL_All_Projects.pem`


![Add private key](./Images/Add%20private%20key.png)


Confirm the key has been added with the command below, you should see the name of your key

 `ssh-add -l`

![Confirm added key](./Images/Confirm%20added%20key.png)

Now, ssh into your Jenkins-Ansible server using ssh-agent. If connecting for the first time click Yes when prompted.


Run;

`ssh -A ubuntu@public IP address for jenkins-ansible`

`ssh -A ubuntu@3.134.112.165`

![Connect to Jenkins Ansible Server](./Images/Connect%20to%20Jenkins%20Ansible%20Server.png)

We will be able to connect to the Jenkins Ansible server through any instances that we would create as long as the same pem key is used.

Run the command below;

`pwd`

![pwd](./Images/pwd.png)

For every build that we created in Project 11, a folder was created. The command below will show all the folders that have been created.

`ls /var/lib/jenkins/jobs/ansible/builds`

![Build folders](./Images/Build%20folders.png)

In a normal CI/CD , we will quickly use up space if this is left like this as there are several developers who are creating different builds. This project will help us see how we can have less folders.



Now going back to the first step, run the command below to create a new directory called *ansible-config-artifact* 

`sudo mkdir /home/ubuntu/ansible-config-artifact`


![ansible-config-artifact](./Images/ansible-config-artifact.png)

2. Change permissions to this directory, so Jenkins could save files there – 

   `sudo chmod -R 0777 /home/ubuntu/ansible-config-artifact`

   ![Change permissions](./Images/Change%20permissions.png)


    We will need to create an explorer page in VSC for the directory we will be working in.

    You can go to file explorer and create a folder for `ansible-config-artifact` under DKWORKSPACE. This will be displayed on the left side under DKWORKSPACE.

   ![ansible-config-artifact1](./Images/ansible-config-artifact1.png)



3. Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on *Available* tab search for *Copy Artifact* and install this plugin without restarting Jenkins

   Login to ansible through the browser. Use your Public IP address for Jenkins_Ansible server.

   http://3.21.170.181:8080

   The screen below will be displayed.



   ![Jenkins_Ansible Login2](./Images/Jenkins_Ansible%20Login2.png)


   Login with your credentials.

   Username : Project9_Admin

   Password: password

   The screen below will be displayed.

   ![Jenkins_Ansible3](./Images/Jenkins_Ansible%20Login3.png)

   The screen below will be displayed.

   ![Copy Artifacts](./Images/Copy%20Artifact.png)


   Select Copy Artifact and click Install withouth restart.

   ![Copy Artifacts1](./Images/Copy%20Artifact1.png)

   The screen below will be displayed. It will show that the installation is successful.

   ![Copy Artifact2](./Images/Copy%20Artifact2.png)

4. Create a new Freestyle project (you have done it in Project 9) and name it save_artifacts.


   Click Dashboard to Go back.

   ![Dashboard](./Images/Dashboard.png)

   Click New Item.


      
   ![New Item](./Images/New%20Item.png)

    Enter item name as *save_artifacts*, click on *Frestyle project* and click ok.

    ![Freestyle project](./Images/Freestyle%20project.png)



5. This project will be triggered by completion of your existing ansible project. Configure it accordingly:

   Click on save_artifacts

   ![Save_artifacts](./Images/Save_artifacts.png)

   Click on Configure.

   ![Configure](./Images/Configure.png)

   The General tab will be displayed.

   ![General tab](./Images/General%20tab.png)

   Check discard old builds > Log rotation under strategy > 2 for max number of bulds to keep. This can be whatever you want.

   ![Configuration 1](./Images/Configuration%201.png)

   Source Code Management is None > Under Build triggers check Build after other projects are built > Put ansible under project to watch > select trigger only if build is stable

   ![Configuration 2](./Images/Configuration%202.png)


   Under Build Steps , select copy artifacts from another project > ansible for project name > ** for artifacts to copy.

   ![Configuration 3](./Images/Configuration%203.png)

   Under target directory put /home/ubuntu/ansible-config-artifact

   Click Save

   ![Configuration 4](./Images/Configuration%204.png)

   We configured the number of build to 2. This is useful because whenever the jenkins pipeline runs, it creates a directory for the artifacts and it takes alot of space. By specifying the number of build, we can choose to keep only 2 of the latest builds and discard the rest.

6. Test your set up by making some change in README.MD file inside your ansible-config-mgt repository (right inside main branch).

   To do this , login to Github.

   ![Test changes](./Images/Test%20changes.png)

   Select ansible-config-mgt

   ![Test changes](./Images/Test%20changes.png)

   Ensure that yiou are under the main branch and click on READ.md file

   ![Test changes 1](./Images/Test%20changes%201.png)

   Change the public IP address for Jenkins-ansible server under webhooks if your system has been turned off.

   ![Webhooks update](./Images/Webhooks%20update.png)

   ![Webhooks update 1](./Images/Test%20changes.png)

   Click update webhooks



   Go back under code, click on Edit and make a change at the bottom. I just added : Testing changes for project 12 - March 15, 2023.

   ![Test changes 2](./Images/Test%20changes%202.png)

   Click on Commit changes.

   ![Test changes 3](./Images/Test%20changes%203.png)

   It will show build # 1 created.

   ![test changes 4](./Images/Test%20changes%204.png)

   If both Jenkins jobs have completed one after another – you shall see your files inside /home/ubuntu/ansible-config-artifact directory and it will be updated with every commit to your master branch.

   Now your Jenkins pipeline is more neat and clean.

   ## REFACTOR ANSIBLE CODE BY IMPORTING OTHER PLAYBOOKS INTO SITE.YML


   ## Step 2 – Refactor Ansible code by importing other playbooks into site.yml

   Before starting to refactor the codes, ensure that you have pulled down the latest code from master (main) branch, and created a new branch, name it refactor.

   DevOps philosophy implies constant iterative improvement for better efficiency – refactoring is one of the techniques that can be used, but you always have an answer to question "why?". Why do we need to change something if it works well?

   In Project 11 you wrote all tasks in a single playbook common.yml, now it is pretty simple set of instructions for only 2 types of OS, but imagine you have many more tasks and you need to apply this playbook to other servers with different requirements. In this case, you will have to read through the whole playbook to check if all tasks written there are applicable and is there anything that you need to add for certain server/OS families. Very fast it will become a tedious exercise and your playbook will become messy with many commented parts. Your DevOps colleagues will not appreciate such organization of your codes and it will be difficult for them to use your playbook.

   Most Ansible users learn the one-file approach first. However, breaking tasks up into different files is an excellent way to organize complex sets of tasks and reuse them.

Let see code re-use in action by importing other playbooks.

1. Within playbooks folder, create a new file and name it site.yml – This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed. Including common.yml that you created previously. 

   ![site.yml](./Images/site.yml.png)

2. Create a new folder in root of the repository and name it static-assignments. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. You will see why the folder name has a prefix of static very soon. For now, just follow along.

   ![static-assignments](./Images/static-assignments.png)

3. Move common.yml file into the newly created static-assignments folder. Just drag and drop the common.yml file into static-assignments.

   ![Move common.yml](./Images/Move%20common.yml.png)

4. Inside site.yml file, import common.yml playbook.

   ```

   ---
   - hosts: all
   - import_playbook: ../static-assignments/common.yml
   ```

   ![Import common.yml](./Images/Import%20common.yml.png)


   The code above uses built in import_playbook Ansible module.

   Your folder structure should look like this;

   ```

   ├── static-assignments
   │   └── common.yml
   ├── inventory
      └── dev
      └── stage
      └── uat
      └── prod
   └── playbooks
      └── site.yml
   ```

   ![Folder structure](./Images/Folder%20structure.png)

5. Run ansible-playbook command against the dev environment.

   Since you need to apply some tasks to your dev servers and wireshark is already installed – you can go ahead and create another playbook under static-assignments and name it common-del.yml. In this playbook, configure deletion of wireshark utility.

```
   ---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    yum:
      name: wireshark
      state: removed

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    apt:
      name: wireshark-qt
      state: absent
      autoremove: yes
      purge: yes
      autoclean: yes
```

![Delete wireshark](./Images/Delete%20wireshark.png)

update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers:

![common-del.yml](./Images/common-del.yml.png)

Go to powershell terminal and change directory to ansible-config-mgt.

`cd ansible-config-mgt`

![Directory change](./Images/Directory%20change.png)

Run `git status`

![Git status](./Images/Git%20status.png)


Run `git pull`

![Git pull](./Images/Git%20pull.png)


Click on the Source Control - pending changes. It currently shows 60.


![Source Control](./Images/Source%20Control.png)

Name the folder as updates and click on the check mar to commit.

![Source Control_1](./Images/Source%20Control_1.png)

Click on since changes.




 We need to point the ansible configuration file to inventory directory.

 Run `pwd`

 ![pwd_1](./Images/pwd_1.png)

 Run `cd inventory`

 Run `pwd`

 ![Inventory_pwd](./Images/Inventory_pwd.png)

 copy the path as shown below;

 /home/ubuntu/ansible-config-artifact/inventory































































