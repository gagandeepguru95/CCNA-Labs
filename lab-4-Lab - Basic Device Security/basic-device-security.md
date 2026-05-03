# IOS CLI Basics Lab

## Overview

This lab covers the first hands-on experience with the Cisco IOS command line interface. The tasks include setting hostnames, configuring passwords, encrypting them, and saving the configuration. It is a foundational lab focused on getting comfortable with navigating the CLI and understanding how passwords work on Cisco devices.

## Topology

A small network with a router, a switch, and a few PCs. The main configurations in this lab are done on the router, but the same steps apply to the switch and it is recommended to practice on both.

## Key Concepts

**CLI Modes**

There are three main modes used in this lab. User exec mode is the starting point and has limited access. Privileged exec mode, entered with the enable command, gives access to show commands and more. Global configuration mode, entered with configure terminal, is where most changes are made. You can use shortcuts like en for enable and conf t for configure terminal.

**Enable Password vs Enable Secret**

Two different commands can protect privileged exec mode. The enable password command sets a basic password that can be encrypted with service password-encryption, but that encryption is weak and can be cracked. The enable secret command uses MD5 encryption, which is much stronger. If both are configured at the same time, only the enable secret works. The enable password is ignored.

**Password Encryption**

By default, passwords in the running configuration appear in plain text. Running service password-encryption applies type 7 encryption to existing and future passwords, which at least prevents someone from reading it at a glance. The enable secret always uses type 5 encryption regardless of whether service password-encryption is enabled.

**Saving the Configuration**

Any changes made are stored in the running configuration, which is lost if the device is powered off without saving. To make changes permanent, you save to the startup configuration using write, write memory, or copy running-config startup-config. All three do the same thing.

## What I Learned

This lab made the different CLI modes feel more natural. Knowing when you are in user exec, privileged exec, or global config mode matters because certain commands only work in specific modes. The difference between enable password and enable secret was also clearer after seeing that only the secret works when both are configured. It is a small detail but easy to get confused on.

## Notes

- The do command lets you run privileged exec commands like show running-config without leaving global configuration mode, which saves time.
- Type 7 encryption from service password-encryption is better than nothing but should not be relied on for real security.
- Always verify changes by checking the running config before saving, so you know exactly what will be stored.
