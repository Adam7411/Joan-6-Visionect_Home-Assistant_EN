

<img width="841" alt="logojoan" src="https://github.com/user-attachments/assets/36499aed-4e26-404e-b75d-13082303f039" /> <img width="841" alt="aaan" src="https://github.com/user-attachments/assets/8327c8a7-910d-4863-bdf1-94a4e1610f7c" /> <img width="841" alt="aaan" 


Visionect Joan 6 E-Ink Display for Home Assistant
The Joan 6 E-Ink display from Visionect, originally designed as a meeting room booking system, is an excellent choice for managing entities in Home Assistant, thanks to its energy efficiency, touch screen, and flexibility.

🧰 Prerequisites
A server with Proxmox VE installed and internet access.

Root (administrator) access to the Proxmox server.

A Windows, Linux, or macOS computer (for initial tablet configuration).

A Visionect Joan 6 tablet.

Important Note: A Docker container with the Visionect Software Suite (the server managing the tablet) must be running at all times. If you're already using Home Assistant on Proxmox, this won’t be an extra burden. However, HA can also run on a separate device.

📦 Step 1: Installing Docker in a Virtual Machine on Proxmox
Log into the Proxmox VE web interface.

Select your server and open the Shell console.

Paste and run the following script to automatically create a Docker VM:

bash
Kopiuj
Edytuj
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/docker-vm.sh)"
After the script completes, a new virtual machine with Docker installed will be ready.

✏️ Step 2: Create the docker-compose.yml File
Log into the newly created Docker VM via Proxmox console (login: root).

Create a new file called docker-compose.yml using nano:

bash
Kopiuj
Edytuj
nano docker-compose.yml
Copy the contents of the docker-compose.yml file from this repository:
👉 docker-compose.yml

Paste the copied content into the terminal (usually right-click in the console).

Adjust the image for your CPU architecture. Find the line starting with image::

For x86_64 (Intel/AMD): Make sure it looks like this (remove -arm):

yaml
Kopiuj
Edytuj
image: visionect/visionect-server-v3:7.6.5
For ARM (e.g., Raspberry Pi): Keep the original line:

yaml
Kopiuj
Edytuj
image: visionect/visionect-server-v3:7.6.5-arm
Save changes and exit the editor:

Ctrl + O → Enter (save the file)

Ctrl + X (exit editor)

Verify the file was created correctly:

bash
Kopiuj
Edytuj
ls -l docker-compose.yml
You should see something like: -rw-r--r-- 1 root root 1079 Jul 4 13:45 docker-compose.yml

🚀 Step 3: Start Docker Containers
In the virtual machine terminal, start the containers in the background:

bash
Kopiuj
Edytuj
docker compose up -d
This may take a moment, as Docker will download the required images:
postgres_db, redis, and vserver3.

Check container status:

bash
Kopiuj
Edytuj
docker ps
You should see three running containers.

🌐 Step 4: Access the Visionect Software Suite Panel
In your web browser, enter the IP address of your Docker VM with port 8081, e.g.:
http://192.168.1.100:8081

My example: 192.168.100.132



You should see the Visionect Server login panel.

On first launch, you’ll be prompted to set an admin password. Later, log in with the username admin and your password.



📲 Step 5: Configure the Visionect Tablet
Download and run the Visionect Configurator on your computer:

Windows: VisionectConfigurator.exe or joan-configurator-2.1.3-windows.exe

Windows: older models (v1.3.10): VisionectConfigurator1.3.10.exe or VC_1.exe
For older devices (Second Gen Visionect Sign 6) use VC_1.exe or v1.3.10 if the new one doesn't connect.

Linux: VisionectConfigurator_linux.deb

macOS (Apple Silicon): VisionectConfigurator_m1.dmg

macOS (Intel): VisionectConfigurator_intel.dmg

Connect the tablet to your computer via USB.

Once the tablet is detected:

Select your Wi-Fi network and enter the password.

Go to Advanced Connectivity tab.

Enter your server details:

Server IP: The IP address of your Docker VM (e.g., 192.168.1.100)

Port: 11113

Visionect Configurator v2.0:


Click the button to connect the tablet to the server.

After a moment, your tablet should appear in the Visionect Software Suite device list.

✏️ Step 6: Create a Dashboard in Home Assistant
(P.S. If you don’t want to use AppDaemon, try this Puppeteer-based solution)

Now let’s display a Home Assistant interface on the tablet using the AppDaemon add-on.

Install the AppDaemon add-on in Home Assistant (if not already installed).

Navigate to the AppDaemon configuration directory, e.g. via "Samba share" or "File editor":
\config\appdaemon\dashboards\ (in my case: \\HA_IP\addon_configs\a7c7b154_appdaemon\dashboards\)

Create a new file with a .dash extension, e.g., joanl.dash.

Use the ready-made dashboard templates from this repository:

joan1.dash

joan2.dash

Important: Edit joanl.dash or joan2.dash, replacing the sample entities with your actual Home Assistant entities. Full dashboard creation docs are available on the official AppDaemon site.

Restart the AppDaemon add-on to load the new configuration.

Check your dashboard in the browser:
http://<ha_ip_address>:5050/joan1
(replace joan1 with your actual file name if using a custom dashboard).

Copy this URL.

Return to the Visionect Software Suite panel, go to your tablet settings, and paste the dashboard URL into the Default URL field. Save the changes.



After a moment, your Home Assistant dashboard should appear on the tablet screen.
(If it doesn't, temporarily set Refresh rate to 2 seconds to force update, then restore your preferred value.)

Additional Tips
You can create a separate .dash file and unique URL for each tablet.

In the Visionect panel, it's worth tuning the Refresh rate to balance battery life and update frequency.


⭐ Integration with Home Assistant (Tablet State Monitoring)
To read tablet status (e.g., battery level, connection state, etc.) in Home Assistant, you can use a custom HACS integration from my other repository:
👉 Visionect Joan.
This allows for automations like battery alerts or displaying battery level as an entity on the tablet.

