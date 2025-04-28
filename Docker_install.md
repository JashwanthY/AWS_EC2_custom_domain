"Here’s a step-by-step guide to install Docker and Docker Compose on your Ubuntu EC2 instance to run your containerized application:

---

### **1. Update Package Repository**
Run the following commands to update your package index:
```bash
sudo apt-get update
sudo apt-get upgrade -y
```

---

### **2. Install Docker**
1. **Install Prerequisite Packages**:
   ```bash
   sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
   ```

2. **Add Docker's Official GPG Key**:
   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```

3. **Set up Docker's Stable Repository**:
   ```bash
   echo ""deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

4. **Install Docker Engine**:
   ```bash
   sudo apt-get update
   sudo apt-get install -y docker-ce docker-ce-cli containerd.io
   ```

5. **Verify Docker Installation**:
   ```bash
   sudo docker --version
   ```

---

### **3. Install Docker Compose**
1. **Download the Latest Version of Docker Compose**:
   ```bash
   sudo curl -L ""https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)"" -o /usr/local/bin/docker-compose
   ```

2. **Set Executable Permissions**:
   ```bash
   sudo chmod +x /usr/local/bin/docker-compose
   ```

3. **Verify Docker Compose Installation**:
   ```bash
   docker-compose --version
   ```

---

### **4. Manage Docker as a Non-Root User**
To run Docker without `sudo`:
1. **Add Your User to the Docker Group**:
   ```bash
   sudo usermod -aG docker $USER
   ```
2. **Log Out and Log Back In** for the changes to take effect.

---

### **5. Test Docker Installation**
Run the following command to test Docker:
```bash
sudo docker run hello-world
```

---

### **6. Deploy Your Application Using Docker Compose**
1. **Upload Your `docker-compose.yml` File** to the server.
2. Navigate to the directory containing the file and run:
   ```bash
   docker-compose up -d
   ```
3. Verify that the containers are running:
   ```bash
   docker ps
   ```

---

Let me know if you encounter any issues during the setup!"
