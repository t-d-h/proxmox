# create debian cloud image in proxmox:


qm create 9111 --name debian11-cloud --memory 2048 --net0 virtio,bridge=vmbr0

qm importdisk 9111 debian-11-generic-amd64-20220121-894.qcow2 local -format qcow2

qm set 9111 --scsihw virtio-scsi-pci --virtio0 /var/lib/vz/images/9111/vm-9111-disk-0.qcow2
qm set 9111 --ide2 local:cloudinit --boot c --bootdisk virtio0 --serial0 socket --vga serial0

qm resize 9111 virtio0 +30G

qm set 9111 --ipconfig0 ip=192.168.1.53/24,gw=192.168.1.1

qm set 9111 --sshkey ~/.ssh/id_rsa.pub
qm set 9111 --cipassword toor

qm cloudinit dump 9111 user

# create tempalte and a linked clone
qm template 9111
qm clone 9111 191 --name debian11-1
qm start 191

rm -v debian-10.0.2-20190721-openstack-amd64.qcow2
