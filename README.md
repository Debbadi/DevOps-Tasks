# ***DevOps Intern Evaluation Task: Implementing Automation for Azure Spot VM Restart***

## 1. Install Zabbix on Your Local PC Using Docker or a VM:

To set up Zabbix using Docker, follow these steps:

- I Pull the necessary Docker images:

- I Run the following commands to pull the required images for Zabbix and MySQL:

    - docker pull zabbix/zabbix-server-mysql
    - docker pull zabbix/zabbix-web-nginx-mysql
    - docker pull mysql
    - docker pull zabbix/zabbix-agent



**II. Create a Docker Network:**

Before running the Zabbix containers, I have created a custom Docker network. This network allows the containers to communicate with each other using internal DNS names.

Run the following command to create the network:

     -docker network create zabbix-net



After setting up the Docker network, the next step is to create and run the necessary containers for Zabbix and MySQL. The following commands will pull and run the required containers, each configured to work together within the zabbix-net network.

### a. Run the MySQL Server Container:

To run the MySQL server container, use the following command:


    -docker run -d --name mysql-server \
    --network zabbix-net \
    -e MYSQL_DATABASE=zabbix \
    -e MYSQL_USER=zabbix \
    -e MYSQL_PASSWORD=zabbix_password \
    -e MYSQL_ROOT_PASSWORD=root_password \
    mysql:latest

This command creates and runs a MySQL container named mysql-server. The environment variables (-e) are used to set up the database (MYSQL_DATABASE=zabbix), user (MYSQL_USER=zabbix), user password (MYSQL_PASSWORD=zabbix_password), and the root password (MYSQL_ROOT_PASSWORD=root_password). The container is attached to the zabbix-net network, allowing it to communicate with other Zabbix components.


### b. Run the Zabbix Server Container:


    -docker run -d --name zabbix-server \
    --network zabbix-net \
    -e DB_SERVER_HOST=mysql-server \
    -e MYSQL_DATABASE=zabbix \
    -e MYSQL_USER=zabbix \
    -e MYSQL_PASSWORD=zabbix_password \
    -e MYSQL_ROOT_PASSWORD=root_password \
    zabbix/zabbix-server-mysql:latest


This command creates and runs a Zabbix Server container named zabbix-server. The server is configured to connect to the MySQL database hosted by the mysql-server container using the specified environment variables. The DB_SERVER_HOST=mysql-server points the Zabbix server to the MySQL container, ensuring they work together seamlessly within the zabbix-net network.

### c. Run the Zabbix Web Interface (Nginx) Container:

    -docker run -d --name zabbix-web-nginx \
    --network zabbix-net \
    -e DB_SERVER_HOST=mysql-server \
    -e MYSQL_DATABASE=zabbix \
    -e MYSQL_USER=zabbix \
    -e MYSQL_PASSWORD=zabbix_password \
    -e ZBX_SERVER_HOST=zabbix-server \
    -e PHP_TZ=America/Chicago \
    -p 8080:8080 \
    zabbix/zabbix-web-nginx-mysql:latest

This command sets up the Zabbix Web Interface container named zabbix-web-nginx. It connects to both the MySQL database and the Zabbix server using the DB_SERVER_HOST=mysql-server and ZBX_SERVER_HOST=zabbix-server environment variables. The PHP_TZ is set to the desired timezone (America/Chicago), and port 8080 is mapped for accessing the web interface. The container is also attached to the zabbix-net network.

### d. Run the Zabbix Agent Container:

    -docker run -d --name zabbix-agent \
    --network zabbix-net \
    -e ZBX_HOSTNAME="zabbix-agent" \
    -e ZBX_SERVER_HOST="zabbix-server" \
    zabbix/zabbix-agent:latest

Finally, this command sets up the Zabbix Agent container named zabbix-agent. The agent is configured to report to the Zabbix server (ZBX_SERVER_HOST="zabbix-server") and uses the hostname zabbix-agent. The container is also connected to the zabbix-net network, allowing it to monitor and send data to the Zabbix server.

***Below are the images that I have pulled and run using these commands.***

<img width="1433" alt="image" src="https://github.com/user-attachments/assets/6513abde-4f26-4f60-bfeb-1d0e54909f92">



<img width="1433" alt="image" src="https://github.com/user-attachments/assets/2b62135d-1d37-4982-9a4e-3c5b1c787d3c">


***This is the Zabbix interface dashboard, and I set up the Zabbix server.***

