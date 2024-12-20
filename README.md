
# Step-by-Step NAS Setup Guide  

This guide walks through the process of setting up a NAS (Network-Attached Storage) server using Raspberry Pi, SSDs, and HDDs.  

---

## Prerequisites  
- Raspberry Pi with Raspberry Pi OS installed.  
- At least one 256 GB and one 512 GB SSD.  
- Optional: Additional HDD for storage.  
- Power supply, monitor (optional for initial setup), and a stable network connection.  

---

## Step 1: Update and Upgrade the System  
Run the following commands to ensure your Raspberry Pi OS is up to date:  

```bash
sudo apt update && sudo apt upgrade -y
```

---

## Step 2: Install Required Tools  
Install tools for managing storage and networking:  

```bash
sudo apt install samba samba-common-bin ntfs-3g
```

---

## Step 3: Configure Storage Drives  
1. Plug in your SSDs and HDDs.  
2. Identify the drives:  

```bash
lsblk
```

3. Create a mount point for each drive:  

```bash
sudo mkdir -p /mnt/ssd256 /mnt/ssd512 /mnt/hdd
```

4. Mount the drives:  

```bash
sudo mount /dev/sdX1 /mnt/ssd256  
sudo mount /dev/sdY1 /mnt/ssd512  
sudo mount /dev/sdZ1 /mnt/hdd
```

Replace `sdX1`, `sdY1`, and `sdZ1` with the appropriate drive identifiers.  

5. Make the mounts permanent by editing the `fstab` file:  

```bash
sudo nano /etc/fstab
```

Add the following lines:  

```plaintext
/dev/sdX1 /mnt/ssd256 ext4 defaults 0 2  
/dev/sdY1 /mnt/ssd512 ext4 defaults 0 2  
/dev/sdZ1 /mnt/hdd ext4 defaults 0 2  
```

Save and exit.

---

## Step 4: Set Up Samba for File Sharing  
1. Edit the Samba configuration file:  

```bash
sudo nano /etc/samba/smb.conf
```

2. Add shares for each drive:  

```plaintext
[SSD256]
path = /mnt/ssd256
read only = no
browsable = yes

[SSD512]
path = /mnt/ssd512
read only = no
browsable = yes

[HDD]
path = /mnt/hdd
read only = no
browsable = yes
```

3. Restart Samba:  

```bash
sudo systemctl restart smbd
```

---

## Step 5: Configure Remote Access  
1. Enable SSH if not already enabled:  

```bash
sudo raspi-config
```

Navigate to **Interface Options** > **SSH** and enable it.  

2. Note your Raspberry Pi’s IP address:  

```bash
hostname -I
```

---

## Step 6: Test Your NAS  
- Access your NAS using the IP address in a file explorer (e.g., `\\192.168.x.x\SSD256`).  
- Test file upload and download speeds.  

---

## Step 7: Secure Your NAS  
1. Set up a strong Samba user password:  

```bash
sudo smbpasswd -a yourusername
```

2. Configure a firewall:  

```bash
sudo apt install ufw  
sudo ufw allow ssh  
sudo ufw allow samba  
sudo ufw enable
```

3. Enable automatic updates:  

```bash
sudo apt install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

---

## Step 8: Monitor and Maintain  
- Install monitoring tools like `htop` or `glances`:  

```bash
sudo apt install htop glances
```

- Check drive health with `smartmontools`:  

```bash
sudo apt install smartmontools
sudo smartctl -a /dev/sdX
```

---

## Conclusion  
Your NAS is now set up and ready to use! 🎉 For additional features or troubleshooting, feel free to open an issue in this repository.  
