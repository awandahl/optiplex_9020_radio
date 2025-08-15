# optiplex_9020_radio

## Software Installation

### Source locations:
```
/home/aw/.hamradio/sources/wsjtz
/home/aw/.hamradio/sources/wsjtx-imp
/home/aw/.hamradio/sources/flrig
/home/aw/.hamradio/sources/fldigi
/home/aw/.hamradio/sources/mshv
/home/aw/.hamradio/sources/jtdx
/home/aw/.hamradio/sources/hamlib-prefix
```

### Install locations:

```
/home/aw/.hamradio/install/wsjtz
/home/aw/.hamradio/install/wsjtx-imp
/home/aw/.hamradio/install/flrig
/home/aw/.hamradio/install/fldigi
/home/aw/.hamradio/install/mshv
/home/aw/.hamradio/install/jtdx
```

### Create hamlib-prefix:

```
$ cd ~/.hamradio/sources/hamlib-prefix
$ git clone https://github.com/Hamlib/Hamlib src
$ cd src
$ ./bootstrap
$ mkdir build
$ cd build
$ ../configure --prefix=/home/aw/.hamradio/sources/hamlib-prefix \
   --disable-shared --enable-static \
   --without-cxx-binding --disable-winradio \
   CFLAGS="-g -O2 -fdata-sections -ffunction-sections" \
   LDFLAGS="-Wl,--gc-sections"
$ make
$ make install-strip
```


### Install WSJT-Z:

```
$ mkdir /home/aw/.hamradio/install/wsjtz
$ mkdir /home/aw/.hamradio/sources/wsjtz
$ cd ~/.hamradio/sources/wsjtz
$ wget https://sourceforge.net/projects/wsjt-z/files/Source/wsjtz-2.7.0-rc7-1.48.zip
$ unzip wsjtz-2.7.0-rc7-1.48.zip
$ cd wsjtx/
$ mkdir -p build
$ cd build
$ cmake -D CMAKE_PREFIX_PATH=/home/aw/.hamradio/sources/hamlib-prefix \
    -DWSJT_SKIP_MANPAGES=ON -DWSJT_GENERATE_DOCS=OFF \
    -DCMAKE_CXX_FLAGS="-Wno-error=deprecated-declarations" \
    -D CMAKE_INSTALL_PREFIX=/home/aw/.hamradio/install/wsjtz ..
$ cmake --build .
$ cmake --build . --target install
```

### Install WSJT-X Improved:

```
$ mkdir -p /home/aw/.hamradio/sources/wsjtx-imp
$ mkdir -p /home/aw/.hamradio/install/wsjtx-imp
$ cd ~/.hamradio/sources/wsjtx-imp
$ wget https://sourceforge.net/projects/wsjt-x-improved/files/WSJT-X_v2.8.0/Source%20code/wsjtx-2.8.0_improved_widescreen_PLUS_250501.tgz/download
$ unzip download
$ cd wsjtx-2.8.0/
$ cd src
$ tar xvfz wsjtx.tgz
$ cd wsjtx
$ mkdir -p build
$ cd build
$ cmake -D CMAKE_PREFIX_PATH=/home/aw/.hamradio/sources/hamlib-prefix \
    -DWSJT_SKIP_MANPAGES=ON -DWSJT_GENERATE_DOCS=OFF \
    -DCMAKE_CXX_FLAGS="-Wno-error=deprecated-declarations" \
    -D CMAKE_INSTALL_PREFIX=/home/aw/.hamradio/install/wsjtx-imp ..
$ cmake --build .
$ cmake --build . --target install
```

### Install MSHV:

```
$ mkdir -p /home/aw/.hamradio/sources/mshv
$ mkdir -p /home/aw/.hamradio/install/mshv
$ cd ~/.hamradio/sources/mshv
$ wget https://sourceforge.net/projects/mshv/files/MSHV_2763_Full_Source_Code.zip
$ unzip MSHV_2763_Full_Source_Code.zip
$ cd MSHV_2763
$ qmake MSHV_x86_64.pro
$ make
$ cp -r bin/* /home/aw/.hamradio/install/mshv/ (copy everything in bin to the installation directory)
For making clean, use "make distclean"
```

### Install Fldigi:

```
$ mkdir -p /home/aw/.hamradio/sources/fldigi
$ mkdir -p /home/aw/.hamradio/install/fldigi
$ cd ~/.hamradio/sources/fldigi
$ wget https://www.w1hkj.org/alpha/fldigi/fldigi-4.2.07.12.tar.gz
$ tar xvfz fldigi-4.2.07.12.tar.gz
$ cd fldigi-4.2.07.12
$ ./configure --prefix=/home/aw/.hamradio/install/fldigi
$ make
$ make install
```

### Install Flrig:

```
$ mkdir -p /home/aw/.hamradio/sources/flrig
$ mkdir -p /home/aw/.hamradio/install/flrig
$ cd ~/.hamradio/sources/flrig
$ wget https://www.w1hkj.org/alpha/flrig/flrig-2.0.07.01.tar.gz
$ tar xvfz flrig-2.0.07.01.tar.gz
$ cd flrig-2.0.07.01
$ ./configure --prefix=/home/aw/.hamradio/install/flrig
$ make
$ make install
```

### Install JTDX:

```
$ mkdir -p /home/aw/.hamradio/sources/jtdx
$ mkdir -p /home/aw/.hamradio/install/jtdx
$ cd ~/.hamradio/sources/jtdx
$ git clone https://github.com/jtdx-project/jtdx
$ cd jtdx
$ mkdir -p build
$ cd build
$ cmake -D CMAKE_PREFIX_PATH=/home/aw/.hamradio/sources/hamlib-prefix \
   -D WSJT_SKIP_MANPAGES=ON \
   -D CMAKE_INSTALL_PREFIX=~/home/aw/.hamradio/install/jtdx ..
$ cmake --build .
$ cmake --build . --target install
```



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

