# Windows Sandbox Troubleshooting

## Objective

Launch and test Windows Sandbox while following a training tutorial.

## Initial Setup

My first attempt was performed on a Windows virtual machine running in Proxmox.

I opened:

```text
Control Panel → Programs and Features → Turn Windows features on or off
```

and enabled Windows Sandbox and the required virtualization components
```text
Hyper-V
Windows Hypervisor Platform
Windows Sandbox
```

After rebooting, the virtual machine became extremely slow.

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

---

## Troubleshooting

To identify the cause, I reviewed Windows Sandbox configuration settings and policies.

During the investigation, I found the following policy:

```text
Computer Configuration
 └─ Administrative Templates
     └─ Windows Components
         └─ Windows Sandbox
             └─ Allow vGPU sharing for Windows Sandbox
```

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
