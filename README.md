# optiplex_9020_radio



## Rsync Backup

### 1. Example Backup Command

```bash
rsync -a --delete ~/Documents ~/Desktop ~/.config ~/.bashrc ~/.ssh ~/.hamradio ~/.local /mnt/storage/backups/userdata
```

- `-a` = archive (preserves permissions, timestamps, recursive)
- `--delete` = removes files on backup that were deleted from source
- Adjust source directories and backup destination as needed.

***

### 2. Put Your Command in a Script

Create `backup.sh`:

```bash
#!/bin/bash
rsync -a --delete ~/Documents ~/Desktop ~/.config ~/.bashrc ~/.ssh ~/.hamradio ~/.local /mnt/storage/backups/userdata
```

Make script executable:

```bash
chmod +x ~/backup.sh
```


***

### 3. Automate with Cron (Run Daily)

Edit crontab:

```bash
crontab -e
```

Add:

```
0 2 * * * /home/yourusername/backup.sh
```

(Runs every day at 2 AM. Change time as needed.)

***

### 4. Restore Files

If you ever need to restore:

```bash
rsync -a /mnt/storage/backups/userdata/* ~/
```

(Adjust paths as needed.)

***

### 5. Tips

- Make sure destination (`/mnt/storage/backups/userdata`) exists.
- For system files (e.g., `/etc`), run as root and include those directories.
- Exclude large cache folders if space is a concern (`--exclude '.cache/'`).
- For extra portability, you can archive (`tar czvf`) your configs as well.

***

**Summary:**
Put your rsync backup command in a script, automate with cron, verify backups regularly, and use rsync to restore when needed. This covers daily file/config backups to your 1TB disk.







## ReaR Bootable ISO Backup

### 1. Install ReaR

```bash
sudo apt update
sudo apt install rear
```


### 2. Configure ReaR

Edit `/etc/rear/local.conf`:

```bash
sudo nano /etc/rear/local.conf
```

Add:

```
OUTPUT=ISO
OUTPUT_URL=file:///mnt/storage/rear-backups/
BACKUP=NETFS
BACKUP_URL=file:///mnt/storage/rear-backups/
```

(Adjust `/mnt/storage/rear-backups/` if needed)

### 3. Create the ISO Backup

```bash
sudo rear -v mkbackup
```

This generates a bootable ISO image and backup archive in your backup folder.

### 4. Retain the ISO

- Copy it to external disks, cloud, or dedicated USB if desired.
- Optional: Burn to USB stick using `dd`:

```bash
sudo dd if=/mnt/storage/rear-backups/rear-<hostname>.iso of=/dev/sdX bs=4M status=progress
sudo sync
```

*(Replace `/dev/sdX` with your USB device!)*


### 5. Restore Steps (when needed)

1. Boot target machine from the USB stick (or DVD) with your ReaR ISO.
2. Log in as `root` in the rescue environment.
3. Restore with:

```bash
rear recover
```

4. Follow prompts to complete the system recovery.

***

**Tip:**

- Test the ISO boot and recovery process on a spare system if possible to ensure everything works as expected.
- You can regularly repeat the backup process to keep up-to-date recovery media.

