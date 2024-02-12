# 1. Configure smtp in localhost.

## Steps:
### 1: We need to install postfix first.
 ```bash
sudo apt update -y
sudo apt install postfix
```
### 2: Edit the configuration file
   Location: /etc/postfix/mail.cf 
   Add below things.
```bash
smtp_sasl_auth_enable = yes
smtp_sasl_security_options = noanonymous
smtp_sasl_password_maps = hash:/etc/postfix/sasl/sasl_passwd
smtp_tls_security_level = encrypt
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
```

### 3: We’ll create a file to store our credentials. Before that, create an app password in your gmail.
   sudo nano /etc/postfix/sasl/sasl_passwd
```bash
add line [smtp.gmail.com]:587	yourgmail:app_password
```

### 4: We’ll give this file permission in such a way that root can only read by root
```bash
sudo chmod 600 /etc/postfix/sasl/sasl_passwd
```
### 5: Update the postfix so that it’ll use the credentials.
```bash
sudo postfix /etc/postfix/sasl/sasl_passwd
```
### 6: Restart the postfix and check if it’s running or not
```bash
sudo systemctl restart postfix
sudo systemctl status postfix
```


### 7: Send a mail using ‘sendmail’ command
sendmail receipent@gmail.com then enter
Subject: Test mail
Body
. (if you add a . that means you have finished your writing)
Your mail has been delivered.

# 2. Create a user in your localhost, which should not be able to execute the sudo command.
## Steps:
### 1: First create a user
```bash
sudo adduser username
```
### 2: Check the user created successfully or not
```bash
cat /etc/passwd
```
### 3: Remove the user from sudo group
```bash
sudo deluser username sudo
```
# 3. Configure your system in such a way that when a user type and executes a describe command from anywhere of the system it must list all the files and folders of the user's current directory.
Ex:- $ describe
$ content1 content2
Content3 content 4
(We can achieve it through alias)
## Steps:
### 1: open .bashrc file
```bash
sudo nano ~/.bashrc
```
### 2: Add one line
 Adding the alias here
```bash
alias describe=’ls -al “pwd”’
```
### 3: Then we need to restart .bashrc file
``` bash
source ~/.bashrc
```

# 4. Users can put a compressed file at any path of the linux file system. The name of the file will be research and the extension will be of compression type, example for gzip type extension will be .gz. You have to find the file and check the compression type and uncompress it.

## Steps:
### 1: This command will do the task
``` bash
find / -name "research.gz" -exec gunzip {} \;
or, gunzip -k $(find /home -type f -name "*.gz" -print -quit 2>/dev/null)
``` 
# 5. Configure your system in such a way that any user of your system creates a file then there should not be permission to do any activity in that file.
Note:- Don’t use the chmod command.

## Steps:
Here we’ll use the concept of umask. It sets default permissions for a newly created file and directory. (for file 666, for directory 777)

### 1: Set the umask value
```bash
umask 077
```

# 6. Create a service with the name showtime , after starting the service, every minute it should print the current time in a file in the user home directory.
Ex:-
sudo service showtime start -> It should start writing in file.
sudo service showtime stop -> It should stop writing in file.
sudo service showtime status -> It should show status.

## Steps:
### 1. First we’ll write a shell script that creates a showtime.log file which holds the current time in every minute.

```bash
#!/bin/bash
log_file="$HOME/showtime.log"
while true; do
echo "$(date)" >> "$log_file"
sleep 60
done
```

### 2. Giving permission to the shell script
```bash
sudo chmod a+x showtime.sh
```
### 3. Creating a systemd service unit file named showtime.
Location: /etc/systemd/system
```bash
sudo nano /etc/systemd/system/showtime.service
```
### 4. We’ll add some content that defines the service.
```bash
[Unit]
Description=Showtime Service
[Service]
Type=simple
ExecStart=/path/to/showtime.sh
Restart=always
RestartSec=60
[Install]
WantedBy=multi-user.target
```

### 5. Enable and start the service
```bash
sudo systemctl enable showtime.service
sudo systemctl start showtime.service
```

