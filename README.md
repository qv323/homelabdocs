# Homelab Documentation

Welcome to my Homelab documentation! This repo tracks my journey in designing, building, and maintaining a home lab focused on clustering, virtualization, VLANs, disaster recovery, automation, and general IT best practices. 
My goal is to create a functional, educational environment for learning and experimentation—and to build habits that translate directly to enterprise IT.

---

# Table of Contents
- [Overview](#overview)
- [Documentation & Reference Files](#documentation-and-reference-files)
- [Hardware & Inventory](#hardware-and-Inventory)
- [Cluster Architecture](#cluster-architecture)
- [Networking & VLANs](#networking--vlans)

<details>
<summary><strong>Tools</strong></summary>

&nbsp;&nbsp;<details>
<summary><strong>Network Management</strong></summary>

&nbsp;&nbsp;&nbsp;&nbsp;- [Tera Term](#tera-term)  
&nbsp;&nbsp;&nbsp;&nbsp;- [TFTPd64](#tftpd64)  
&nbsp;&nbsp;&nbsp;&nbsp;- [Winbox / WebFig](#winbox--webfig)  
&nbsp;&nbsp;&nbsp;&nbsp;- [The Dude](#the-dude)  
&nbsp;&nbsp;&nbsp;&nbsp;- [USB Drives](#usb-drives)  
&nbsp;&nbsp;&nbsp;&nbsp;- [WinSCP](#winscp)

</details>

&nbsp;&nbsp;<details>
<summary><strong>Virtualization & Automation</strong></summary>

&nbsp;&nbsp;&nbsp;&nbsp;- [Proxmox VE Web UI](#proxmox-ve-web-ui)  
&nbsp;&nbsp;&nbsp;&nbsp;- [Proxmox Shell/SSH](#proxmox-shellssh)  
&nbsp;&nbsp;&nbsp;&nbsp;- [Ansible](#ansible)

</details>

&nbsp;&nbsp;<details>
<summary><strong>Backup & Config Management</strong></summary>

&nbsp;&nbsp;&nbsp;&nbsp;- [External SSD/HDD](#external-ssdhdd)

</details>

&nbsp;&nbsp;<details>
<summary><strong>Monitoring & Misc</strong></summary>

&nbsp;&nbsp;&nbsp;&nbsp;- [MobaXterm / PuTTY](#mobaxterm--putty)  
&nbsp;&nbsp;&nbsp;&nbsp;- [HDMI Splitter and Monitor](#hdmi-splitter-and-monitor)  
&nbsp;&nbsp;&nbsp;&nbsp;- [Lenovo Legion Laptop](#lenovo-legion-laptop)

</details>

</details>

- [Disaster Recovery](#disaster-recovery)
- [Update & Patch Management](#update--patch-management)
- [Backup Routines](#backups-and-data-retention)
- [Automation](#automation)
- [Proxmox & VMware Notes](#proxmox--vmware-notes)
- [Lessons Learned & Best Practices](#lessons-learned--best-practices)
- [Future Plans](#future-plan)

---

## Overview

This homelab is built to mirror real-world IT infrastructure on a small scale. I use clustering, virtualization, VLAN segmentation, automated backups, and scripting to test ideas and simulate production scenarios. 
My documentation here is meant to be a resource for myself and anyone interested in homelab or IT operations.

---

## Documentation and Reference Files

Most detailed configuration files, best practices checklists, troubleshooting guides, and device inventory spreadsheets are maintained **offline** in the `/homelab docs` directory on my NAS and/or primary management workstation. These files are not included in this public repository to protect sensitive data and streamline maintenance.

When a section in this README refers to a specific guide, checklist, or reference, look for the document by name in the `/homelab docs` folder on the NAS and or management laptop.

---

## Hardware and Inventory

- Full device inventory: Homelab Device Tracker.xlsx (NAS)
- mATX Proxmox node configuration: mATX Proxmox Node Reference.pdf (NAS)
- Bill of materials for 10Gb networking (NICs, cables, adapters): Homelab 10Gb DAC BOM.xlsx (NAS)
  
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

[⬆️ Back to Top](#table-of-contents)

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

[⬆️ Back to Top](#table-of-contents)

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

[⬆️ Back to Top](#table-of-contents)

---

## Summary Notes & Unique Features

- All nodes use Intel X520 dual-port 10Gb SFP+ NICs for fast, reliable networking
- PCIe riser cables and 10Gb adapters are externally powered and properly grounded for safety and stability
- Custom 3D-printed cases and rack parts maximize airflow and cable management
- Cooling is a mix of premium Noctua fans and custom airflow solutions for quiet, cool operation
- Serial console access for Brocade switch via handmade cable and Tera Term
- Nearly all hardware (except mATX build and NAS) is housed in a single, ultra-compact 14U mini rack for simplicity and space savings

--- 

[⬆️ Back to Top](#table-of-contents)

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
 
  **Reference Docs**
- Physical wiring and quick reference diagrams: cluster config back script check.png (NAS)
- VLAN port assignments: Needs updated Homelab VLAN Port Assignment Table.xlsx (NAS)

- **Reference Diagrams:**  
  _(upload or link to a network diagram image here)_

--- 

[⬆️ Back to Top](#table-of-contents)

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

[⬆️ Back to Top](#table-of-contents)

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

[⬆️ Back to Top](#table-of-contents)

---

## Backup & Config Management

### **External SSD/HDD**
- **Purpose:** VM backups, ISO/image storage, and cold backups.
- **How I Use It:**  
  Used for scheduled exports of VM images, keeping redundant copies of key configs, and offline storage of golden images.
  
--- 

[⬆️ Back to Top](#table-of-contents)

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

[⬆️ Back to Top](#table-of-contents)

---

## [Next one here]

--- 

[⬆️ Back to Top](#table-of-contents)

---


## Disaster Recovery

- **Snapshots:**  
  - Regular VM snapshots before major changes or updates
  - Automated rollback procedures tested in the lab

- **Failover:**  
  - Proxmox cluster provides node failover (VMs move to another node if hardware fails)
  - Practice restoring VMs from backup and snapshots

- **Reference Docs:**  
  - All procedures for failover, restore, and snapshotting are written and tested
  - 
--- 

[⬆️ Back to Top](#table-of-contents)

---

# Update & Patch Management

This section documents how to keep core homelab systems updated, patched, and secure. Always **back up your config** before performing any major updates or patches.

--- 

[⬆️ Back to Top](#table-of-contents)

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

--- 

[⬆️ Back to Top](#table-of-contents)

---
 
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

After reboot, confirm with: **insert cmd here**

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
- Don’t interrupt the switch during update or reboot. **Make sure the weather is Nice/Boring**

--- 

[⬆️ Back to Top](#table-of-contents)

---

## Backups and Data Retention

This section details all currently implemented backup routines, retention schedules, storage locations, validation, and disaster recovery procedures in the homelab.

**Note:** All descriptions here reflect only what is presently in use. Planned or future enhancements are tracked separately in the “Future Plans” section.

- **Backups**
  - NFS HA setup steps: Needs updated NFS HA Setup.docx (NAS)
  
---

## 1. Proxmox Cluster Backups

### Proxmox Backup Server (PBS)
- PBS runs as a VM on the NAS (Dell 7050, 4x8TB RAID10). The PBS datastore is on the main RAID volume.
- Nightly encrypted backups of all Proxmox VMs and containers are scheduled from every node (Dell 3080s, mATX, nuc1, etc.) to PBS over the 10Gb network.
- **Retention Policy:** 7 daily, 4 weekly, 12 monthly backups per VM/CT, pruned automatically by PBS.
- **Cold Storage:** After major changes and quarterly, the PBS datastore is manually exported or synced to an external SSD/HDD, which is kept offline except during backup operations.
- **Validation:** A test restore (clone or mount) is performed at least monthly on a Proxmox node to ensure backup integrity. PBS and Proxmox backup logs are reviewed weekly.
- **Disaster Recovery:** In case of major failure, a new Proxmox node is deployed, PBS datastore is reattached (from NAS or the most recent cold backup), and restores are performed as needed. PBS credentials and keys are stored offline.

---

## 2. NAS & Storage Backups

- NAS configuration (TrueNAS, ZFS pool, and share settings) is exported after any major change and on a monthly basis.
- Critical share data (documentation, scripts, playbooks, personal files) is mirrored to a secondary volume on the NAS and copied to an external HDD quarterly for cold storage.
- VM template exports and important ISO images are stored on the NAS and included in regular cold storage copies.

---

## 3. Network Device & Service Configs

- **MikroTik CRS310:** Config is exported after major changes and monthly using `/export file=backup` and/or `/system backup save`, with copies kept on NAS and USB.
- **Brocade ICX7250:** Running/startup configs are exported via TFTP or manually and saved to the NAS and cold USB storage.
- **Pi-hole, OPNsense, Unbound:** Configs are exported via web UI after significant changes and monthly. All configs are stored on the NAS and included in cold storage rotation.
- **Datto and other appliances:** Configurations are exported as available and archived with the same schedule.

---

## 4. Documentation & Automation

- All current infrastructure documentation, diagrams, and scripts are stored on the NAS and included in the regular cold backup rotation.
- Automation tools (e.g., Ansible playbooks, scripts) are also stored on the NAS and external backup devices.
- Restore steps and procedures are included in the `/Docs_Exports` folder on the NAS and in cold storage media.

---

## 5. Backup Schedules & Validation

| Task/Item                      | Frequency         | Storage Location           | Validation/Test                    |
|--------------------------------|-------------------|----------------------------|------------------------------------|
| PBS (VM/CT backups)            | Nightly           | NAS RAID10, Ext. SSD/HDD   | Monthly restore/test               |
| PBS (pruning)                  | Weekly auto       | NAS RAID10                 | Review logs weekly                 |
| NAS config export              | Monthly/after chg | NAS, USB/SSD               | Mount/restore as test              |
| NAS share data cold copy       | Quarterly         | Ext. HDD                   | Manual file check                  |
| MikroTik/Brocade config export | After chg/monthly | NAS, USB                   | Import/restore as test             |
| Pi-hole/OPNsense/Unbound conf  | After chg/monthly | NAS, USB                   | Import/restore as test             |
| Docs/scripts/automation        | Ongoing           | NAS, USB/SSD               | Manual verify quarterly            |

---

## 6. Data Retention Policy

- **VM/CT Images:** 7 daily, 4 weekly, 12 monthly (PBS pruning).
- **Configs/Docs:** All versions maintained on NAS and USB; cold copies kept for at least 1 year.

---

## 7. Disaster Recovery & Restore Plan

- Restore procedures for Proxmox/PBS, NAS, and all configs are documented in `/Docs_Exports` and included in cold backup sets.
- PBS and NAS credentials/keys are kept offline for security.
- In the event of a full-site loss, a new Proxmox node can be built, PBS datastore attached, and VM/CTs restored. Network device and service configs can be restored to new hardware or VMs from NAS/USB backups.

 - **Reference Docs**

- NFS HA setup steps: Needs updated NFS HA Setup.docx (NAS)
- Quick recovery and restore tips: Homelab Quick Fixes.pdf (NAS)


---

## 8. Best Practices

- Backups are validated monthly with test restores or mounts.
- All logs are checked when failures occur.
- Documentation is updated after any major infrastructure or network change. And the ever-present 'that sucked to fix, i should write it down for my future self'
- Multiple backup layers are maintained: PBS for rapid recovery, NAS/USB for long-term/cold storage.
- **No critical data exists in only one place.**


## Automation

- **Scripting:**  
  - Use of bash, PowerShell, and Python for common tasks (bulk VM provisioning, config exports)
  - Plans to implement Proxmox API and PowerCLI for automation

- **Monitoring:**  
  - Basic monitoring scripts for resource usage, network health, and alerts

- **Reference Docs:**  
  - All scripts and automations are documented for easy reuse
    - Quick recovery and restore tips: Homelab Quick Fixes.pdf (NAS)

--- 

[⬆️ Back to Top](#table-of-contents)

---

## Proxmox & VMware Notes

- **Proxmox:**
  - Experience with clustering, VM migration, template creation, ZFS pools
  - VLAN/tagged bridge networking and storage integration

- **VMware (enterprise parallel):**
  - Principles learned in homelab translate to VDI/VMware setups in fintech:
    - Clustering = high availability
    - Centralized management = easier compliance, audits, patching
    - Fast failover, disaster recovery, and rollback

--- 

[⬆️ Back to Top](#table-of-contents)

---

## Lessons Learned & Best Practices

- I update documentation after every significant change, especially after troubleshooting or complex fixes.
- My full best practices checklist is maintained as a living document:  
  📄 [Homelab Best Practices Checklist.docx] (NAS)
- For quick solutions and troubleshooting notes, see:  
  📄 [Homelab Quick Fixes.pdf] (NAS)

> “That sucked to fix, so I wrote it down for my future self.”

- Document everything—assume you’ll need to fix it at 2am!
- VLANs and segmentation improve security and reliability
- Automated backups (and regular test restores) prevent disasters
- Practice disaster recovery before it’s needed for real
- Scripting and templates save hours in the long run
- Clear, simple documentation helps period

## Lessons Learned & Best Practices

- I update documentation after every significant change, especially after troubleshooting or complex fixes.
- My full best practices checklist is maintained as a living document:  
  📄 [Homelab Best Practices Checklist.docx] (NAS)
- For quick solutions and troubleshooting notes, see:  
  📄 [Homelab Quick Fixes.pdf] (NAS)

> “That sucked to fix, so I wrote it down for my future self.”


--- 

[⬆️ Back to Top](#table-of-contents)

---

## Future Plans

- Add more automation for VM provisioning and monitoring
- Experiment with advanced VLAN routing and firewalling
- Explore hybrid cloud scenarios (self-hosted + cloud)
- Version control via GitHub or cloud is not currently implemented for infrastructure documentation and automation
- Personal/family data:** Retained indefinitely on NAS with offsite cold backup
- Implement Proxmox API and PowerCLI for automation
- Logs that capture failures sent via some app to notify me
  
--- 

[⬆️ Back to Top](#table-of-contents)

---

## Why I Document

Creating and maintaining good documentation is part of what separates a hobbyist from a true IT pro. It ensures continuity, makes onboarding and troubleshooting easier, and helps others (and my future self!) learn from my experiences.

---

# homelabdocs
