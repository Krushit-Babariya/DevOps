# Jenkins Server Setup in Linux VM #

## Step - 1 : Create Linux VM ##

1) Create Ubuntu VM using AWS EC2 (t2.medium) <br/>
2) Enable 8080 Port Number in Security Group Inbound Rules
3) Connect to VM using MobaXterm

## Step-2 : Install Java ##

```
sudo apt update
sudo apt install fontconfig openjdk-17-jre
java -version
```

## Step-3 : Install Jenkins ##
```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

## Step-4 : Configure Firewall (if using UFW) ##

```
sudo ufw allow 8080
sudo ufw status
```

## Step-5 : Start Jenkins ## 

```
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

## Step-6 : Verify Jenkins ##

```
sudo systemctl status jenkins
```

## Step-7 : Check Jenkins Service Logs (if needed) ##

```
sudo journalctl -u jenkins -f
```

## Step-8 : Open jenkins server in browser using VM public ip ##

```
http://public-ip:8080/
```

## Step-9 : Copy jenkins admin pwd ##
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

## Step-10 : Create Admin Account & Install Required Plugins in Jenkins ##

1) **Create Admin User:**
   - Username: admin
   - Full name: Administrator
   - Email: admin@yourdomain.com
   - Password: (use a strong password)

2) **Install Suggested Plugins**
   - Or choose "Select plugins to install" for custom selection

3) **Essential Plugins to Install:**
   - Git plugin
   - Pipeline plugin
   - Docker plugin
   - SSH plugin
   - Credentials plugin
   - Blue Ocean (optional - for better UI)

## Step-11 : Configure Jenkins Security ##

1) **Configure Global Security Settings:**
   - Go to Manage Jenkins > Configure Global Security
   - Enable "Enable security"
   - Choose "Jenkins' own user database"
   - Enable "Allow users to sign up"
   - Set "Authorization" to "Matrix-based security" or "Role-based strategy"

2) **Create Additional Users (Optional):**
   - Go to Manage Jenkins > Manage Users
   - Create users for team members

## Step-12 : Configure Jenkins URL ##

1) Go to Manage Jenkins > Configure System
2) Set Jenkins URL: `http://your-public-ip:8080/`

## Step-13 : Install Additional Tools (Optional) ##

```
# Install Docker (if needed for containerized builds)
sudo apt-get update
sudo apt-get install docker.io
sudo usermod -aG docker jenkins
sudo systemctl start docker
sudo systemctl enable docker

# Install Git (if not already installed)
sudo apt-get install git

# Install Maven (if needed for Java projects)
sudo apt-get install maven

# Install Node.js (if needed for JavaScript projects)
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs
```

## Step-14 : Configure Jenkins Tools ##

1) Go to Manage Jenkins > Global Tool Configuration
2) Configure:
   - JDK installations
   - Maven installations
   - Git installations
   - Docker installations

## Step-15 : Create First Jenkins Job ##

1) Click "Create a new job"
2) Choose "Freestyle project"
3) Configure basic settings:
   - Name: "Hello-World"
   - Description: "My first Jenkins job"
4) Add build step: "Execute shell"
5) Command: `echo "Hello from Jenkins!"`
6) Save and run the job

## Step-16 : Backup and Maintenance ##

```
# Backup Jenkins home directory
sudo tar -czf jenkins-backup-$(date +%Y%m%d).tar.gz /var/lib/jenkins/

# Check disk space
df -h

# Monitor Jenkins logs
sudo tail -f /var/log/jenkins/jenkins.log
```

## Troubleshooting ##

### Common Issues:

1. **Jenkins won't start:**
   ```
   sudo systemctl status jenkins
   sudo journalctl -u jenkins -f
   ```

2. **Port 8080 already in use:**
   ```
   sudo netstat -tulpn | grep :8080
   sudo lsof -i :8080
   ```

3. **Permission issues:**
   ```
   sudo chown -R jenkins:jenkins /var/lib/jenkins
   sudo chmod -R 755 /var/lib/jenkins
   ```

4. **Java version issues:**
   ```
   java -version
   sudo update-alternatives --config java
   ```

## Security Best Practices ##

1. **Change default admin password**
2. **Use HTTPS in production**
3. **Regular security updates**
4. **Limit network access**
5. **Regular backups**
6. **Monitor logs for suspicious activity**
7. **Use strong passwords for all users**
8. **Disable unnecessary plugins**