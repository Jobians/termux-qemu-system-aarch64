# Termux QEMU System ARM64

Run ARM64 Linux on Termux using QEMU. You have **two options**:

1. Install from ISO (full install – very slow)  
2. Boot immediately using a prebuilt image (fast setup, fresh system)  

---

## Requirements

```bash
pkg update && pkg upgrade
pkg install qemu-system-aarch64-headless qemu-utils
```

---

## Option 1: Install Debian from ISO (Very Slow)

> ⚠️ **Note:** Installing Debian from ISO is **very slow** on Termux and can take **several hours** to complete. Use this option only if you want a fresh installation.

### Step 1: Download the Debian ARM64 netinst ISO

```bash
wget https://cdimage.debian.org/debian-cd/13.1.0/arm64/iso-cd/debian-13.1.0-arm64-netinst.iso
```

### Step 2: Create a disk image for installation

```bash
qemu-img create -f qcow2 debian-arm64.img 8G
```

### Step 3: Boot the ISO and install Debian

```bash
qemu-system-aarch64 \
  -machine virt,accel=tcg \
  -cpu cortex-a53 \
  -m 2048 \
  -smp 2 \
  -drive file=debian-arm64.img,format=qcow2,if=virtio \
  -cdrom debian-13.1.0-arm64-netinst.iso \
  -boot d \
  -bios $PREFIX/share/qemu/edk2-aarch64-code.fd \
  -nographic \
  -serial mon:stdio \
  -device virtio-net-device,netdev=net0 \
  -netdev user,id=net0
```

Follow the normal Debian installer prompts. When the installation is complete, shut down QEMU.

### Step 4: Boot your installed Debian system

```bash
qemu-system-aarch64 \
  -machine virt,accel=tcg \
  -cpu cortex-a53 \
  -m 2048 \
  -smp 2 \
  -drive file=debian-arm64.img,format=qcow2,if=virtio \
  -bios $PREFIX/share/qemu/edk2-aarch64-code.fd \
  -nographic \
  -serial mon:stdio \
  -device virtio-net-device,netdev=net0 \
  -netdev user,id=net0 \
  -boot c
```

---

## Option 2: Boot Directly Using Prebuilt Image (Fast Setup, Fresh System)

> ⚡ **Note:** The prebuilt image is also a **fresh, clean Debian system**. Nothing extra is installed, so it’s ready for you to start from scratch.

### Step 1: Download the prebuilt image

```bash
wget https://github.com/Jobians/termux-qemu-system-aarch64/releases/download/debian-13.1.0-arm64/debian-13.1.0-arm64.img.xz
```

### Step 2: Decompress the image

```bash
xz -d debian-13.1.0-arm64.img.xz
```

### Step 3: Boot the prebuilt image

```bash
qemu-system-aarch64 \
  -machine virt,accel=tcg \
  -cpu cortex-a53 \
  -m 2048 \
  -smp 2 \
  -drive file=debian-13.1.0-arm64.img,format=qcow2,if=virtio \
  -bios $PREFIX/share/qemu/edk2-aarch64-code.fd \
  -nographic \
  -serial mon:stdio \
  -device virtio-net-device,netdev=net0 \
  -netdev user,id=net0 \
  -boot c
```

### Step 4: Login

```
Username: root
Password: root
```

---

## Notes

- **Option 1 (ISO installation)** is very slow and may take several hours.  
- **Option 2 (prebuilt image)** is fast, fresh, and clean. Nothing is pre-installed, so you can start immediately.  
- Use Option 1 only if you specifically want to go through the full Debian installation process.
