<div align="center">

![Xiaomi Mi 8 Dipper Motherboard](https://static.life.ru/posts/2018/05/1122047/539056279364c0b1486be1c5d1877000.jpg)

### Xiaomi Mi 8 (Dipper) — Hardbrick Recovery → Instant UBL → Global ROM

*EDL / 9008 • Qualcomm SDM845 • Flashing • Instant UBL*

</div>

> 🖼️ Banner image: Xiaomi Mi 8 photo from [Life.ru](https://life.ru/p/1122047)

Dokumentasi singkat berdasarkan proses yang berhasil saya lakukan pada **Xiaomi Mi 8 / Dipper** dari kondisi hardbrick sampai **bootloader unlock**, kemudian kembali ke Global ROM.

> **Catatan:** Gunakan hanya file untuk Mi 8 (`dipper`). Proses flashing dan modifikasi bootloader memiliki risiko brick atau kehilangan data.

## Software & file yang perlu disiapkan

1. **[UnlockTool](https://unlocktool.net/)** — untuk proses EDL/9008 dan flashing. Pada percobaan saya menggunakan akses sewa sekitar 6 jam.
2. **[Mi 8 China Fastboot V12.5.2.0.QEACNXM](https://xmfirmwareupdater.com/miui/dipper/stable/V12.5.2.0.QEACNXM/)** — **pilih Fastboot ROM**, bukan Recovery ROM. File yang tersedia untuk versi ini adalah `dipper_images_V12.5.2.0.QEACNXM_20211028.0000.00_10.0_cn_f4ae6b9441.tgz`.
3. **China firmware modifikasi untuk Instant UBL** — *File terlampir* **Jangan menggantinya dengan China ROM biasa.**
4. **Software Instant UBL** — tool yang digunakan pada tahap unlock. *File terlampir*
5. **[Mi 8 Global Fastboot ROM](https://xmfirmwareupdater.com/miui/dipper/stable/V12.0.3.0.QEAMIXM/)** — contoh yang saya gunakan adalah `V12.0.3.0.QEAMIXM`. Pastikan yang dipilih **Fastboot ROM**. 
6. **Qualcomm HS-USB QDLoader 9008 Driver** — agar perangkat terbaca saat EDL. *File terlampir*
7. **[Minimal Adb and Fastboot Devices](https://www.mediafire.com/file/1g634wggkh4kw7k/minimal_adb_fastboot_v1.4.3.zip/file)** - opsional

> Saya sarankan semua file diekstrak dan diletakkan di **Desktop** terlebih dahulu. Secara praktik ini membuat path lebih pendek dan pemilihan folder di tool lebih mudah, terutama saat proses flash.

## Proses

**1. Masukkan Mi 8 ke EDL/9008** menggunakan Test Point. Di Device Manager pastikan terbaca sebagai `Qualcomm HS-USB QDLoader 9008 (COMxx)`.

**2. Flash China ROM pertama.** Buka **UnlockTool → Snapdragon/Qualcomm → Mi 8**, pilih folder ROM China yang sudah diekstrak, masuk ke folder `images`, lalu **Load** dan **Flash**. Tunggu sampai selesai.

**3. Masuk EDL lagi.** Setelah flash pertama selesai, kembalikan HP ke mode EDL/9008.

**4. Flash China firmware khusus Instant UBL.** Di UnlockTool pilih lagi **Mi 8**, arahkan ke firmware China yang sudah dimodifikasi untuk Instant UBL, pilih folder `images`, **Load**, lalu **Flash** sampai selesai.

**5. Masuk Fastboot dan jalankan Instant UBL.** Setelah flash kedua selesai, masuk Fastboot. Buka software Instant UBL, klik **Scan Device** di kiri bawah, tunggu device terbaca, lalu klik tombol proses di bagian kanan dan tunggu sampai selesai.

**6. Verifikasi UBL.** Setelah sukses, HP dapat terlihat masih stuck di logo MI. Jangan langsung menganggap gagal; masuk kembali ke Fastboot dan cek status unlock, misalnya dengan `fastboot getvar unlocked`. Targetnya adalah status **UNLOCKED / unlocked: yes**.

**7. Flash Global ROM.** Setelah UBL berhasil, tetap di Fastboot lalu buka **UnlockTool** atau **MiFlashTool**. Pilih **Global Fastboot ROM** yang sesuai untuk Mi 8, Load/Select firmware, lalu Flash sampai selesai. Setelah itu reboot dan tunggu boot pertama.

## Alur singkat

```text
Hardbrick
   ↓
EDL / 9008
   ↓
China Fastboot ROM V12.5.2.0.QEACNXM
   ↓
EDL lagi
   ↓
China Modified ROM (Instant UBL)
   ↓
Fastboot
   ↓
Instant UBL
   ↓
Bootloader UNLOCKED
   ↓
Global Fastboot ROM
   ↓
Selesai
```

## Troubleshooting singkat

Jika UnlockTool berhenti pada `Sahara Reading Hello - Failed to handshake with device PBL`, jangan langsung menyimpulkan UFS rusak. Cek terlebih dahulu kestabilan 9008, driver Qualcomm, kabel USB, port USB, Test Point, dan coba PC lain bila perlu.

## Referensi ROM

- **China V12.5.2.0.QEACNXM:** [Download](https://xiaomirom.com/en/download/mi-8-dipper-stable-V12.5.2.0.QEACNXM/) citeturn205265search7
- **Global V12.0.3.0.QEAMIXM:** [Download](https://xiaomirom.com/en/download/mi-8-dipper-stable-V12.0.3.0.QEAMIXM/) citeturn205265search4
- **Alternatif daftar ROM Mi 8:** [MiROM](https://mirom.ezbox.idv.tw/en/phone/dipper/) citeturn205265search0

## Author

**Diva Lutfiando**
