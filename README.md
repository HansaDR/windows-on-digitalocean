# windows-on-digitalocean
A guide on how to install a custom Windows image on a DigitalOcean droplet.

-----

## Overview

This guide provides a step-by-step process for installing Windows on a DigitalOcean droplet. This process involves using a `.gz` file containing the Windows image and writing it to the droplet's disk. This guide is for educational purposes only. I am not responsible for any data loss or other issues that may arise from following these instructions.

-----

## Prerequisites

  * Access to a DigitalOcean account and the ability to create droplets. If you don't have an account, you can sign up using my referral link to get a credit:
    (https://www.digitalocean.com/?refcode=9ab72d603ab4&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=badge)

-----

## Guide

### 1\. Prepare Your Droplet

1.  **Create a New Droplet.** In your DigitalOcean dashboard, create a new droplet. A plan with **8GB of RAM** is preferred. For the operating system, choose a Linux distribution like **Ubuntu 22.04 or 24.04**. This base OS will be wiped in a later step.
2.  **Power Off the Droplet.** Once the droplet has finished creating, turn it off by clicking the **power toggle button** in the top-right corner of the droplet's management page.
3.  **Boot from Recovery ISO.** Go to the **Recovery** tab in the left-hand menu. Select the **"Boot from Recovery ISO"** option. Now, turn the droplet back on using the same power toggle button.
4.  **Access the Recovery Console.** After the droplet boots up, click the **"Recovery Console"** button to open the command line terminal.

### 2\. Download and Write the Windows Image

1.  **Access the Shell.** When the recovery console opens, you will see a menu with numbered options. Press **6** and hit **Enter** to exit to a shell prompt.

2.  **Run the Download Command.** Paste the command below into the terminal. This will download the Windows 11 image, decompress it, and write it to the droplet's primary disk.

    ```bash
    wget -O- 'https://www.dropbox.com/scl/fi/9sll3ctje09d1ahqdank8/windows11.gz?rlkey=c7vwlaiq5staj21en1g82gqlx&st=f8k6qpv8&dl=1' | gunzip | sudo dd of=/dev/vda bs=4M status=progress
    ```

3.  **Wait for it to Finish.** Press **Enter** to run the command. This process will take some time, and there might not be any progress indicator. Please be patient and wait for the command to complete.

### 3\. Login Credentials

You will need the following credentials to log in to Windows for the first time. You can (and should) change these later for security.

  * **User:** `root`
  * **Password:** `123@@@`

### 4\. Next Step

1.  **Power Down.** Once the download command is finished, close the terminal window and **power off** the droplet using the toggle button in the dashboard.
2.  **Boot from Hard Drive.** Go back to the **Recovery** tab and switch the boot option back to **"Boot from Hard Drive"**.
3.  **Turn On the Droplet.** Power the droplet back on.
4.  **Access Windows.** Navigate to the **Access** tab on the left-hand menu. Click the **"Launch Recovery Console"** button to open the Windows interface. **Do not use the regular Droplet Console.** Log in with the credentials provided above to begin your setup.

-----

### 4\. Configure the Network

After logging in, you must manually configure the network to get internet access. The mouse in the console can be laggy; this is normal.

1.  **Open Command Prompt as Administrator.** Right-click the Start button and select **"Command Prompt (Admin)"** or **"Windows Terminal (Admin)"**.

2.  **Find Your Interface Name.** In the command prompt, type `netsh interface ipv4 show interfaces` and press **Enter**. Find your primary network connection name in the "Interface Name" column (e.g., "Ethernet").

3.  **Get Your Network Details.** Go to your droplet's page in the DigitalOcean dashboard and click the **Networking** tab to find its **Public IP Address**, **Netmask**, and **Gateway**.

4.  **Set Static IP and DNS.** Execute the following commands one by one. Replace `"YOUR_ETHERNET_NAME"` with the name from step 2, and the other values with the details from step 3.

    ```bash
    netsh interface ipv4 set address name="YOUR_ETHERNET_NAME" static YOUR_PUBLIC_IP YOUR_NETMASK YOUR_GATEWAY
    ```

    ```bash
    netsh interface ipv4 set dns name="YOUR_ETHERNET_NAME" static 8.8.8.8
    ```

    ```bash
    netsh interface ipv4 add dns name="YOUR_ETHERNET_NAME" 8.8.4.4 index=2
    ```

    **Example:**

    ```bash
    netsh interface ipv4 set address name="Ethernet" static 152.42.240.118 255.255.240.0 152.42.220.1
    ```

    Your droplet should now be connected to the internet.

-----


### 5\. Enable Remote Desktop (RDP)

To manage your droplet easily without using the web console, you should enable Remote Desktop.

1.  **Open System Properties.** In the same Command Prompt window, type the following command and press **Enter**:
    ```bash
    sysdm.cpl
    ```
2.  **Allow Remote Connections.** A "System Properties" window will appear. Go to the **Remote** tab, select the option **"Allow remote connections to this computer"**, and then click **Apply** and **OK**.
3.  **Connect via RDP.** That's it\! You can now close the DigitalOcean console window. Use any RDP client (like the Microsoft Remote Desktop app) on your computer to connect to your droplet using its **Public IP Address** and the login credentials.
-----

Of course. Here is the new troubleshooting section.

-----

## Troubleshooting 🛠️

### Extending the Disk Partition

If you notice that your C: drive doesn't reflect the full size of your Droplet's disk, you'll need to extend the partition manually. You can do this with a tool like **Minitool Partition Wizard**.

1.  **Download and install** a partition management tool.
2.  Open the application, select your **C: drive**, and choose the **"Extend"** option.
3.  From the drop-down menu, select the **unallocated space**.
4.  Drag the slider all the way to the end to use the maximum available space.
5.  Click **OK**, and then click the **"Apply"** button to execute the changes.

### Installing Missing Drivers

DigitalOcean's infrastructure uses VirtIO drivers for optimal performance. If you see devices with missing drivers in the Device Manager (like the network adapter or disk controller), you'll need to install them.

1.  **Download the VirtIO drivers.** You can get the ISO file from this link:
    ```
    https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/virtio-win-0.1.96/virtio-win.iso
    ```
2.  **Mount the ISO.** Once downloaded, right-click the file and select **"Mount"**. This will create a new virtual DVD drive.
3.  **Update the driver.** Go to **Device Manager**, find the device with the missing driver (it will likely have a yellow exclamation mark), right-click it, and select **"Update driver"**.
4.  **Browse for the driver.** Choose **"Browse my computer for drivers"**.
5.  **Select the mounted drive.** Click the **"Browse..."** button, select the virtual DVD drive you mounted in step 2, and click **OK**.
6.  **Install.** Click **"Next"**, and Windows will automatically find and install the appropriate driver from the ISO. Repeat this process for any other devices with missing drivers.

***

## Important: Safety and Security 🛡️

Once your droplet is running, you must take immediate steps to secure it.

### 1. Change User and Password

The first thing you should do is change the default password. It's best to create a new user account with administrator privileges and a **strong, unique password**, and then disable or delete the default `root` user.

### 2. Secure Your RDP Connection

DigitalOcean's promotional credits (often $200 for 60 days) can cover a powerful droplet running 24/7. This makes it a prime target for automated **brute-force attacks** on the open Remote Desktop port. To protect against this, you should not expose your IP address publicly.

* **Solution:** Use a tool like **Tailscale** or **Cloudflare Zero Trust** to create a secure, private network tunnel to your droplet. This hides the RDP port from the public internet.
* **Recommendation:** **Tailscale** is generally much easier and faster to set up. You can find many simple guides on how to install and configure it for Windows.

### 3. Droplet Management

* **Snapshots:** The standard DigitalOcean snapshot feature may **not work correctly** with this custom installation method. Do not rely on it for backups.
* **Billing:** To avoid unexpected charges after your free credits are used, remember to **destroy the droplet** as soon as you are finished with it.

## Disclaimer

This guide is for educational purposes only. I am not responsible for any damage or data loss that may occur. Proceed at your own risk.
