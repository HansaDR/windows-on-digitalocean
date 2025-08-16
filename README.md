# windows-on-digitalocean
A guide on how to install a custom Windows image on a DigitalOcean droplet.

-----

## Overview

This guide provides a step-by-step process for installing Windows on a DigitalOcean droplet. This process involves using a `.gz` file containing the Windows image and writing it to the droplet's disk. This guide is for educational purposes only. I am not responsible for any data loss or other issues that may arise from following these instructions.

-----

## Prerequisites

  * Access to a DigitalOcean account and the ability to create droplets. If you don't have an account, you can sign up using my referral link to get a credit:
    [](https://www.digitalocean.com/?refcode=9ab72d603ab4&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=badge)

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
    wget -O- 'https://www.dropbox.com/scl/fi/9sll3ctje09d1ahqdank8/windows11.gz?rlkey=c7vwlaiq5staj21en1g82gqlx&st=f8k6qpv8&dl=1' | gunzip | sudo dd of=/dev/vda
    ```

3.  **Wait for it to Finish.** Press **Enter** to run the command. This process will take a significant amount of time, and there might not be any progress indicator. Please be patient and wait for the command to complete.

### 3\. Login Credentials

You will need the following credentials to log in to Windows for the first time. You can (and should) change these later for security.

  * **User:** `root`
  * **Password:** `123@@@`

### 4\. Final Steps

1.  **Power Down.** Once the download command is finished, close the terminal window and **power off** the droplet using the toggle button in the dashboard.
2.  **Boot from Hard Drive.** Go back to the **Recovery** tab and switch the boot option back to **"Boot from Hard Drive"**.
3.  **Turn On the Droplet.** Power the droplet back on.
4.  **Access Windows.** Navigate to the **Access** tab on the left-hand menu. Click the **"Launch Recovery Console"** button to open the Windows interface. **Do not use the regular Droplet Console.** Log in with the credentials provided above to begin your setup.

-----

## Disclaimer

This guide is for educational purposes only. I am not responsible for any damage or data loss that may occur. Proceed at your own risk.
