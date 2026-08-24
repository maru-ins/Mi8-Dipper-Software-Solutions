# Xiaomi Mi 8 (Dipper) --- Hardbrick Recovery & Instant UBL

Dokumentasi recovery **Xiaomi Mi 8 (Dipper)** dari kondisi
hardbrick/stuck boot sampai perangkat dapat digunakan kembali, sekaligus
proses **Instant UBL (Unlock Bootloader)**.

> **Status:** Dokumentasi berdasarkan prosedur yang berhasil dilakukan
> pada perangkat Mi 8 Snapdragon 845 (SDM845).
>
> **Catatan:** Nama file, versi firmware, dan hasil tiap perangkat dapat
> berbeda. Jangan menjalankan proses erase/flash secara sembarangan,
> terutama pada EFS/modem.

------------------------------------------------------------------------

## 1. Device Information

  Item                      Value
  ------------------------- ----------------------------------
  Device                    Xiaomi Mi 8
  Codename                  `dipper`
  SoC                       Qualcomm Snapdragon 845 / SDM845
  Storage                   UFS
  Recovery Interface        Qualcomm EDL / 9008
  Service Tool              UnlockTool
  Firmware yang digunakan   MIUI 12.0.3.0.QEAMIXM
  Android base              Android 10

------------------------------------------------------------------------

## 2. Kondisi Awal

Gejala perangkat sebelum recovery:

-   Stuck pada logo **MI**.
-   Perangkat mengalami **glitch hijau** sebelum mati.
-   Perangkat kemudian tidak dapat boot normal.
-   Recovery/Fastboot tidak dapat digunakan secara normal.
-   Perangkat dapat masuk ke **Qualcomm EDL 9008** melalui test point.
-   Pada percobaan awal, koneksi Sahara/Firehose dapat mengalami
    kegagalan.

Contoh error yang muncul:

``` text
Handshaking... FAIL
[Sahara] Reading Hello - Failed to handshake with device PBL!
Trying to connect to FIREHOSE Mode!
Connecting to flash programmer... FAIL
Waiting for response timeout!
```

Error tersebut menunjukkan bahwa perangkat sudah terdeteksi pada
Qualcomm 9008, tetapi komunikasi **Sahara → PBL** belum berhasil.

------------------------------------------------------------------------

# 3. Files & Tools Used

## 3.1 UnlockTool

Tool utama yang digunakan untuk komunikasi Qualcomm EDL dan proses
recovery.

Contoh versi:

``` text
UnlockTool 2026.08.16.0
```

> Gunakan versi UnlockTool yang mendukung Mi 8 / SDM845 dan operasi EDL
> yang diperlukan.

------------------------------------------------------------------------

## 3.2 Xiaomi Mi 8 Fastboot Firmware

Firmware yang digunakan:

``` text
dipper_global_images_V12.0.3.0.QEAMIXM_20211213.0000.00_10.0_global
```

Firmware ini digunakan sebagai sumber file flashing Mi 8.

Struktur penting yang biasanya terdapat di dalam firmware Qualcomm:

``` text
images/
├── rawprogram0.xml
├── rawprogram1.xml
├── rawprogram2.xml
├── patch0.xml
├── prog_ufs_firehose_sdm845_ddr.elf
├── xbl_a.img
├── xbl_b.img
├── abl_a.img
├── abl_b.img
├── boot.img
├── vendor.img
├── system.img
└── ...
```

**PENTING:** Jangan mengganti Firehose dengan file dari device lain
walaupun sama-sama menggunakan Snapdragon 845.

------------------------------------------------------------------------

## 3.3 Qualcomm Firehose

Firehose yang digunakan pada proses ini:

``` text
prog_ufs_firehose_sdm845_ddr.elf
```

Firehose berfungsi sebagai flash programmer setelah komunikasi Sahara
berhasil.

Alur sederhananya:

``` text
PC
 │
 ▼
Qualcomm 9008 / EDL
 │
 ▼
PBL
 │
 ▼
Sahara
 │
 ▼
Firehose
 │
 ▼
UFS
 │
 ▼
Flash / Repair
```

------------------------------------------------------------------------

## 3.4 Rawprogram & Patch XML

File XML dari firmware digunakan oleh tool untuk menentukan partisi dan
file image yang akan diproses.

File utama:

``` text
rawprogram0.xml
rawprogram1.xml
rawprogram2.xml
patch0.xml
```

**Jangan mengedit XML secara manual kecuali benar-benar memahami
struktur partisi Mi 8.**

------------------------------------------------------------------------

# 4. Hardware / Connection

Yang diperlukan:

