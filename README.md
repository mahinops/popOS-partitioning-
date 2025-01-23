# Renaming and Permanently Mounting Linux Partitions with Custom Labels - Pop!_OS

Here’s the full process to rename your partitions, mount them to meaningful directories, and make the configuration permanent across reboots:

---

### Step 1: Identify the Partitions
1. Run the `df -h` command to find the partitions and their current mount points. For example:
   ```bash
   df -h
   ```
   You might see something like:
   ```
   /dev/nvme0n1p5  183G   32G  142G  19% /media/mahin/7f184e3d-214d-4a7b-b00c-2ae16aa1b82a
   /dev/nvme0n1p6  146G  4.2G  135G   3% /media/mahin/7f1454ba-0ed2-4c5e-b5b0-0b38c716b4bf
   ```

---

### Step 2: Rename the Partitions
2. **Unmount the partitions:**
   ```bash
   sudo umount /media/mahin/7f184e3d-214d-4a7b-b00c-2ae16aa1b82a
   sudo umount /media/mahin/7f1454ba-0ed2-4c5e-b5b0-0b38c716b4bf
   ```

3. **Rename the partitions using `e2label`:**
   ```bash
   sudo e2label /dev/nvme0n1p5 personal
   sudo e2label /dev/nvme0n1p6 development
   ```

---

### Step 3: Create Custom Mount Points
4. Create directories for the mount points:
   ```bash
   sudo mkdir -p /media/mahin/personal
   sudo mkdir -p /media/mahin/development
   ```

---

### Step 4: Mount the Partitions Temporarily
5. Mount the renamed partitions to their new directories:
   ```bash
   sudo mount /dev/nvme0n1p5 /media/mahin/personal
   sudo mount /dev/nvme0n1p6 /media/mahin/development
   ```

6. Verify the changes with `df -h`:
   ```bash
   df -h
   ```
   You should see:
   ```
   /dev/nvme0n1p5  183G   32G  142G  19% /media/mahin/personal
   /dev/nvme0n1p6  146G  4.2G  135G   3% /media/mahin/development
   ```

---

### Step 5: Make the Changes Permanent
7. **Get the UUIDs of the partitions:**
   Use the following command to list the UUIDs:
   ```bash
   sudo blkid
   ```
   Look for the entries for `/dev/nvme0n1p5` and `/dev/nvme0n1p6`. You’ll see something like:
   ```
   /dev/nvme0n1p5: UUID="abc12345-6789-0123-4567-890abcdef012" TYPE="ext4" PARTLABEL="personal"
   /dev/nvme0n1p6: UUID="def67890-1234-5678-9012-345abcdef678" TYPE="ext4" PARTLABEL="development"
   ```

8. **Edit the `/etc/fstab` file:**
   Open the file with a text editor:
   ```bash
   sudo nano /etc/fstab
   ```

   Add the following lines at the bottom of the file (replace `UUID=` with the actual UUIDs from the `blkid` output):
   ```
   UUID=abc12345-6789-0123-4567-890abcdef012 /media/mahin/personal ext4 defaults 0 2
   UUID=def67890-1234-5678-9012-345abcdef678 /media/mahin/development ext4 defaults 0 2
   ```

9. **Save and exit the editor:**
   - Press `Ctrl+O` to save.
   - Press `Ctrl+X` to exit.

10. **Test the `fstab` configuration:**
    Run:
    ```bash
    sudo mount -a
    ```
    This command mounts all filesystems listed in `/etc/fstab`. If no errors appear, the configuration is correct.

---

### Step 6: Optional Shortcuts for Easy Access
11. **Create symbolic links in your home directory:**
    ```bash
    ln -s /media/mahin/personal ~/personal
    ln -s /media/mahin/development ~/development
    ```

    Now, you can use:
    ```bash
    cd ~/personal
    cd ~/development
    ```

12. **Alternatively, add aliases in your shell configuration file:**
    Open `~/.zshrc` (for Zsh) or `~/.bashrc` (for Bash):
    ```bash
    nano ~/.zshrc
    ```
    Add:
    ```bash
    alias personal="cd /media/mahin/personal"
    alias development="cd /media/mahin/development"
    ```

    Save and reload:
    ```bash
    source ~/.zshrc
    ```

---

### Summary of Commands
```bash
# Unmount
sudo umount /media/mahin/7f184e3d-214d-4a7b-b00c-2ae16aa1b82a
sudo umount /media/mahin/7f1454ba-0ed2-4c5e-b5b0-0b38c716b4bf

# Rename
sudo e2label /dev/nvme0n1p5 personal
sudo e2label /dev/nvme0n1p6 development

# Create mount points
sudo mkdir -p /media/mahin/personal
sudo mkdir -p /media/mahin/development

# Temporary mount
sudo mount /dev/nvme0n1p5 /media/mahin/personal
sudo mount /dev/nvme0n1p6 /media/mahin/development

# Get UUIDs
sudo blkid

# Edit /etc/fstab
sudo nano /etc/fstab
# Add lines:
# UUID=<UUID1> /media/mahin/personal ext4 defaults 0 2
# UUID=<UUID2> /media/mahin/development ext4 defaults 0 2

# Test fstab
sudo mount -a

# Create shortcuts
ln -s /media/mahin/personal ~/personal
ln -s /media/mahin/development ~/development
```

Save this as a reference for future use! 😊 Let me know if you need anything else!
