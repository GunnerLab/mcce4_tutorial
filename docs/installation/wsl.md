---
title: Windows Users - WSL
nav_order: 5
parent: Installation
permalink: /docs/installation/wsl/
layout: default
---
# WSL

While Linux and macOS come with a built-in Linux-like environment, Windows users need to install the Windows Subsystem for Linux (WSL) to access a Linux terminal. You can find the installation guide at [Microsoft Learn - Install WSL](https://learn.microsoft.com/en-us/windows/wsl/install).

Here is a sample procedure to install WSL with an Ubuntu Linux distribution:

1. Open PowerShell or Command Prompt as Administrator: Right-click the Start Menu and select "Windows PowerShell (Admin)".
2. Run the command: ```wsl --install```.
3. Once the installation completes, restart your computer.
4. After restarting, search for "Ubuntu" in the Windows Start Menu to launch the WSL Ubuntu app.
5. On the first launch, WSL will take some time to initialize. You will then be prompted to set up a username and password for the first user.