-   Xiaomi Mi 8 motherboard
-   USB data cable
-   PC Windows
-   Qualcomm USB driver
-   Test Point EDL Mi 8
-   UnlockTool
-   Firmware Mi 8
-   Multimeter/PSU untuk troubleshooting jika diperlukan

Saat berhasil masuk EDL, Windows harus mendeteksi:

``` text
Qualcomm HS-USB QDLoader 9008 (COMxx)
```

Contoh:

``` text
Qualcomm HS-USB QDLoader 9008 (COM14)
```

> Pastikan port yang dipilih di UnlockTool adalah COM Qualcomm 9008,
> bukan port lain seperti Bluetooth Serial.

------------------------------------------------------------------------

# 5. Recovery Workflow

## Step 1 --- Prepare Firmware

Extract firmware Mi 8:

``` text
dipper_global_images_V12.0.3.0.QEAMIXM...
```

Pastikan file berikut tersedia:

``` text
rawprogram0.xml
rawprogram1.xml
rawprogram2.xml
patch0.xml
prog_ufs_firehose_sdm845_ddr.elf
```

------------------------------------------------------------------------

## Step 2 --- Install Qualcomm Driver

Install:

``` text
Qualcomm HS-USB QDLoader 9008
```

Kemudian buka:

``` text
Device Manager
```

Pastikan perangkat muncul sebagai:

``` text
Qualcomm HS-USB QDLoader 9008 (COMxx)
```

------------------------------------------------------------------------

## Step 3 --- Enter EDL Mode

Matikan perangkat.

Masuk EDL menggunakan **Mi 8 Test Point**.

Setelah USB terhubung, periksa Device Manager.

Target:

``` text
Qualcomm HS-USB QDLoader 9008
```

Jangan melanjutkan proses flash jika device tidak stabil atau terus
disconnect.

------------------------------------------------------------------------

## Step 4 --- Open UnlockTool

Buka UnlockTool.

Pilih:

``` text
Brand  : Xiaomi
Model  : Xiaomi Mi 8 [Dipper]
Platform: Qualcomm
```

Kemudian pilih COM yang benar:

``` text
COMxx = Qualcomm HS-USB QDLoader 9008
```

Jika terdapat beberapa COM, jangan memilih:

``` text
Standard Serial over Bluetooth link
```

------------------------------------------------------------------------

# 6. Sahara / Firehose Handshake

Pada tahap ini UnlockTool mencoba berkomunikasi dengan PBL.

Normalnya:

``` text
9008 detected
       ↓
Connecting to device
       ↓
Sahara handshake
       ↓
Firehose programmer
       ↓
UFS communication
```

Jika muncul:

``` text
[Sahara] Reading Hello - Failed to handshake with device PBL!
```

jangan langsung menyimpulkan UFS rusak.

Periksa terlebih dahulu:

1.  Kabel USB.
2.  Port USB.
3.  Qualcomm driver.
4.  COM port.
5.  Stabilitas EDL.
6.  PC lain.
7.  Koneksi test point.
8.  Kondisi power motherboard.

------------------------------------------------------------------------

# 7. Flashing

Setelah Sahara dan Firehose berhasil terhubung, gunakan firmware Mi 8
yang sesuai.

File XML digunakan sesuai struktur firmware:

``` text
rawprogram0.xml
rawprogram1.xml
rawprogram2.xml
patch0.xml
```

Pada tahap ini **jangan melakukan erase EFS/modem secara sembarangan**.

Partisi seperti:

``` text
modem
fsg
fsc
persist
efs
```

berhubungan dengan fungsi penting perangkat seperti modem, calibration,
IMEI, dan data identitas perangkat.

### Prinsip recovery

Prioritaskan:

``` text
Boot-critical partitions
        ↓
System/vendor
        ↓
Boot normal
        ↓
UBL
```

Hindari:

``` text
Erase EFS
Erase modem
Format storage
```

kecuali memang diperlukan dan memiliki backup yang valid.

------------------------------------------------------------------------

# 8. First Boot

Setelah flashing selesai:

``` text
Reboot
```

Tunggu proses boot pertama.

Boot pertama setelah recovery dapat memerlukan waktu lebih lama.

Periksa:

-   MI Logo
-   Boot animation
-   Recovery/Fastboot
-   Sistem Android
-   Touch/display
-   Wi-Fi
-   Bluetooth
-   SIM/network
-   IMEI/baseband
-   Storage

------------------------------------------------------------------------

# 9. Instant UBL

Setelah perangkat kembali dapat berkomunikasi melalui mode yang
diperlukan, lakukan proses **Instant UBL** menggunakan metode UnlockTool
yang digunakan dalam recovery ini.

Target akhir:

