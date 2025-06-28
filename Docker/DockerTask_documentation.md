# Installation Steps

**Update system:**

sudo apt update && sudo apt upgrade -y
![Items (1)](../DevOps_Pictures/items-1.png)


![Screenshot from 2025-04-24 13-08-02.png](/.attachments/Screenshot%20from%202025-04-24%2013-08-02-474c3776-2c7a-44a9-a91a-713053cfe664.png)

**Install dependencies:**

sudo apt install ca-certificates curl gnupg lsb-release
![Screenshot from 2025-04-24 13-47-04.png](/.attachments/Screenshot%20from%202025-04-24%2013-47-04-fb88360f-15b2-48ca-9d03-093e4fc561ad.png)

**Add Docker GPG key:**

sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

**Add Docker’s repository:**

echo \
"deb [arch=$(dpkg --print-architecture) \
signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

**Install Docker packages:**

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
![Screenshot from 2025-04-24 13-49-49.png](/.attachments/Screenshot%20from%202025-04-24%2013-49-49-5eef630b-9855-4bcc-ab3f-32520894f26c.png)

**Verify installation:**
docker --version
![Screenshot from 2025-04-24 13-51-59.png](/.attachments/Screenshot%20from%202025-04-24%2013-51-59-67310bfc-8935-4825-9344-481b35ac1e72.png)
**Add user permission:**

sudo usermod -aG docker $USER

after running the command Now you must log out from your os and log back in, or reboot your system to apply the  change.

Logging out and back in is important, otherwise the new permissions won’t apply!

**After re-login, test it again:**
After you have completed the installation, you can verify the installation is successful by running the hello-world image.

docker run hello-world

![Screenshot from 2025-04-24 15-58-55.png](/.attachments/Screenshot%20from%202025-04-24%2015-58-55-685d41b7-b257-43e1-9df9-83646570c4de.png)

ref urls: https://docs.docker.com/engine/install/ubuntu/
https://www.tutorialspoint.com/docker/installing_docker_on_linux.htm
