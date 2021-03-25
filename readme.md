Skip to main content
OpenEdge Site Logo
OpenEdge Doc 0.8.0
Architecture
User Guide
Admin Guide
API Doc
Search
Admin Guide
Admin Guide Introduction
Component Administration
Procedures
Installation
Upgrade
Device Lifecycle
Disaster Recovery
VM Processes
VM Shutdown / Reboot
VM Move
Orchestrator VM Loss
Control Plane Stop/Start
NMS Alert Codes
VM Move
Move a VM from one hypervisor server to another.

NOTE
This process assumes you are using KVM hypervisors

Preparation#
Make sure you have sufficient resources on the destination hypervisor
Follow the component-specific processes to prepare for shutdown
Move#
Set up environment variables#
Setup the environment variables used in the remaining commands.

VM_IMG_NAME: name of the VM image as shown by "sudo virsh list"
SRC_USER: username for scp on the source hypervisor
SRC_HOST: IP or hostname of the source hypervisor
@Source Hypervisor#
export VM_IMG_NAME=<image name from above>
@Destination Hypervisor#
export VM_IMG_NAME=sjt-vwan1
export SRC_USER=dduck
export SRC_HOST=10.100.2.70
Export#
Shutdown the VM, make its image readable, and export the VM definition

@Source Hypervisor#
sudo virsh shutdown ${VM_IMG_NAME}
sudo virsh dumpxml ${VM_IMG_NAME} > /tmp/${VM_IMG_NAME}.xml
sudo chmod a+r /var/lib/libvirt/images/${VM_IMG_NAME}.qcow2
Copy#
Copy the files and set their permissions

@Destination Hypervisor#
sudo scp ${SRC_USER}@${SRC_HOST}:/var/lib/libvirt/images/${VM_IMG_NAME}.qcow2 /var/lib/libvirt/images/
sudo scp ${SRC_USER}@${SRC_HOST}:/tmp/${VM_IMG_NAME}.xml /tmp
sudo chown qemu:qemu /var/lib/libvirt/images/${VM_IMG_NAME}.qcow2
sudo chmod 600 /var/lib/libvirt/images/${VM_IMG_NAME}.qcow2
Import#
Import the VM definition and set it to autostart

@Destination Hypervisor#
sudo virsh define /tmp/${VM_IMG_NAME}.xml
sudo virsh autostart ${VM_IMG_NAME}
Start new VM#
Start the VM on the destination hypervisor

@Destination Hypervisor#
sudo virsh start ${VM_IMG_NAME}
Clean up#
Undefine the old VM, remove image and temp files

@Source Hypervisor#
sudo rm -f /tmp/${VM_IMG_NAME}.xml
sudo virsh undefine ${VM_IMG_NAME}
sudo rm -f /var/lib/libvirt/images/${VM_IMG_NAME}.qcow2
@Destination Hypervisor#
sudo rm -f /tmp/${VM_IMG_NAME}.xml
Previous
« VM Shutdown / Reboot
Next
Orchestrator VM Loss »
Preparation
Move
Set up environment variables
Export
Copy
Import
Start new VM
Clean up
Copyright © 2021 Verizon