``` text
Bootloader status = UNLOCKED
```

Setelah UBL, verifikasi melalui fastboot:

``` bash
fastboot devices
```

Kemudian:

``` bash
fastboot getvar unlocked
```

atau perintah verifikasi lain yang sesuai dengan metode UBL yang
digunakan.

> **Catatan penting:** Jangan menganggap perangkat sudah UBL hanya
> karena proses tool menampilkan `OK`. Selalu verifikasi status
> bootloader dari perangkat/fastboot.

------------------------------------------------------------------------

# 10. Verification Checklist

Setelah recovery:

-   [ ] Device dapat boot ke Android
-   [ ] Tidak stuck di logo MI
-   [ ] Tidak mengalami green glitch
-   [ ] Fastboot dapat digunakan
-   [ ] Recovery dapat digunakan
-   [ ] Wi-Fi berfungsi
-   [ ] Bluetooth berfungsi
-   [ ] SIM/network berfungsi
-   [ ] IMEI masih ada
-   [ ] Baseband terdeteksi
-   [ ] Storage terdeteksi normal
-   [ ] Bootloader menunjukkan `UNLOCKED`
-   [ ] Reboot normal

------------------------------------------------------------------------

# 11. Troubleshooting

## Sahara FAIL

``` text
[Sahara] Reading Hello - Failed to handshake with device PBL!
```

Cek:

``` text
9008
 ↓
Driver
 ↓
USB cable
 ↓
USB port
 ↓
Test Point
 ↓
Power
 ↓
PC
```

Jika 9008 stabil tetapi Sahara selalu gagal pada beberapa PC, lanjutkan
pemeriksaan hardware.

------------------------------------------------------------------------

## Firehose FAIL

``` text
Connecting to flash programmer... FAIL
```

Jika Sahara sudah berhasil tetapi Firehose gagal:

-   Pastikan Firehose sesuai dengan SDM845/Mi 8.
-   Pastikan firmware berasal dari Mi 8/dipper.
-   Pastikan tipe storage sesuai.
-   Periksa koneksi UFS.
-   Jangan menggunakan Firehose random dari device Qualcomm lain.

------------------------------------------------------------------------

## Device Tidak Muncul 9008

Periksa:

``` text
Qualcomm Driver
USB Cable
Test Point
USB Port
Motherboard Power
```

Target:

``` text
Qualcomm HS-USB QDLoader 9008
```

------------------------------------------------------------------------

# 12. Important Notes

Dokumentasi ini dibuat sebagai catatan teknis untuk **Xiaomi Mi 8 /
Dipper**.

Jangan langsung menerapkan file:

``` text
Firehose
rawprogram
patch
partition image
```

ke device lain hanya karena chipsetnya sama.

Contoh:

``` text
SDM845 ≠ otomatis Firehose compatible
```

Gunakan file yang sesuai dengan device, codename, storage, dan kebutuhan
repair.

------------------------------------------------------------------------

# 13. Files Used --- Final List

Checklist file yang digunakan pada prosedur:

``` text
[✓] UnlockTool
[✓] Qualcomm HS-USB QDLoader 9008 Driver
[✓] Xiaomi Mi 8 Dipper Fastboot Firmware
[✓] rawprogram0.xml
[✓] rawprogram1.xml
[✓] rawprogram2.xml
[✓] patch0.xml
[✓] prog_ufs_firehose_sdm845_ddr.elf
[✓] USB Data Cable
[✓] Mi 8 EDL Test Point
```

### Firmware

``` text
MIUI V12.0.3.0.QEAMIXM
Android 10
Codename: dipper
SoC: Snapdragon 845 / SDM845
```

------------------------------------------------------------------------

# 14. Result

**Before:**

``` text
Mi 8
 ↓
Stuck MI Logo
 ↓
Green glitch
 ↓
Power off
 ↓
Unable to boot normally
```

**Recovery:**

``` text
EDL 9008
 ↓
Qualcomm PBL / Sahara
 ↓
Firehose
 ↓
UFS
 ↓
Flash firmware
 ↓
Boot
 ↓
Instant UBL
```

**After:**

``` text
Mi 8 Dipper
      ↓
Recovered
      ↓
Booting normally
      ↓
Bootloader UNLOCKED
```

------------------------------------------------------------------------

## Disclaimer

This documentation is intended for educational and repair documentation
purposes.

Flashing or modifying partitions can permanently damage a device or
cause loss of data/IMEI/calibration data. Always keep a valid backup
before modifying critical partitions.

------------------------------------------------------------------------

## Author

**Diva Lutfiando**

Xiaomi Mi 8 / Dipper --- Hardbrick Recovery & Instant UBL Documentation
