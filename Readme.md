# zenon Service Engine on Linux - Getting Started

This guide guides you through the initial installation and configuration of the zenon Service Engine on Linux.

## Requirements

- Ubuntu 22.04 or Rasperry Pi OS (bullseye)  
    - amd64 or ARM64 only
    - Raspberry PI model 4B or newer
    - If you have the need for a different Linux distribution, please contact pm@copadata.com or sales@copadata.com

- Existing installation of the zenon IIoT Services version 12.0 or later  
    - It does not matter if the installation is done using Windows or Docker.  
    - Have a look at the [zenon Online Help](https://onlinehelp.copadata.com/) and the *Getting Started Guide for zenon IIoT Services*

- A machine with the zenon License Manager installed and an available netork license
    - see [zenon Online Help](https://onlinehelp.copadata.com/) in the licensing section for more information on this topic


# Installation steps

Login to the Linux machine by means of a physical keyboard, mouse and display, or just connect to it using a remote shell using ssh.  
The following steps will guide you step-by-step through the installation procedures.  
Ensure to configure the correct repository based on your Linux installation.

## Add repository on Ubuntu
Follow theses steps to add COPA-DATA's APT repository on Ubuntu

1. TODO !!! (use correct official URL and commands) !!! Download and install the repository's gpg key  
`wget -O- http://<server>/zenon/12/release-candidate/unsecure-build-pipeline.gpg.key | gpg --dearmor | sudo tee /usr/share/keyrings/copadata-archive-keyring.gpg > /dev/null`
2. TODO !!! (use correct official key and url) !!! Add the repository to the list of remote repositories  
`echo "deb [arch=amd64 signed-by=/usr/share/keyrings/copadata-archive-keyring.gpg] http://<server>/zenon/12/release-candidate/ jammy main" | sudo tee /etc/apt/sources.list.d/copa-data.list`
3. Update the local package index files for the repository  
   `sudo apt update`


## Add repository on Raspberry PI OS
Follow theses steps to add COPA-DATA's APT repository on Raspberry PI OS

1. TODO !!! (use correct official URL and commands) !!! Download and install the repository's gpg key  
`wget -O- http://<server>/zenon/12/release-candidate/unsecure-build-pipeline.gpg.key | gpg --dearmor | sudo tee /usr/share/keyrings/copadata-archive-keyring.gpg > /dev/null`
2. TODO !!! (use correct official key and url) !!! Add the repository to the list of remote repositories  
`echo "deb [arch=arm64 signed-by=/usr/share/keyrings/copadata-archive-keyring.gpg] http://<server>/zenon/12/release-candidate/ bullseye main" | sudo tee /etc/apt/sources.list.d/copa-data.list`
3. Update the local package index files for the repository  
   `sudo apt update`


## Install required packages
The following steps are valid for Ubuntu and Raspberry PI OS

1. Install the `locales-all` package, which is required by the Service Engine  
    `sudo apt install locales-all`
2. Install the Service Engine package  
    `sudo apt install service-engine`
3. Install the `iiot-cli-12-0` package  
    `sudo apt install iiot-cli-12`

## Configure a network license

- You need to have a working license server and a valid license that is shared for network access. This machine must be accessible from the Linux machine in order to retrieve a license.

- Open the file `/etc/copa-data/License.ini` using `nano` or another text editor and insert the following configuration.  
    `sudo nano /etc/copa-data/License.ini`
 
    ``` ini
    [Runtime]
    SERIAL0 = <license serial number>
    SERIAL0_LOCATION = <machine name of the license server>

    [LogicRuntime]
    SERIAL0 = <license serial number>
    SERIAL0_LOCATION = <machine name of the license server>
    ```


## Download and install the CA certificate of the IIoT Services

**Explanation:**  
The following steps are only important if you use the default HTTPs certificate or any other self-signed HTTPs certificate with the IIoT Services. As the IIoT Services HTTPs certificate is not trusted, you need to add the respective CA certificate to the list of trusted certificates.  

**Verification:**  
If your IIoT Services installation already uses a trusted certificate, you can ignore this chapter. You can verify if the HTTPs certificate is being trusted with the following command `curl 'https://<url-of-iiot-services>:<port-of-iiot-services>'`. If the command works without any certificate error, the Linux machine already trusts the certificate of the IIoT Services.

If the certificate is not trusted, follow those steps:
1. Open the Service Configuration Studio of the IIoT Services and download the CA certificate to your machine.
2. Transfer the CA certificate to the Linux machine using an appropriate mechanism like `scp`, `ftp` or network file shares. [SCP Manpages](https://manpages.ubuntu.com/manpages/trusty/man1/scp.1.html)
3. Copy the CA certificate file to `/usr/local/share/ca-certificates`. The actual name of the file does not matter.
4. Update the system's trusted CA certificatse using `sudo update-ca-certificates`.  This will update the CA certificates and add the IIoT Service's certificate to the trusted list.
5. Use the command above to verify that the certificate is being trusted.


## Connect the machine with the Device Management service
The Device Management service is a central service, that allows the transfer of zenon projects to connected devices.
Detailed information about the provided functionalities can be found in the [zenon Online Help](https://onlinehelp.copadata.com/).

To connect the machine with the Device Management, follow these steps:
1. Setup the device agent and register the machine with the Device Management  
    `iiot-cli setup-agent -u https://<url-of-iiot-services>:<port-of-iiot-services>`
2. A web browser will open and you need to authenticate with your IIoT Services administrator account to successfully register the device.
3. Verify the connection status of the device in the Device Management's UI by opening the Service Configuration Studio.  
    The device needs to be in state *Online*  
    You can also verify the state of the device management service using `sudo systemctl status device-agent.service`

**Hint:** If your Linux device does not offer a user interface and web browser, or you are connected using SSH, use the command `iiot-cli setup-agent -u https://<url-of-iiot-services>:<port-of-iiot-services> --use-device-code` for the registration. This will allow you to authenticate using a web browser on a dedicated device. 


## Transfer a zenon project using the Device Management

1. Open the zenon Engineering Studio with your desired project.  
    In order to have a compatible project for the Service Engine on Linux, please refer to the [zenon Online Help](https://onlinehelp.copadata.com/) and make sure you are not using any incompatible functionalities.
2. Ensure to have the desired project loaded and have it configured for the IIoT Services within *Network > IIoT Services* inside the project properties.
3. Compile the project
4. Upload the project using the Device Management Wizard. (*Tools > Provide Project for Device Management*)  
    1. Select a desired version number for the project upload  
    2. Enter the URL of the IIoT Services  
    3. Upload the project using the button `Publish`  
        A web browser will open and asks you to authenticate yourself via the Identity Service.
5. Open the Device Management UI in the web browser and verify that the project was uploaded successfully.
6. Switch to the deployment tasks overview and create a deployment task for your Linux Service Engine  
    1. Untick the checkbox *Use device-specific Project ID* when you create the deployment task.  
    2. Select the desired deployment type *instant* or *schedule*
    3. Create the deployment task.
7. Open the logs to see the progress of the deployment task
8. Once the deployment task has finished successful, verfiy the state of the Service Engine using `sudo systemctl status serviceEngine.service`.  
    It should be in state running and executing the configured zenon project.

**Congratulations, your Linux machine is now running the Service Engine with your zenon project.**


# Troubleshoot and How-to

In case of troubles when performing the installation and configuration steps, make sure to have a look at the troubleshooting section below.

## Use Diagnosis Viewer for viewing logs
The Service Engine on Linux also includes the Log Server for viewing and retrieving log messages. To use it, open the Diagnosis Viewer on your Windows machine and connect the Diagnosis Viewer to the Log Server of the Linux machine (*File > Connect to ...*).
By default it is using the port 50780.

## Check the status of the Service Engine and the Device Agent
The status of the Service Engine and the Device Agent can be checked with the commands `sudo systemctl status serviceEngine.service` and `sudo systemctl status device-agent.service`.
It is also possible to restart them if necessary with `sudo systemctl restart <service-name>`

## Read the logs
The logs from the Service Engine or Device Agent can either be retrieved via the zenon Log Server or also via `journalctl -u <service-name>`.  
Use `journalctl -u serviceEngine.service` and `journalctl -u device-agent.service` to the retrieve the respective logs.  
**You may need to scroll to reach the correct date and time.**

## Check the license status of the Service Engine
On startup the Service Engine checks for an available license. If no license server and/or license serial number is configured, the Service Engine won't start up properly. In this case, this can be checked via `sudo systemctl status serviceEngine.service`