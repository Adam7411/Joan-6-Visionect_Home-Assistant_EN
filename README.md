If you want to directly install Visionect Software Suite in Home Assistant, click-> [visiononect-v3-allinone] (https://github.com/adam7411/visionect-v3-allinone)


# Visionect Software Suite - Installation in Proxmox

<img width="841" alt="logojoan" src="https://github.com/user-attachments/assets/36499aed-4e26-404e-b75d-13082303f039" /> 

# Visionect Joan 6 + Home Assistant Integration (English Version)
--> [Polish version](https://github.com/Adam7411/Joan-6-Visionect_Home-Assistant)

The Joan 6 E-Ink display by Visionect, originally created for conference room management, also works excellently as a Home Assistant control panel thanks to its low power usage, touchscreen, and flexibility.


---

![Example1](https://github.com/user-attachments/assets/054cda40-bb31-4192-9b8d-c88860b5e144)
![Example2](https://github.com/user-attachments/assets/440e108e-4ffa-497a-893c-9be2b7d67f02)
![Example3](https://github.com/user-attachments/assets/db4b8a13-7e46-43b6-a745-67962b7457ab)

---

## 🧰 Prerequisites

* A server with **Proxmox VE** and internet access.  If you want to directly install Visionect Software Suite in Home Assistant, click-> [visiononect-v3-allinone] (https://github.com/adam7411/visionect-v3-allinone)
* Root (admin) privileges.
* A computer with Windows, Linux, or macOS.
* A **Visionect Joan 6** device.
* Add-on HASC [Visionect Joan](https://github.com/Adam7411/visionect_joan)
> **Note:** A Visionect Software Suite Docker container must run continuously. If you're already using Home Assistant on Proxmox, this setup adds minimal overhead.

---

## 📦 Step 1: Install Docker in a Proxmox VM

1. Access the **Proxmox VE** web interface.
2. Select your node and open the **Shell**.
3. Run the following script:

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/docker-vm.sh)"
```

4. This creates a new VM with Docker installed.

---

## ✏️ Step 2: Create the `docker-compose.yml` File

1. Open the new Docker VM console (login: `root`).
2. Create a file:

```bash
nano docker-compose.yml
```

3. Paste the contents from:
   👉 [docker-compose.yml](https://github.com/Adam7411/Joan-6-Visionect_Home-Assistant/blob/main/docker-compose.yml)
4. Adjust image depending on CPU:

   * **x86\_64 (Intel/AMD):**

     ```yaml
     image: visionect/visionect-server-v3:7.6.5
     ```
   * **ARM (e.g., Raspberry Pi):**

     ```yaml
     image: visionect/visionect-server-v3:7.6.5-arm
     ```
5. Save and exit: `Ctrl+O`, `Enter`, then `Ctrl+X`
6. Confirm file was created:

```bash
ls -l docker-compose.yml
```

---

## 🚀 Step 3: Start Docker Containers

1. Start services:

```bash
docker compose up -d
```

2. Wait while Docker pulls `postgres_db`, `redis`, and `vserver3`.
3. Check status:

```bash
docker ps
```

---

## 🌐 Step 4: Open the Visionect Software Suite

1. Open a browser and go to: `http://<your_vm_ip>:8081`
2. Set admin password on first login.
3. Use `admin` as username next time.

---

## 📲 Step 5: Configure the Tablet

1. Download Visionect Configurator:

   * Windows: [VisionectConfigurator.exe](https://files.visionect.com/VisionectConfigurator/VisionectConfigurator.exe)
   * Linux: [VisionectConfigurator\_linux.deb](https://files.visionect.com/VisionectConfigurator/VisionectConfigurator_linux.deb)
   * macOS: [Intel](https://files.visionect.com/VisionectConfigurator/VisionectConfigurator_intel.dmg) | [Apple Silicon](https://files.visionect.com/VisionectConfigurator/VisionectConfigurator_m1.dmg)
   * Older version: [1.3.10](https://files.visionect.com/VisionectConfigurator2.exe) [VC_1.exe](https://files.visionect.com/VC_1.exe)

2. Connect the tablet to USB.

3. Select Wi-Fi, enter password.

4. Go to **Advanced Connectivity**:

   * Server IP: e.g., `192.168.1.100`
   * Port: `11113`
     
 Visionect Configurator 2.0
![image](https://github.com/user-attachments/assets/de30fd1e-9bd3-4f98-ab00-9a3b534f7332)

Visionect Configurator 1
<img width="754" height="501" alt="Bez tytułu" src="https://github.com/user-attachments/assets/79a42b61-659c-469f-b593-0122ff8e8e82" />


5. Click connect. The tablet should appear in the Visionect server panel.

---

## ✏️ Step 6: Create a Dashboard for Home Assistant

> You can also use [Puppeteer version](https://github.com/Adam7411/Joan-6-Puppeteer/blob/main/README.md) if you don't want to use AppDaemon.

1. Install **AppDaemon** in Home Assistant.
2. Go to: `\HA_IP\config\appdaemon\dashboards\` \addon_configs\a7c7b154_appdaemon\dashboards\ )
3. Create a file, e.g., `joan1.dash`
4. Example files:

   * [joan1.dash](https://github.com/Adam7411/Joan-6-Visionect_Home-Assistant/blob/main/joan1.dash)
   * [joan2.dash](https://github.com/Adam7411/Joan-6-Visionect_Home-Assistant/blob/main/joan2.dash)
5. Edit and insert your own Home Assistant entities.
6. Restart AppDaemon.
7. Test: `http://<HA_IP>:5050/joan1` `http://<HA_IP>:5050/joan2`
8. Copy URL, paste into **Default URL** in Visionect dashboard settings.
9. Adjust **Refresh Rate** if necessary (e.g., 2 seconds initially). 
Dashboard can also be set from home assistant by add -on👉 [Visionect Joan](https://github.com/Adam7411/visionect_joan) 👈
---

Visionect Joan for Home Assistant (Reading the State of the Tablet and sending photos of URL and your own text)
Integration for reading information about the status of the Joan tablet in Home Assistant (e.g. battery level, connection status, etc.), as well as for sending your own URL ( e.g. https://www.wikipedia.org/ ) or local images ( example: http://HAaddress:8123/local/test_image.png
P.S. The file test_image.png should be placed in the folder: \\192.168.xxx.xxx\config\www\

👉 [Visionect Joan](https://github.com/Adam7411/visionect_joan) 👈

This allows for creating automations, such as sending low battery notifications, displaying an entity with battery level on the tablet, or sending images for different notifications, and then returning to the AppDaemon dashboard, etc.
This integration provides the following entities and services:

Sensors:

Battery Level
Total & Used Storage
Uptime
Charging Status (Binary Sensor)
Screen Refresh Interval (Number)
Battery Voltage
Device Status (Online/Offline)
Wi-Fi Signal Strength
Temperature
Configured URL
Last Seen Timestamp
Services:

visionect_joan.send_text: Send text messages, now with image support and layout options. **Supports Jinja2 templates for dynamic content.**
visionect_joan.set_url: Display a custom URL (e.g., a website like https://www.wikipedia.org/ or a local image like http://<your_ha_ip>:8123/local/test_image.png).
visionect_joan.clear_display: Wipes the screen.
visionect_joan.force_refresh: Immediately reloads the content from the configured URL.

<img width="510" height="739" alt="3" src="https://github.com/user-attachments/assets/8f8c673d-8447-42ec-9d13-0bd4e9683437" /> <img width="948" height="791" alt="2" src="https://github.com/user-attachments/assets/4a3c054a-e239-49c1-ab9d-037584cd7989" /> <img width="607" height="893" alt="1" src="https://github.com/user-attachments/assets/1321cfe8-905d-44ef-b1b9-29d999559a04" /> <img width="770" height="641" alt="4" src="https://github.com/user-attachments/assets/31e9bca1-d7c6-4245-b32f-4c909251bf2c" />


