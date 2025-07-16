# Homelab Documentation

Welcome to my Homelab documentation! This repo tracks my journey in designing, building, and maintaining a home lab focused on clustering, virtualization, VLANs, disaster recovery, automation, and general IT best practices. 
My goal is to create a functional, educational environment for learning and experimentation—and to build habits that translate directly to enterprise IT.

---

## Table of Contents
- [Overview](#overview)
- [Cluster Architecture](#cluster-architecture)
- [Networking & VLANs](#networking--vlans)
- [Tools](#tools)
    - [Network Management](#network-management)
        - [Tera Term](#tera-term)
        - [TFTPd64](#tftpd64)
        - [Winbox / WebFig](#winbox--webfig)
        - [The Dude](#the-dude)
        - [USB Drives](#usb-drives)
        - [WinSCP](#winscp)
    - [Virtualization & Automation](#virtualization--automation)
        - [Proxmox VE Web UI](#proxmox-ve-web-ui)
        - [Proxmox Shell/SSH](#proxmox-shellssh)
        - [Ansible](#ansible)
    - [Backup & Config Management](#backup--config-management)
        - [External SSD/HDD](#external-ssdhdd)
    - [Monitoring & Misc](#monitoring--misc)
        - [MobaXterm / PuTTY](#mobaxterm--putty)
        - [HDMI Splitter and Monitor](#hdmi-splitter-and-monitor)
        - [Lenovo Legion Laptop](#lenovo-legion-laptop)
- [Disaster Recovery](#disaster-recovery)
- [Update & Patch Management](#update--patch-management)
- [Backup Routines](#backup-routines)
- [Automation](#automation)
- [Proxmox & VMware Notes](#proxmox--vmware-notes)
- [Lessons Learned & Best Practices](#lessons-learned--best-practices)
- [Future Plans](#future-plans)

---

## Overview

This homelab is built to mirror real-world IT infrastructure on a small scale. I use clustering, virtualization, VLAN segmentation, automated backups, and scripting to test ideas and simulate production scenarios. 
My documentation here is meant to be a resource for myself and anyone interested in homelab or IT operations.

---

## Cluster Architecture

- **Hardware:**  
  - 2 Dell 3080 Micro PCs (Proxmox nodes)
  - 1 Dell 7050 SFF (used as a NAS)
     -Intel/LSI SAS controllers for storage clustering
  - MikroTik CRS310-8G+2S+IN switc

- **Virtualization Platform:**  
  - Proxmox VE as the main hypervisor (multiple nodes in a cluster)
  - Proxmox cluster configured for high availability and redundancy

- **Core Concepts:**  
  - Shared storage for VM migration and failover
  - Management interfaces separated from production via VLANs
  - Automated VM deployment templates
 
    # Hardware Details

This section covers the physical components in my homelab, including compute, storage, networking, rack layout, and cooling. My design choices focus on modularity, reliability, and creative problem-solving on a budget—using techniques from enterprise IT (clustering, redundancy, VLANs), adapted to a home lab environment.

---

## Compute Nodes

### Dell OptiPlex 3080 Micro PCs (x2)
- **CPU:** Intel Core i3-10500 (6C/12T, 3.1 GHz)
- **RAM:** 16GB DDR4 each
- **OS Storage:** 512GB NVMe SSD each
- **NIC:** Intel X520 dual-port 10Gb SFP+ NIC (in each node)
- **PCIe Riser:** M.2 to PCIe adapters with PCIe riser cables, externally powered by a dedicated ATX power supply (grounded to each Dell case for safety)
- **Form Factor:** Compact SFF, both housed in the custom 10" mini rack
- **Notes:**  
  - HDMI from each node runs to a splitter for secondary monitor access
  - Each node cooled by airflow from the 140mm Noctua rack intake and additional 40mm Noctua fans pushing air across the 10Gb NICs

---

### Dell OptiPlex 7050 SFF (NAS)
- **CPU:** Intel i7-7700
- **RAM:** 16GB DDR4
- **OS Storage:** 256GB NVMe SSD
- **Data Storage:** 4 x 8TB 3.5" HDDs (RAID 10, LSI SAS9211-8i controller)
- **NIC:** Intel X520 dual-port 10Gb SFP+ NIC
- **Case:** Custom 3D-printed enclosure for improved cooling and cable management
- **Purpose:** Main NAS, running TrueNAS for ZFS, NFS, SMB, and config backups

---

### Custom mATX Build (Proxmox Node)
- **Motherboard:** ASUS TUF GAMING B560M-PLUS (mATX)
- **CPU:** Intel Core i7-11700KF (8C/16T, 11th Gen)
- **RAM:** 64GB G.SKILL Trident Z RGB DDR4-3600 CL18-22-22-42 (2x32GB)
- **Storage:** 128gb NVMe SSD
- **GPU:** GTX 1650
- **NIC:** Intel X520 dual-port 10Gb SFP+ NIC
- **PSU:** ATX
- **Case:** 3D-printed mATX case (custom, optimized for cooling, horizontal PCIe slot mounting, and easy disassembly)
- **Location:** Separate from main rack
- **Notes:** Designed for maintenance and airflow, supports high-performance and test workloads

---

### Intel NUC (Utility Node)
- **Model:** NUC8i7BEH
- **CPU:** Intel Core i7-8650U
- **RAM:** 16GB DDR4
- **OS Storage:** 128GB SSD
- **Purpose:** Runs Pi-hole, OPNsense firewall, and other utility workloads (future Proxmox cluster node)
- **Cooling:** 40mm Noctua intake fan pushes air directly over external 10Gb NICs
- **Location:** Housed in 10" mini rack

---

### HP ProLiant G7 Servers (Retired/Phasing Out)
- **CPU:**  Dual Xeon E5-2670
- **RAM:**  128GB ECC DDR3
- **Storage:** Mixed SAS HDDs
- **Purpose:** Used for legacy and experimental workloads; replaced by newer, more efficient systems

---

## Networking

### Main Switch: MikroTik CRS310-8G+2S+IN
- **Role:** Core switch, 10Gb SFP+ trunking, VLAN segmentation, primary LAN backbone
- **Notes:**  
  - Houses all active nodes 1GB
  - Connected to matx and NAS 10Gb NICs with DAC cables

### Brocade ICX7250 (10Gb Switch)
- **Role:** Dedicated for 10Gb connectivity/expansion
- **Serial Console:** Custom-made serial cable for CLI access via Tera Term
- **Mounting:** Vertically mounted on the exterior of the server rack
- **Cooling:**  
  - 120mm exhaust fan
  - 80mm intake blower
  - OEM fans replaced with two 40mm Noctua fans for silent operation

---

## Rack & Cooling

### 10" Mini Rack (14U high, custom 3D print)
- **Contents:** Both Dell 3080s, NUC, MikroTik switch, power supply, utility hardware
- **Fans:**  
  - 140mm Noctua intake fan (horizontal 1u)
  - Three 40mm exhaust fans (rear, across 13U)
  - 40mm Noctua intake for NIC cooling
- **HDMI:** All nodes routed to a single splitter for shared secondary monitor

### Peripherals & Power
- **External ATX power supply:** Powers all PCIe riser cards for 10Gb NICs; grounded to each Dell case
- **Monitor setup:** HDMI from every node into a splitter for KVM-style multi-system access

---

## Summary Notes & Unique Features

- All nodes use Intel X520 dual-port 10Gb SFP+ NICs for fast, reliable networking
- PCIe riser cables and 10Gb adapters are externally powered and properly grounded for safety and stability
- Custom 3D-printed cases and rack parts maximize airflow and cable management
- Cooling is a mix of premium Noctua fans and custom airflow solutions for quiet, cool operation
- Serial console access for Brocade switch via handmade cable and Tera Term
- Nearly all hardware (except mATX build and NAS) is housed in a single, ultra-compact 14U mini rack for simplicity and space savings

---


## Networking & VLANs

- **VLAN Design:**  
  - Segmented networks for LAN, backup, storage, management, test/dev, and IoT
  - MikroTik CRS310 switch used for VLAN trunking and port-based segmentation
  - OPNsense firewall for routing, DNS, and access control
  - Unbound DNS and Pi-hole for local DNS and ad blocking

- **Proxmox Network Setup:**  
  - Bonded interfaces for 10Gb connectivity
  - VLAN-aware Linux bridges mapped to physical switch ports
  - Consistent naming (mgmt0, prod0, etc.)

- **Reference Diagram:**  
  _(You can upload or link to a network diagram image here)_

---

# Tools

This section lists the core software, utilities, and hardware tools used to manage, automate, monitor, or recover your homelab environment. Each tool includes a description and **how it’s used in this specific setup**.

---

## Network Management

### **Tera Term**
- **Purpose:** Serial console access for network devices.
- **How I Use It:**  
  Used for connecting to the Brocade ICX7250 via USB-to-serial cable on my Lenovo Legion. I use Tera Term for firmware upgrades, bootloader access, and config recovery.

### **TFTPd64**
- **Purpose:** Lightweight TFTP server for config and firmware transfer.
- **How I Use It:**  
  Runs on my Lenovo Legion whenever I need to push or pull firmware/config files to/from the Brocade switch or any device that supports TFTP.

### **Winbox / WebFig**
- **Purpose:** MikroTik configuration via GUI (Winbox app or browser).
- **How I Use It:**  
  Used to configure, back up, or update my MikroTik CRS310. Also used to check for RouterOS updates and upload new packages.

### **The Dude**
- **Purpose:** MikroTik’s network monitoring and mapping tool.
- **How I Use It:**  
  Installed on my Lenovo Legion. I use The Dude to monitor uptime, get SNMP graphs, track device status, and visualize my entire homelab network topology in real time.

### **USB Drives**
- **Purpose:** Offline firmware upgrades and config backup.
- **How I Use It:**  
  FAT32-formatted USB drives are used to update Brocade firmware or back up configs when I’m not on the same subnet, or if TFTP isn’t practical.

### **WinSCP**
- **Purpose:** SCP/SFTP file transfer.
- **How I Use It:**  
  Used to quickly move config files, firmware, or scripts to MikroTik and Proxmox systems directly from my management laptop.

---

## Virtualization & Automation

### **Proxmox VE Web UI**
- **Purpose:** Web-based virtualization management platform.
- **How I Use It:**  
  Main tool for managing all VMs and containers, snapshots, storage, and backups. I access the Web UI from my Lenovo or any browser on the LAN.

### **Proxmox Shell/SSH**
- **Purpose:** Command-line troubleshooting and automation.
- **How I Use It:**  
  Used for in-depth troubleshooting, template builds, and running shell scripts (including automated backups and updates) on all Proxmox hosts.

### **Ansible**
- **Purpose:** Automation and configuration management.
- **How I Use It:**  
  Runs on my Lenovo Legion or a dedicated VM. I use Ansible playbooks to automate software installs, push updates, and maintain consistent config across all Proxmox nodes, and for select network gear.

---

## Backup & Config Management

### **External SSD/HDD**
- **Purpose:** VM backups, ISO/image storage, and cold backups.
- **How I Use It:**  
  Used for scheduled exports of VM images, keeping redundant copies of key configs, and offline storage of golden images.

---

## Monitoring & Misc

### **MobaXterm / PuTTY**
- **Purpose:** SSH and serial console multipurpose terminals.
- **How I Use It:**  
  Sometimes used for SSH access to Proxmox, OPNsense, or for serial troubleshooting (as an alternative to Tera Term).

### **HDMI Splitter and Monitor**
- **Purpose:** KVM-style access for all rack devices.
- **How I Use It:**  
  All node HDMI outputs go through a splitter to a single secondary monitor, making physical troubleshooting and install/recovery much easier.

### **Lenovo Legion Laptop**
- **Purpose:** Primary management workstation.
- **How I Use It:**  
  Used for everything—Tera Term console, Winbox, The Dude, Ansible playbooks, Proxmox access, TFTP server, file transfer, and more.

---

## [Add your own!]
If you use Docker, WireGuard, Pi-hole, OPNsense, a label printer, etc., add a section with a short “How I Use It” note.

---


## Disaster Recovery

- **Snapshots:**  
  - Regular VM snapshots before major changes or updates
  - Automated rollback procedures tested in the lab

- **Failover:**  
  - Proxmox cluster provides node failover (VMs move to another node if hardware fails)
  - Practice restoring VMs from backup and snapshots

- **Documentation:**  
  - All procedures for failover, restore, and snapshotting are written and tested

---

# Update & Patch Management

This section documents how to keep core homelab systems updated, patched, and secure. Always **back up your config** before performing any major updates or patches.

---

## MikroTik CRS310-8G+2S+IN

### Checking for Updates via Web Interface (Recommended)

1. **Login to the MikroTik GUI** (Winbox, WebFig, or browser).
2. Go to **System → Packages**.
3. Click **Check for Updates**.
4. Select the preferred channel (**stable** is recommended).
5. Click **Download & Install**.
6. The device will download the update, install, and reboot automatically.

### Manual Package Upload & Install

1. **Download the latest package** for your device from [mikrotik.com/download](https://mikrotik.com/download).
2. Login to the MikroTik GUI.
3. Go to **Files** in the menu.
4. **Upload** the `.npk` package (drag-and-drop or use the upload button).
5. Go to **System → Packages**.
6. **Enable** the new package if it’s not already.
7. **Reboot** the device.
8. The new update or feature will be active after reboot.

### CLI Method 
- Upload the package via SCP, then SSH in and run:

 
## Brocade ICX7250: Update & Patch Management

Keeping the Brocade ICX7250 firmware (FastIron OS) up to date improves security, stability, and adds new features. **Always back up your running config before upgrading.**

---

### 1. Check Current Firmware Version

  - Connect to switch via serial console (Tera Term), SSH, or Telnet, then run:

---

### 2. Download Latest Firmware

- Go to [Brocade Downloads (now Ruckus)](https://support.ruckuswireless.com/software?filter=104#firmwares)
- Download both the **FastIron OS image (`.bin`)** and the matching **boot or U-Boot image (`.img`)** for your model (ICX7250).

---

### 3. Transfer Firmware to Switch

You can use **TFTP, FTP, or USB stick** (formatted FAT32) to copy the new images to your switch.

#### TFTP Example (common in labs)
Assuming your TFTP server IP is `192.168.1.100` and you place the new `.bin` in its root directory:

---

Do the same for the boot image if updating:
(Replace filenames as needed.)

---

### 4. USB-only Update Workflow

**Best for airgapped switches or if you don't have a TFTP server handy.**

1. Format a USB stick as FAT32.
2. Place the OS image (`.bin`) and/or boot image (`.img`) at the root of the USB drive.
3. Plug the USB into the Brocade’s USB port.
4. Connect to the console.
5. Run:


*(Replace filenames as needed.)*
6. Check images:
7. Set boot to primary (if needed):
8. Reboot:

---

### 5. Activate New Firmware

If not already set, choose which partition to boot from:
Then **reboot**:

---

### 6. Verify Upgrade

After reboot, confirm with:

---

### 7. Quick Restore from Serial Console (XMODEM Recovery)

**If the Brocade won't boot, or image is corrupted, use XMODEM over serial:**

1. Connect serial console to the switch (use Tera Term on your Lenovo laptop).
2. Reboot or power cycle the switch.
3. Interrupt the bootloader with `b` or by pressing a key if prompted.
4. At the bootloader prompt, enter:
5. In Tera Term, go to **File > Transfer > XMODEM > Send**, and select the OS image `.bin` file.
6. Wait for transfer (can be 20+ minutes).
7. When done, set the switch to boot from the recovered image:
8. Reboot:
9. When back up, verify with:

---

### Cheat Sheet (CLI Reference)

| Task                       | CLI Command Example                                            |
|----------------------------|---------------------------------------------------------------|
| Show current version       | `show version`                                                |
| Show current images        | `show flash`                                                  |
| Copy OS image (TFTP)       | `copy tftp flash 192.168.1.100 FI7250.bin primary`            |
| Copy bootrom (TFTP)        | `copy tftp bootrom 192.168.1.100 mnz10114.bin`                |
| Copy OS image (USB)        | `copy usb FI7250.bin flash primary`                           |
| Activate primary image     | `boot system flash primary`                                   |
| Reboot                     | `reload`                                                      |
| Backup config (TFTP)       | `copy running-config tftp 192.168.1.100 backup.cfg`           |
| XMODEM recovery            | `copy xmodem flash primary`                                   |

---

**Tips:**
- Always backup config before upgrades:
 ```
 copy running-config tftp 192.168.1.100 mybackup.cfg
 ```
- Only use images for the ICX7250—using the wrong model may brick your device.
- If upgrading both bootrom and OS, do bootrom first, then OS.
- Don’t interrupt the switch during update or reboot.

---


## Backup Routines

- **VM Backups:**  
  - Daily/weekly backups to NAS
  - NFS and local ZFS storage pools

- **Configuration Backups:**  
  - Regular backup of Proxmox configs, OPNsense configs, Pi-hole configs
  - Backups stored both on NAS and cold USB storage

- **Test Restores:**  
  - Periodic test restores to validate backup integrity

 ---

## Automation

- **Scripting:**  
  - Use of bash, PowerShell, and Python for common tasks (bulk VM provisioning, config exports)
  - Plans to implement Proxmox API and PowerCLI for automation

- **Monitoring:**  
  - Basic monitoring scripts for resource usage, network health, and alerts

- **Documentation:**  
  - All scripts and automations are documented for easy reuse

---

## Proxmox & VMware Notes

- **Proxmox:**
  - Experience with clustering, VM migration, template creation, ZFS pools
  - VLAN/tagged bridge networking and storage integration

- **VMware (enterprise parallel):**
  - Principles learned in homelab translate to VDI/VMware setups in banks:
    - Clustering = high availability
    - Centralized management = easier compliance, audits, patching
    - Fast failover, disaster recovery, and rollback

---

## Lessons Learned & Best Practices

- Document everything—assume you’ll need to fix it at 2am!
- VLANs and segmentation improve security and reliability
- Automated backups (and regular test restores) prevent disasters
- Practice disaster recovery before it’s needed for real
- Scripting and templates save hours in the long run
- Clear, simple documentation helps period

---

## Future Plans

- Add more automation for VM provisioning and monitoring
- Experiment with advanced VLAN routing and firewalling
- Explore hybrid cloud scenarios (self-hosted + cloud)
- Continue improving documentation and sharing lessons

---

## Why I Document

Creating and maintaining good documentation is part of what separates a hobbyist from a true IT pro. It ensures continuity, makes onboarding and troubleshooting easier, and helps others (and my future self!) learn from my experiences.

---

# homelabdocs
