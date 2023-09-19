# format-azure-disk.sh

```
export SCRIPT_NAME="format-azure-disk.sh"; touch $SCRIPT_NAME && chmod +x $SCRIPT_NAME && nano $SCRIPT_NAME
```

```sh
set -e

DISK="$1"

# format
sudo parted "/dev/${DISK}" --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs -f "/dev/${DISK}1"
sudo partprobe "/dev/${DISK}1"
```

# attach-azure-disk.sh

```
export SCRIPT_NAME="attach-azure-disk.sh"; touch $SCRIPT_NAME && chmod +x $SCRIPT_NAME && nano $SCRIPT_NAME
```

```sh
set -e

DISK="$1"
FS="$2"
DATA_DIR="$3"
DISK_UUID=$(sudo blkid | grep "/dev/${DISK}1" | cut -d " " -f 2 | cut -d "\"" -f 2)

# mount
sudo mkdir "${DATA_DIR}"
sudo mount "/dev/${DISK}1" "${DATA_DIR}"

# attach on system start up
FSTAB_LINE="UUID=${DISK_UUID}   ${DATA_DIR}   ${FS}   defaults,nofail   1   2"
sudo bash -c "echo \"$FSTAB_LINE\" >> /etc/fstab"

# check status
cat /etc/fstab
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

# detach-azure-disk.sh
```
export SCRIPT_NAME="detach-azure-disk.sh"; touch $SCRIPT_NAME && chmod +x $SCRIPT_NAME && nano $SCRIPT_NAME
```
```sh
set -e

DISK="$1"
DATA_DIR="$2"
DISK_UUID=$(sudo blkid | grep "/dev/${DISK}1" | cut -d " " -f 2 | cut -d "\"" -f 2)

# unmount
sudo umount -l "/dev/${DISK}1"
sudo rm -r "${DATA_DIR}"

# detach from system start up
sudo sed --in-place "/${DISK_UUID}/d" /etc/fstab

# check status
cat /etc/fstab
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

# Usage example
1. Get list of disks:

```
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

2. Output will be like this:

```
deep@deep:~$ lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
sda     1:0:0:0      128G 
sdb     0:0:0:0       30G 
├─sdb1              29.9G /
├─sdb14                4M 
└─sdb15              106M /boot/efi

```

3. Choose disk, for example `sda`

4. Attach

```sh
sudo ./format-azure-disk.sh sda
sudo ./attach-azure-disk.sh sda xfs /data
```

5. Deatach if needed

```sh
sudo ./detach-azure-disk.sh sda /data
```