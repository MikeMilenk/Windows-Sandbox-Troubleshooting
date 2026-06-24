# Windows Sandbox Troubleshooting

## Objective

Launch and test Windows Sandbox while following a training tutorial.

## Initial Setup

My first attempt was performed on a Windows virtual machine running in [Proxmox](https://github.com/MikeMilenk/Installing-Proxmox-VE.git).

I opened:

```text
Control Panel → Programs and Features → Turn Windows features on or off
```
If you can't find Programs and Features option, just change the "_View by:_" from _Category_ to _Small Icons_
![Control Panel](https://github.com/MikeMilenk/Windows-Sandbox-Troubleshooting/blob/4bae257cef9c2a32470908bc50419df08ee100ad/Images/Control%20Panel.png)
![Change Windows Features](https://github.com/MikeMilenk/Windows-Sandbox-Troubleshooting/blob/4bae257cef9c2a32470908bc50419df08ee100ad/Images/Change%20Windows%20Features.png)

and enabled Windows Sandbox and the required virtualization components
```text
Hyper-V
Windows Hypervisor Platform
Windows Sandbox
```
![Sandbox Features](https://github.com/MikeMilenk/Windows-Sandbox-Troubleshooting/blob/4bae257cef9c2a32470908bc50419df08ee100ad/Images/Sandbox%20Features.png)



After rebooting, the virtual machine became extremely slow.
![Low Performance Chart](https://github.com/MikeMilenk/Windows-Sandbox-Troubleshooting/blob/4255c66848abbc74a47fe9934c715866166daa2b/Images/Low%20Performance.png)

## Investigation

The issue was not caused by Windows Sandbox itself.

The Windows VM was hosted on a small home lab server with 16 GB of RAM shared across multiple virtual machines. Due to resource limitations, I could not allocate more than 6 GB of RAM to the VM. After enabling the Hyper-V components required by Windows Sandbox, performance degraded significantly.

To eliminate resource constraints from the troubleshooting process, testing was moved to a physical Windows machine.

---

## Second Attempt

An additional test was performed on a Windows 10 running through BootCamp tool on my iMac Pro. Due to platform limitations and the planned migration to Windows 11, testing continued on another Windows physical machine.

Windows Sandbox was initially unavailable because the system was running Windows 11 Home Edition. The system was upgraded to Windows 11 Pro, after which Sandbox became available for installation.

The installation completed successfully. I enabled Windows Sandbox and the required virtualization components on that new computer, but Windows Sandbox failed to launch and displayed the following error:

```text
The connection to the Windows Sandbox environment was lost. Do you want to reconnect?
```
![Sandbox Connection Issue](https://github.com/MikeMilenk/Windows-Sandbox-Troubleshooting/blob/4bae257cef9c2a32470908bc50419df08ee100ad/Images/Sandbox%20Connection%20Issue.png)

---

## Troubleshooting

To identify the cause, I reviewed Windows Sandbox configuration settings and local group policies.

During the investigation, I found the following policy:

```text
gpedit.msc

Computer Configuration
 └─ Administrative Templates
     └─ Windows Components
         └─ Windows Sandbox
             └─ Allow vGPU sharing for Windows Sandbox
```
![gpedit](https://github.com/MikeMilenk/Windows-Sandbox-Troubleshooting/blob/0f23f9ed68db37c691bf781b70440867c8650f37/Images/gpedit.png)
![Path to Windows Sandbox](https://github.com/MikeMilenk/Windows-Sandbox-Troubleshooting/blob/0f23f9ed68db37c691bf781b70440867c8650f37/Images/Path%20to%20Windows%20Sandbox.png)
![vGPU Sharing](https://github.com/MikeMilenk/Windows-Sandbox-Troubleshooting/blob/0f23f9ed68db37c691bf781b70440867c8650f37/Images/vGPU%20Sharing.png)
![vGPU Sharing Disabled](https://github.com/MikeMilenk/Windows-Sandbox-Troubleshooting/blob/0f23f9ed68db37c691bf781b70440867c8650f37/Images/vGPU%20Sharing%20Disabled.png)


---

## Resolution

The following policy was changed:

```text
Allow vGPU sharing for Windows Sandbox = Disabled
```

After applying the change, Windows Sandbox launched successfully.

---

## Root Cause

The root cause was the vGPU sharing configuration used by Windows Sandbox.

Disabling vGPU sharing allowed Windows Sandbox to start normally.

## Result

Windows Sandbox was successfully deployed and tested.
![Sanbdox itself](https://github.com/MikeMilenk/Windows-Sandbox-Troubleshooting/blob/0f23f9ed68db37c691bf781b70440867c8650f37/Images/Sanbdox%20itself.png)
