# Custom Rockchip-Based Cyberdeck

Welcome to my repository detailing the journey of building my very first **DIY Cyberdeck**. This project is an ongoing exploration into custom portable computing, hardware integration, and Linux configuration. Since this is my first experience building a device like this, it is a work in progress, and I would love to hear your feedback, suggestions, or ideas in the issues or comments!

---

## 🚀 Inspiration & Goals

The main catalyst for this project was **Pavel Zhovner’s "Flipper One"**. When the initial concepts and platform specifications for his project were published, I wondered: *Why not try building something similar, utilizing a capable single-board platform?*

**Project Goals:**
*   **No Advanced Soldering Required:** As I didn't have strong soldering skills when starting, the hardware architecture is intentionally modular, using off-the-shelf components easily sourced from marketplaces like AliExpress.
*   **Form Factor:** A highly compact, mini-laptop-style portable terminal.
*   **Utility:** A functional Linux environment for development, network analysis, and hardware experimentation.

---

## 🛠️ Hardware Architecture & Bill of Materials (BOM)

Initially, I planned to build this device around the **Radxa Rock 4D**. However, due to component shortages affecting RAM availability, I pivoted to a less common but highly capable alternative: the **DshanPi-A1**. 

### Component Breakdown
![[Pasted image 20260625174707.png]]

| Component            | Description                                                 | Why It Was Chosen / Notes                                                                                                | Image Placeholder                    |
| :------------------- | :---------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------- | :----------------------------------- |
| **SBC (Main Board)** | **DshanPi-A1** (Rockchip-based)                             | Budget-friendly, features dual RJ45 Ethernet ports, and comes with a built-in 32GB eMMC module.                          | ![[Pasted image 20260625183103.png]] |
| **Power Management** | **Raspberry Pi 4 Li-polymer Battery HAT** (SW6106 Solution) | Features built-in protection circuits. Testing confirmed it works flawlessly with the DshanPi-A1.                        | ![[Pasted image 20260625183114.png]] |
| **Battery**          | **3.7V 5000mAh 955565 Li-Po Battery**                       | Equipped with a JST PH 2.0mm 2-pin plug, fitting the power management HAT cleanly. CHECK THE POLARITY BEFORE ASSEMBLY!!! |                                      |
| **Display**          | **5" HDMI LCD Resistive Touch Screen**                      | 800x480 resolution TFT monitor designed for Raspberry Pi interfaces.                                                     | ![[Pasted image 20260625183123.png]] |
| **Display Cable**    | **FPV Flat HDMI Cable (Type-A Male to Male)**               | Flexible ribbon cable (available in 10cm/20cm/50cm) ideal for tight chassis routing.                                     | ![[Pasted image 20260625183134.png]] |
| **Cooling**          | **Active Cooling Fan & Heatsink**                           | Designed for Raspberry Pi, though the connector cable requires extensions for perfect routing on this board.             |                                      |

---

## 📐 Enclosure & Form Factor

The case design follows a classic clamshell mini-laptop form factor. Below are the early prototyping stages and a visual scale comparison with a standard 10-inch netbook:
![[Pasted image 20260625183402.png|351]]
![[photo_5325939866191208506_w.jpg|508]]
![[photo_5325939866191208504_w.jpg|390]]
![[photo_5325939866191208506_w.jpg|402]]
![[photo_5325939866191208507_w.jpg]]
## 🐧 Software Setup & OS Configuration

Getting a stable operating system running on this specific Rockchip platform involved significant trial and error across multiple Linux distributions:

1.  **Armbian (Ubuntu-based):** Pre-installed on some modules, but lacked many standard packages out-of-the-box, making dependency management frustrating.
2.  **Arch Linux ARM:** Suffered from filesystem stability issues on this hardware, frequently dropping into a `read-only` state during heavy workloads.
 ![[photo_5325939866191208510_y.jpg|303]]![[photo_5325939866191208511_y 2.jpg|313]]
3.  **Debian 13 (Trixie / Armbian-optimized):** **Success.** This proved to be the most stable distribution for the DshanPi-A1. You can acquire the image directly from the [Armbian Boards Directory](https://armbian.com/boards/dshanpi-a1).

### 🛠️ Setting Up the Graphical User Interface (GUI)

The stable Debian image comes as a headless (CLI-only) environment. To configure Xorg and a display manager like `LightDM`, follow these configuration steps:

#### 1. Create or Edit the Xorg Configuration File
Open your terminal and initialize a new configuration file for the graphics pipeline:
```bash
sudo nano /etc/X11/xorg.conf.d/20-modesetting.conf
```

#### 2. Add the Video Device Layout
Paste the configuration block below to explicitly assign the `modesetting` driver to the primary DRM/KMS hardware node:
```telegram
Section "Device"
    Identifier  "Rockchip Graphics"
    Driver      "modesetting"
    Option      "kmsdev" "/dev/dri/card0"
EndSection

Section "Screen"
    Identifier  "Default Screen"
    Device      "Rockchip Graphics"
EndSection
```
*Save and exit (`Ctrl+O`, `Enter`, then `Ctrl+X` in Nano).*

#### 3. Configure Permissions for the Display Manager
If Xorg fails to start due to permission restrictions, ensure the display manager user (e.g., `lightdm`) has explicit access to the video, rendering, and TTY device interfaces:
```bash
sudo usermod -aG video,render,tty lightdm
```

#### 4. Launch the Graphical Interface
Restart the display manager service to initialize your desktop environment:
```bash
sudo systemctl restart lightdm
```

---

## ⚠️ Known Issues & Current Limitations

As an early-stage prototype, there are several open hardware and software bugs I am actively addressing:
*   **Limited USB Ports:** The board currently exposes only two usable USB ports.
*   **Bluetooth Driver Stack:** Onboard Bluetooth is not initializing correctly under Debian 13.
*   **Keyboard Cable Routing:** Routing a standard USB Type-C cable through the 3D-printed hinge mechanism has proven difficult. I am planning to transition to an ultra-compact **M5Stack CardKB v1.1 Mini Keyboard (I2C/Serial)** to save space.

---

## 🛠️ Next Steps & Future Development

In upcoming iterations of this project, I plan to:
- [ ] Implement custom diagnostic and security utilities.
- [ ] Refine the 3D-printable enclosure models to improve structural integrity and cable management.
- [ ] Resolve onboard wireless/Bluetooth driver constraints.
- [ ] Integrate the CardKB mini keyboard module directly into the lower chassis.

---

## 🔗 Useful Resources & Documentation

*   **Official Hardware Introduction:** [100Ask Rockchip DshanPi-A1 Documentation](https://docs.100ask.net/rockchip/en/docs/DshanPi-A1/intro)
*   **Flashing via Maskrom Mode:** [Step-by-Step eMMC Flashing Guide](https://docs.100ask.net/rockchip/en/docs/DshanPi-A1/part1/01-3_Flash2eMMC/)
*   **Alternative Distributions & Images:** [DshanPi Wiki Resource Acquisition](https://wiki.dshanpi.org/docs/DshanPi-A1/QuickStart/ResourceAcquisition/)

---

💡 *Feel free to star this repository if you find it interesting, and don't hesitate to open an issue if you have questions about the build!*