<img width="1433" alt="image" src="https://github.com/user-attachments/assets/af50546d-8b9a-46d6-bf8d-3e00f8943baf">


<img width="1433" alt="image" src="https://github.com/user-attachments/assets/eb4f8369-69f1-4536-a681-99b7effdcafa">


## 2. Create a Small Size Linux Spot VM on Azure:


I have successfully created the VM according to the specified requirements. I used my college account for the setup and ensured everything was configured as requested.

<img width="1433" alt="image" src="https://github.com/user-attachments/assets/0f3213bf-5570-48f7-ae8e-19d12a19ad6b">



## 3. Install Zabbix Agent on Your Spot VM:


***a. ssh sk@20.2.65.100***


This command is used to securely connect to my remote Linux VM via SSH. I am connecting as the user sk to the VM with the public IP address 20.2.65.100.

***b. sudo apt-get update***

This command updates the package lists on my VM. It ensures that I have the latest information on the available packages and their versions before I am starting installing new software.

***c. wget https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1%2Bubuntu24.04_all.deb***

This command uses wget to download the Zabbix repository package for Ubuntu 24.04 from the Zabbix official repository. This package is needed to add the Zabbix repository to my system.

***d. sudo dpkg -i zabbix-release_6.4-1+ubuntu24.04_all.deb***


This command installs the Zabbix repository package that I have just downloaded. dpkg -i is used to install .deb packages on Debian-based systems like Ubuntu.

***e. sudo apt-get update***
After adding the Zabbix repository, this command updates the package lists again. This ensures that system recognizes the new Zabbix packages that are now available for installation.


***f. sudo apt-get install zabbix-agent***

This command installs the Zabbix agent on VM. The Zabbix agent is responsible for collecting data from the system and sending it to the Zabbix server for monitoring.

***g. sudo vi /etc/zabbix/zabbix_agentd.conf***

This command opens the Zabbix agent configuration file in the vi text editor. I will use this file to configure the agent by specifying the Zabbix server IP and the hostname of the VM.

***h.sudo systemctl restart zabbix-agent***

This command restarts the Zabbix agent service to apply the changes that made to the configuration file.

***i. sudo systemctl enable zabbix-agent***

This command enables the Zabbix agent service to start automatically when the system boots. This ensures that the Zabbix agent will always be running, even after a system reboot.


<img width="1433" alt="Screenshot 2024-08-16 at 10 26 21 PM" src="https://github.com/user-attachments/assets/d1b29f3c-aa8d-4941-b0a3-555c69897ce9">


## 4. Design Automation to Turn on Your VM Every 5 Minutes When Off:

I could not complete the task of designing automation to turn on my VM every 5 minutes when it is off.

I tried multiple approaches, including writing the bash script myself, searching on Google, and using AI tools like ChatGPT and Copilot, but I couldn't get the script to work. I have successfully completed the first three tasks, but I encountered difficulties with the fourth one.

"Some of the approaches I attempted include:"

    #!/bin/bash

    RESOURCE_GROUP="new__group"
    VM_NAME="assign"

    VM_STATUS=$(az vm get-instance-view --resource-group $RESOURCE_GROUP --name $VM_NAME --query "instanceView.statuses[1].code" -o tsv)

    echo "Current status of $VM_NAME: $VM_STATUS"

    if [ "$VM_STATUS" == "PowerState/deallocated" ]; then
            echo "VM $VM_NAME is off. Starting VM..."

    az vm start --resource-group $RESOURCE_GROUP --name $VM_NAME

        echo "VM $VM_NAME started."
    else
        echo "VM $VM_NAME is already running."
    fi


I created and tested a script that checks the status of the VM and restarts it if necessary but it didnt work
Initially, I stored the script in the Zabbix agent directory (/etc/zabbix/) and set up a cron job to execute it every 5 minutes. 

    -*/5 * * * * /etc/zabbix/vm_check_and_restart.sh >> /var/log/vm_check_and_restart.log 2>&1


I have also verified that the script has the correct permissions and can be executed manually without issues.

Unfortunately, the cron job did not execute as expected.


I am currently troubleshooting the issue to identify why the cron job isn't running as intended.

    *Verifying the cron service is running properly.

    *Checking for any potential errors in the cron job syntax.

    *Reviewing system logs to identify any related errors or warnings.

    *checking the script  



"I utilized tools such as ChatGPT and Copilot to solve this problem."


# If you have any suggestions to take a different approach, please let me know. I’ll continue working on this and keep you updated on my progress.

# Thanks for the opportunity
