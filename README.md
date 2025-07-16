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
