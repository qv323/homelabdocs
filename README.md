# Homelab Documentation

Welcome to my Homelab documentation! This repo tracks my journey in designing, building, and maintaining a home lab focused on clustering, virtualization, VLANs, disaster recovery, automation, and general IT best practices. 
My goal is to create a functional, educational environment for learning and experimentation—and to build habits that translate directly to enterprise IT.

---

## Table of Contents
- [Overview](#overview)
- [Cluster Architecture](#cluster-architecture)
- [Networking & VLANs](#networking--vlans)
- [Disaster Recovery](#disaster-recovery)
- [Backup Routines](#backup-routines)
- [Updates and patch Management]
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
