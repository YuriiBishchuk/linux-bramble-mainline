# 🐧 Linux Mainline 7.3+ Port for Google Pixel 4a (5G) (`bramble`)

> **Platform**: Qualcomm SM7250 (Snapdragon 765G / Lito)  
> **Codename**: `bramble` (Google Pixel 4a 5G)  
> **Target Upstream**: Linux 7.3+ Mainline Tree  
> **Parent Cockpit**: [`Pixel4a5G-lab`](https://github.com/YuriiBishchuk/Pixel4a5G-lab)  

---

## 1. Objectives

This repository houses the mainline Linux kernel adaptation for the Google Pixel 4a (5G). The goal is complete upstream mainline enablement following Linux Kernel Mailing List (LKML) and `linux-arm-msm` standards:

* Pure upstream Device Tree (`sm7250.dtsi` + `sm7250-google-bramble.dts`).
* Zero Android userland dependencies (No LXC, No Halium, No libhybris).
* Direct hardware support:
  - **Display**: Native Freedreno MSM DPU 6.0 with Samsung OLED Command Mode panel.
  - **Bluetooth**: Direct BlueZ connection via `hci_qca` SerDev node on `/dev/ttyMSM0`.
  - **Wi-Fi**: `ath10k_snoc` via Qualcomm IPC Router (`qrtr`).
  - **Modem & DSP**: Standard Linux `remoteproc` (`qcom_q6v5_mss`) with `rmtfs`.
  - **Security**: Upstream Titan M (`citadel`) SPI driver.

---

## 3. Building Against Upstream Linux 7.3

1. Clone vanilla Linux 7.3:
   ```bash
   git clone --depth=1 -b v7.3 https://github.com/torvalds/linux.git linux-7.3-mainline
   cd linux-7.3-mainline
   ```
2. Apply the SM7250 adaptation patch:
   ```bash
   git apply /path/to/linux-bramble-mainline/patches/sm7250-kernel-adaptation.patch
   ```
3. Copy board DTS and driver fragments into tree:
   ```bash
   cp /path/to/linux-bramble-mainline/arch/arm64/boot/dts/qcom/* arch/arm64/boot/dts/qcom/
   cp /path/to/linux-bramble-mainline/drivers/clk/qcom/gcc-sm7250.c drivers/clk/qcom/
   cp /path/to/linux-bramble-mainline/drivers/pinctrl/qcom/pinctrl-sm7250.c drivers/pinctrl/qcom/
   cp /path/to/linux-bramble-mainline/include/dt-bindings/clock/qcom,sm7250-gcc.h include/dt-bindings/clock/
   ```
4. Build `Image.lz4` and DTBs:
   ```bash
   make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- defconfig
   make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j$(nproc) Image.lz4 dtbs
   ```

All contributions to this repository must follow the Linux Kernel upstream protocol:
1. **Developer Certificate of Origin (DCO)**: All commits must be signed off (`git commit -s`).
2. **Schema Validation**: Must pass `make CHECK_DTBS=y qcom/sm7250-google-bramble.dtb`.
3. **Coding Style**: Must pass `scripts/checkpatch.pl --strict`.
4. Detailed guidelines: See [UPSTREAM_MAINLINE_SUBMISSION_GUIDELINES.md](https://github.com/YuriiBishchuk/Pixel4a5G-lab/blob/master/spec-kit/09_UPSTREAM_MAINLINE_SUBMISSION_GUIDELINES.md).
