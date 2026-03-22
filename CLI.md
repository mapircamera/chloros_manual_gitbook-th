# CLI : บรรทัดคำสั่ง

<รูป><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>

**Chloros CLI** ให้การเข้าถึงบรรทัดคำสั่งที่มีประสิทธิภาพสำหรับกลไกการประมวลผลภาพ Chloros ช่วยให้การทำงานอัตโนมัติ การเขียนสคริปต์ และการทำงานแบบไม่มีส่วนหัวสำหรับเวิร์กโฟลว์เกี่ยวกับภาพของคุณ

### คุณสมบัติที่สำคัญ

* 🚀 **ระบบอัตโนมัติ** - การประมวลผลสคริปต์ชุดข้อมูลหลายชุด
* 🔗 **บูรณาการ** - ฝังอยู่ในเวิร์กโฟลว์และไปป์ไลน์ที่มีอยู่
* 💻 **การทำงานแบบไร้หัว** - ทำงานโดยไม่มี GUI
* 🌍 **หลายภาษา** - รองรับ 38 ภาษา
* ⚡ **การประมวลผลแบบขนาน** - [Dynamic Compute Adaptation](processing-architecture/dynamic-compute-adaptation.md) ปรับให้เหมาะสมสำหรับฮาร์ดแวร์ของคุณโดยอัตโนมัติ

### ความต้องการ

| ข้อกำหนด | รายละเอียด |
| -------------------- | ------------------------------------------------------------------- |
| **ระบบปฏิบัติการ** | Windows 10/11 (64 บิต), Linux x86_64 (amd64), Linux arm64 (NVIDIA Jetson JetPack 6) |
| **ใบอนุญาต** | Chloros+ ([ต้องใช้แผนการชำระเงิน](https://cloud.mapir.camera/pricing)) |
| **ความทรงจำ** | RAM ขั้นต่ำ 8GB (แนะนำ 16GB) |
| **อินเตอร์เน็ต** | จำเป็นสำหรับการเปิดใช้งานใบอนุญาต |
| **พื้นที่ดิสก์** | แตกต่างกันไปตามขนาดโครงการ |

{% hint style="warning" %}
**ข้อกำหนดสิทธิ์การใช้งาน**: CLI จำเป็นต้องสมัครสมาชิก Chloros+ แบบชำระเงิน แผนมาตรฐาน (ฟรี) ไม่มีการเข้าถึง CLI ไปที่ [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) เพื่ออัปเกรด
{% endhint %}

## เริ่มต้นอย่างรวดเร็ว

### การติดตั้ง

#### Windows

CLI จะรวมอยู่ในโปรแกรมติดตั้ง Chloros โดยอัตโนมัติ:

1. ดาวน์โหลดและรัน **Chloros Installer.exe**

2. กรอกวิซาร์ดการติดตั้งให้เสร็จสมบูรณ์
3. CLI ติดตั้งไว้ที่: `C:\Program Files\Chloros\resources\cli\chloros-cli.exe`

{% hint style="success" %}
โปรแกรมติดตั้งจะเพิ่ม `chloros-cli` ให้กับ PATH ระบบของคุณโดยอัตโนมัติ รีสตาร์ทเทอร์มินัลของคุณหลังการติดตั้ง
{% endhint %}

#### Linux

ติดตั้งแพ็คเกจ `.deb` สำหรับสถาปัตยกรรมของคุณ:

```bash
# Linux amd64
sudo dpkg -i chloros-amd64.deb

# Linux arm64 (NVIDIA Jetson, JetPack 6)
sudo dpkg -i chloros-arm64-jp6.deb
```

สำหรับการตั้งค่า Linux โดยละเอียด โปรดดูที่ [การติดตั้ง Linux](linux/linux-installation.md)

### การตั้งค่าครั้งแรก

ก่อนที่จะใช้ CLI ให้เปิดใช้งานใบอนุญาต Chloros+ ของคุณ:

**Windows:**

```powershell
# Login with your Chloros+ account
chloros-cli login user@example.com 'your_password'

# Check license status
chloros-cli status

# Process your first project
chloros-cli process "C:\Images\Dataset001"
```

**Linux:**

```bash
# Login with your Chloros+ account
chloros-cli login user@example.com 'your_password'

# Check license status
chloros-cli status

# Process your first project
chloros-cli process ~/images/dataset001
```

### การใช้งานพื้นฐาน

ประมวลผลโฟลเดอร์ด้วยการตั้งค่าเริ่มต้น:

**Windows:**

```powershell
chloros-cli process "C:\Images\Dataset001"
```

**Linux:**

```bash
chloros-cli process ~/images/dataset001
```

***

## การอ้างอิงคำสั่ง

### ไวยากรณ์ทั่วไป

```
chloros-cli [global-options] <command> [command-options]
```

***

## คำสั่ง

### `process` - ประมวลผลรูปภาพ

ประมวลผลภาพในโฟลเดอร์ที่มีการปรับเทียบ

**ไวยากรณ์:**

```bash
chloros-cli process <input-folder> [options]
```

**ตัวอย่าง:**

```bash
# Windows
chloros-cli process "C:\Datasets\Survey_001" --vignette --reflectance

# Linux
chloros-cli process ~/datasets/survey_001 --vignette --reflectance
```

#### ตัวเลือกคำสั่งกระบวนการ

| ตัวเลือก | พิมพ์ | ค่าเริ่มต้น | คำอธิบาย |
| --------------------- | ------- | -------------- | -------------------------------------------------------------------------------------- |
| `<input-folder>` | เส้นทาง | _จำเป็น_ | โฟลเดอร์ที่มีรูปภาพหลายสเปกตรัม RAW/JPG |
| `-o, --output` | เส้นทาง | เช่นเดียวกับอินพุต | โฟลเดอร์เอาท์พุตสำหรับรูปภาพที่ประมวลผล |
| `-n, --project-name` | สตริง | สร้างอัตโนมัติ | ชื่อโปรเจ็กต์แบบกำหนดเอง |
| `--vignette` | ตั้งค่าสถานะ | เปิดใช้งาน | เปิดใช้งานการแก้ไขบทความสั้น |
| `--no-vignette` | ตั้งค่าสถานะ | - | ปิดใช้งานการแก้ไขบทความสั้น |
| `--reflectance` | ตั้งค่าสถานะ | เปิดใช้งาน | เปิดใช้งานการปรับเทียบการสะท้อนแสง |
| `--no-reflectance` | ตั้งค่าสถานะ | - | ปิดใช้งานการปรับเทียบการสะท้อนแสง |
| `--ppk` | ตั้งค่าสถานะ | ปิดการใช้งาน | ใช้การแก้ไข PPK จากข้อมูลเซ็นเซอร์วัดแสง .daq |
| `--format` | ทางเลือก | TIFF (16 บิต) | รูปแบบเอาต์พุต: `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)` |
| `--min-target-size` | จำนวนเต็ม | อัตโนมัติ | ขนาดเป้าหมายขั้นต่ำเป็นพิกเซลสำหรับการตรวจจับแผงการปรับเทียบ |
| `--target-clustering` | จำนวนเต็ม | อัตโนมัติ | เกณฑ์การจัดกลุ่มเป้าหมาย (0-100) |
| `--debayer` | ทางเลือก | `standard` | วิธีการชำระล้าง: `standard` หรือ `texture-aware` (Chloros+ เท่านั้น) |
| `--target`, `--targets` | ตั้งค่าสถานะ | ปิดการใช้งาน | ค้นหาเฉพาะเป้าหมายการสอบเทียบในโฟลเดอร์ย่อย "เป้าหมาย" หรือ "เป้าหมาย" เท่านั้น (เร่งการประมวลผล) |
| `--indices` | รายการ | ไม่มี | ดัชนีพืชพรรณที่ต้องคำนวณ (เช่น `--indices NDVI NDRE GNDVI`) |
| `--exposure-pin-1` | สตริง | ไม่มี | ล็อคค่าแสงสำหรับกล้องรุ่น (พิน 1) |
| `--exposure-pin-2` | สตริง | ไม่มี | ล็อคค่าแสงสำหรับกล้องรุ่น (พิน 2) |
| `--recal-interval` | จำนวนเต็ม | อัตโนมัติ | ช่วงเวลาการปรับเทียบใหม่เป็นวินาที |
| `--timezone-offset` | จำนวนเต็ม | 0 | ชดเชยเขตเวลาเป็นชั่วโมง |

***

### `login` - ตรวจสอบบัญชี

เข้าสู่ระบบด้วยข้อมูลประจำตัว Chloros+ ของคุณเพื่อเปิดใช้งานการประมวลผล CLI

**ไวยากรณ์:**

```bash
chloros-cli login <email> <password>
```

**ตัวอย่าง:**

```bash
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**อักขระพิเศษ**: ใช้เครื่องหมายคำพูดเดี่ยวรอบรหัสผ่านที่มีอักขระ เช่น `$`, `!` หรือช่องว่าง
{% endhint %}

**ผลลัพธ์:**<รูป><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>***

### `logout` - ล้างข้อมูลรับรอง

ล้างข้อมูลประจำตัวที่เก็บไว้และออกจากระบบบัญชีของคุณ

**ไวยากรณ์:**

```bash
chloros-cli logout
```

**ตัวอย่าง:**

```bash
chloros-cli logout
```

**ผลลัพธ์:**

```
✓ Logout successful
ℹ Credentials cleared from cache
```

{% hint style="info" %}
**ผู้ใช้ SDK**: Python SDK ยังมีวิธี `logout()` แบบเป็นโปรแกรมสำหรับการล้างข้อมูลรับรองภายในสคริปต์ Python ดู [Python SDK เอกสาร] (api-python-sdk.md#logout) สำหรับรายละเอียด
{% endhint %}

***

### `status` - ตรวจสอบสถานะใบอนุญาต

แสดงใบอนุญาตปัจจุบันและสถานะการรับรองความถูกต้อง

**ไวยากรณ์:**

```bash
chloros-cli status
```

**ตัวอย่าง:**

```bash
chloros-cli status
```

**ผลลัพธ์:**

```
╔══════════════════════════════════════╗
║     LICENSE & ACCOUNT INFORMATION    ║
╚══════════════════════════════════════╝

📧 Email: user@example.com
📋 Plan: Chloros+ Professional
🔓 API/CLI Access: Enabled
✓ Status: Active
```

***

### `export-status` - ตรวจสอบความคืบหน้าการส่งออก

ติดตามความคืบหน้าในการส่งออก Thread 4 ในระหว่างหรือหลังการประมวลผล

**ไวยากรณ์:**

```bash
chloros-cli export-status
```

**ตัวอย่าง:**

```bash
chloros-cli export-status
```

**กรณีการใช้งาน:** เรียกใช้คำสั่งนี้ขณะประมวลผลเพื่อตรวจสอบความคืบหน้าในการส่งออก***

### `language` - จัดการภาษาอินเทอร์เฟซ

ดูหรือเปลี่ยนภาษาอินเทอร์เฟซ CLI

**ไวยากรณ์:**

```bash
# Show current language
chloros-cli language

# List all available languages
chloros-cli language --list

# Set a specific language
chloros-cli language <language-code>
```

**ตัวอย่าง:**

```bash
# View current language
chloros-cli language

# List all 38 supported languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Change to Japanese
chloros-cli language ja
```

#### ภาษาที่รองรับ (ทั้งหมด 38 ภาษา)

| รหัส | ภาษา | ชื่อพื้นเมือง |
| ------- | --------------------- | ---------------- |
| `en` | อังกฤษ | อังกฤษ |
| `es` | สเปน | ภาษาสเปน |
| `pt` | โปรตุเกส | ภาษาโปรตุเกส |
| `fr` | ฝรั่งเศส | ฝรั่งเศส |
| `de` | เยอรมัน | เยอรมัน |
| `it` | ภาษาอิตาลี | อิตาเลียโน่ |
| `ja` | ญี่ปุ่น | ภาษาญี่ปุ่น |
| `ko` | เกาหลี | เกาหลี |
| `zh` | จีน (ตัวย่อ) | 简体中文 |
| `zh-TW` | จีน (ตัวเต็ม) | 繁體中文 |
| `ru` | รัสเซีย | รัสสกี้ |
| `nl` | ดัตช์ | เนเธอร์แลนด์ |
| `ar` | ภาษาอาหรับ | العربية |
| `pl` | ภาษาโปแลนด์ | โปลสกี้ |
| `tr` | ภาษาตุรกี | เตอร์กเช่ |
| `hi` | ฮินดี | हिंदी |
| `id` | อินโดนีเซีย | บาฮาซาอินโดนีเซีย |
| `vi` | ภาษาเวียดนาม | Tiếng Viết |
| `th` | ไทย | ไทย |
| `sv` | สวีเดน | สเวนสกา |
| `da` | ภาษาเดนมาร์ก | ดันสค์ |
| `no` | นอร์เวย์ | นอร์สค์ |
| `fi` | ภาษาฟินแลนด์ | ซูโอมิ |
| `el` | กรีก | เกรียน |
| `cs` | เช็ก | เชสตินา |
| `hu` | ฮังการี | มากยาร์ |
| `ro` | โรมาเนีย | โรมาเนีย |
| `uk` | ภาษายูเครน | Украйнська |
| `pt-BR` | โปรตุเกสแบบบราซิล | Português Brasileiro |
| `zh-HK` | กวางตุ้ง | 粵語 |
| `ms` | มาเลย์ | บาฮาซามลายู |
| `sk` | สโลวัก | สโลวีเนีย |
| `bg` | บัลแกเรีย | Български |
| `hr` | ภาษาโครเอเชีย | ฮวาตสกี |
| `lt` | ลิทัวเนีย | Lietuvių |
| `lv` | ลัตเวีย | ลัตเวียซู |
| `et` | เอสโตเนีย | เอสติ |
| `sl` | ภาษาสโลเวเนีย | สโลวีเนีย |

{% hint style="success" %}
**การคงอยู่อัตโนมัติ**: การตั้งค่าภาษาของคุณจะถูกบันทึกลงใน `~/.chloros/cli_language.json` และคงอยู่ในทุกเซสชัน
{% endhint %}

***

### `set-project-folder` - ตั้งค่าโฟลเดอร์โครงการเริ่มต้น

เปลี่ยนตำแหน่งโฟลเดอร์โปรเจ็กต์เริ่มต้น (แชร์กับ GUI บน Windows)

**ไวยากรณ์:**

```bash
chloros-cli set-project-folder <folder-path>
```

**ตัวอย่าง:**

```bash
# Windows
chloros-cli set-project-folder "C:\Projects\2025"

# Linux
chloros-cli set-project-folder ~/projects/2025
```

***

### `get-project-folder` - แสดงโฟลเดอร์โครงการ

แสดงตำแหน่งโฟลเดอร์โครงการเริ่มต้นปัจจุบัน

**ไวยากรณ์:**

```bash
chloros-cli get-project-folder
```

**ตัวอย่าง:**

```bash
chloros-cli get-project-folder
```

**ผลลัพธ์:**

```

# Windows
ℹ Current project folder: C:\Projects\2025

# Linux
ℹ Current project folder: /home/user/.local/share/chloros/projects
```

***

### `reset-project-folder` - รีเซ็ตเป็นค่าเริ่มต้น

รีเซ็ตโฟลเดอร์โครงการเป็นตำแหน่งเริ่มต้น

**ไวยากรณ์:**

```bash
chloros-cli reset-project-folder
```

***

### `selftest` - เรียกใช้การวินิจฉัยระบบ

ดำเนินการตรวจสอบวินิจฉัย 7 ครั้งเพื่อตรวจสอบการกำหนดค่าระบบของคุณ

**ไวยากรณ์:**

```bash
chloros-cli selftest
```

**ทำการวินิจฉัย:**

1. การตรวจสอบเวอร์ชั่น
2. ความพร้อมใช้งานของพอร์ต (5,000)
3. การเริ่มต้นแบ็กเอนด์
4. การทดสอบการเชื่อมต่อ API
5. ข้อมูลระบบและการตรวจจับ GPU
6. การตรวจสอบโมเดล Denoiser
7. การตรวจสอบความพร้อมของ CUDA

{% hint style="info" %}
**มีประโยชน์สำหรับการแก้ไขปัญหา**: เรียกใช้ `selftest` หลังการติดตั้งเพื่อตรวจสอบว่าระบบของคุณได้รับการกำหนดค่าอย่างถูกต้อง โดยเฉพาะบน Linux/Jetson ซึ่งการตั้งค่า GPU และ CUDA อาจต้องมีการตรวจสอบ
{% endhint %}

***

### `update` - ตรวจสอบการอัปเดต (Linux เท่านั้น)

ตรวจสอบและติดตั้งอัพเดต CLI บนระบบ Linux

**ไวยากรณ์:**

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

| ตัวเลือก | คำอธิบาย |
| --------- | ---------------------------------- |
| `--check` | ตรวจสอบเฉพาะการอัปเดต อย่าติดตั้ง |

{% hint style="info" %}
คำสั่งนี้มีอยู่ใน Linux เท่านั้น บน Windows การอัปเดตจะถูกส่งผ่านตัวติดตั้ง
{% endhint %}

***

## ตัวเลือกระดับโลก

ตัวเลือกเหล่านี้ใช้กับคำสั่งทั้งหมด:

| ตัวเลือก | พิมพ์ | ค่าเริ่มต้น | คำอธิบาย |
| ----------------- | ------- | ------------- | ------------------------------------------------ |
| `--backend-exe` | เส้นทาง | ตรวจพบอัตโนมัติ | เส้นทางไปยังไฟล์ปฏิบัติการแบ็กเอนด์ |
| `--port` | จำนวนเต็ม | 5000 | หมายเลขพอร์ตแบ็กเอนด์ API |
| `--restart` | ตั้งค่าสถานะ | - | บังคับให้รีสตาร์ทแบ็กเอนด์ (ฆ่ากระบวนการที่มีอยู่) |
| `--version` | ตั้งค่าสถานะ | - | แสดงข้อมูลเวอร์ชันและออก |
| `--help` | ตั้งค่าสถานะ | - | แสดงข้อมูลวิธีใช้และออก |

{% hint style="info" %}
**การตรวจจับอัตโนมัติแบ็กเอนด์**: เส้นทาง `--backend-exe` ถูกตรวจพบอัตโนมัติต่อแพลตฟอร์ม:
* **Windows**: `C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe`
* **Linux (.deb)**: `/usr/lib/chloros/chloros-backend`
* **Linux (แบบแมนนวล)**: `/opt/mapir/chloros/backend/chloros-backend`
{% endhint %}

**ตัวอย่างพร้อมตัวเลือกสากล:**

**Windows:**

```powershell
chloros-cli --port 5001 process "C:\Datasets\Survey_001"
```

**Linux:**

```bash
chloros-cli --port 5001 process ~/datasets/survey_001
```

***

## คู่มือการตั้งค่าการประมวลผล

### การประมวลผลแบบขนานและการปรับการคำนวณแบบไดนามิก

Chloros 1.1.0 ประกอบด้วย [Dynamic Compute Adaptation](processing-architecture/dynamic-compute-adaptation.md) — กลไกการประมวลผล **ตรวจจับฮาร์ดแวร์ของคุณโดยอัตโนมัติ** และเลือกกลยุทธ์ที่เหมาะสมที่สุด:

| แพลตฟอร์ม | กลยุทธ์ | คนงาน | ไปป์ไลน์ | หมายเหตุ |
| --- | --- | --- | --- | --- |
| **เจ็ตสัน นาโน 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` | ประสิทธิภาพของหน่วยความจำ, ซีเรียลไลซ์ |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 3 | `fused_gpu` | การประมวลผล GPU พร้อมกัน |
| **เดสก์ท็อปพร้อม GPU 8GB** | `GPU_SINGLE` | 3 | `tiled_gpu` | ประสิทธิภาพเดสก์ท็อปที่ดี |
| **เดสก์ท็อปที่มี GPU 12GB+** | `GPU_PARALLEL` | 3-4 | `fused_gpu` | ประสิทธิภาพเดสก์ท็อปที่เหมาะสมที่สุด |
| **ระบบเฉพาะ CPU** | `CPU_PARALLEL` | แกน - 1 | `cpu_fallback` | ไม่ต้องใช้ GPU |

{% hint style="success" %}
**ไม่จำเป็นต้องกำหนดค่าด้วยตนเอง!** Chloros ตรวจจับเซ็นเซอร์ความร้อน CPU, GPU, RAM และ (บน Jetson) ของคุณโดยอัตโนมัติ จากนั้นกำหนดค่าไปป์ไลน์การประมวลผลที่เหมาะสมที่สุดโดยอัตโนมัติ
{% endhint %}

### วิธีการชำระหนี้

| วิธีการ | CLI ธง | คุณภาพ | ความเร็ว | ใบอนุญาต |
| --- | --- | --- | --- | --- |
| **มาตรฐาน (เร็ว คุณภาพปานกลาง)** | `--debayer standard` | ดี | รวดเร็ว | ฟรี / Chloros+ |
| **การรับรู้พื้นผิว (ช้า คุณภาพสูงสุด)** | `--debayer texture-aware` | สูงสุด | ช้า | Chloros+ เท่านั้น |

วิธีการ debayer เริ่มต้นคือ **มาตรฐาน**วิธีการ**Texture Aware** ใช้โมเดลการลดสัญญาณรบกวนแบบ AI/ML เพื่อให้ได้เอาต์พุตคุณภาพสูงสุด แต่ต้องใช้ใบอนุญาต Chloros+ และ NVIDIA GPU

```bash
# Use Texture Aware debayer (Chloros+ only)
chloros-cli process ~/datasets/field_a --debayer texture-aware
```

### การแก้ไขขอบมืด

**ให้ประโยชน์อะไรบ้าง:** แก้ไขแสงตกที่ขอบภาพ (มุมที่มืดกว่าซึ่งพบได้ทั่วไปในภาพของกล้อง)

* **เปิดใช้งานโดยค่าเริ่มต้น** - ผู้ใช้ส่วนใหญ่ควรเปิดใช้งานสิ่งนี้ต่อไป
* ใช้ `--no-vignette` เพื่อปิดการใช้งาน

{% hint style="success" %}
**คำแนะนำ**: เปิดใช้การแก้ไขวิกเน็ตต์เสมอเพื่อให้ความสว่างทั่วทั้งเฟรมสม่ำเสมอ
{% endhint %}

### การสอบเทียบการสะท้อนแสง

แปลงค่าเซ็นเซอร์ดิบเป็นเปอร์เซ็นต์การสะท้อนแสงที่เป็นมาตรฐานโดยใช้แผงการสอบเทียบ

* **เปิดใช้งานโดยค่าเริ่มต้น** - จำเป็นสำหรับการวิเคราะห์พืชพรรณ
* ต้องมีแผงเป้าหมายการปรับเทียบในภาพ
* ใช้ `--no-reflectance` เพื่อปิดการใช้งาน

{% hint style="info" %}
**ข้อกำหนด**: ตรวจสอบให้แน่ใจว่าแผงการปรับเทียบได้รับการเปิดเผยอย่างเหมาะสมและมองเห็นได้ในภาพของคุณเพื่อการแปลงการสะท้อนแสงที่แม่นยำ
{% endhint %}

### การแก้ไข PPK

**ให้ประโยชน์อะไรบ้าง:** ใช้การแก้ไขจลนศาสตร์หลังการประมวลผลโดยใช้ข้อมูลบันทึก DAQ-A-SD เพื่อความแม่นยำของ GPS ที่ดีขึ้น

* **ปิดใช้งานโดยค่าเริ่มต้น**
* ใช้ `--ppk` เพื่อเปิดใช้งาน
* ต้องใช้ไฟล์ .daq ในโฟลเดอร์โปรเจ็กต์จากเซ็นเซอร์วัดแสง MAPIR DAQ-A-SD

### รูปแบบเอาต์พุต

<table><thead><tr><th width="197">รูปแบบ</th><th width="130.20001220703125">ความลึกบิต</th><th width="116.5999755859375">ขนาดไฟล์</th><th>ดีที่สุดสำหรับ</th></tr></thead><tbody><tr><td><strong>TIFF (16 บิต)</strong> ⭐</td><td>จำนวนเต็ม 16 บิต</td><td>ขนาดใหญ่</td><td>การวิเคราะห์ GIS, โฟโตแกรมเมทรี (แนะนำ)</td></tr><tr><td><strong>TIFF (32 บิต, เปอร์เซ็นต์)</strong></td><td>ทศนิยม 32 บิต</td><td>มาก ใหญ่</td><td>การวิเคราะห์ทางวิทยาศาสตร์ การวิจัย</td></tr><tr><td><strong>PNG (8 บิต)</strong></td><td>จำนวนเต็ม 8 บิต</td><td>ปานกลาง</td><td>การตรวจสอบด้วยภาพ การแชร์เว็บ</td></tr><tr><td><strong>JPG (8 บิต)</strong></td><td>8 บิต จำนวนเต็ม</td><td>เล็ก</td><td>ดูตัวอย่างด่วน เอาต์พุตที่ถูกบีบอัด</td></tr></tbody></table>

***

## ระบบอัตโนมัติและการเขียนสคริปต์

### การประมวลผลชุด PowerShell (Windows)

ประมวลผลโฟลเดอร์ชุดข้อมูลหลายชุดโดยอัตโนมัติบน Windows:

```powershell
# process_all_datasets.ps1

$datasets = Get-ChildItem "C:\Datasets\2025" -Directory

foreach ($dataset in $datasets) {
    Write-Host "Processing $($dataset.Name)..." -ForegroundColor Cyan
    
    chloros-cli process $dataset.FullName `
        --vignette `
        --reflectance
    
    if ($LASTEXITCODE -eq 0) {
        Write-Host "✓ $($dataset.Name) complete" -ForegroundColor Green
    } else {
        Write-Host "✗ $($dataset.Name) failed" -ForegroundColor Red
    }
}

Write-Host "All datasets processed!" -ForegroundColor Green
```

### Windows ชุดสคริปต์ (Windows)

การวนซ้ำอย่างง่ายสำหรับการประมวลผลแบบแบตช์บน Windows:

```batch
@echo off
echo Starting batch processing...

for /d %%i in (C:\Datasets\2025\*) do (
    echo.
    echo ========================================
    echo Processing: %%i
    echo ========================================
    chloros-cli process "%%i"
    
    if %ERRORLEVEL% EQU 0 (
        echo SUCCESS: %%i processed
    ) else (
        echo ERROR: %%i failed
    )
)

echo.
echo All datasets processed!
pause
```

### การประมวลผลแบตช์ Bash (Linux)

ประมวลผลโฟลเดอร์ชุดข้อมูลหลายชุดบน Linux:

```bash
#!/bin/bash
# process_all_datasets.sh

for dataset in ~/datasets/2026/*/; do
    name=$(basename "$dataset")
    echo "Processing $name..."

    chloros-cli process "$dataset" \
        --vignette \
        --reflectance

    if [ $? -eq 0 ]; then
        echo "✓ $name complete"
    else
        echo "✗ $name failed"
    fi
done

echo "All datasets processed!"
```

### สคริปต์การทำงานอัตโนมัติ Python (ข้ามแพลตฟอร์ม)

ระบบอัตโนมัติขั้นสูงพร้อมการจัดการข้อผิดพลาด (ใช้ได้กับ Windows และ Linux):

```python
import subprocess
import os
import sys
from pathlib import Path
from datetime import datetime

def process_dataset(input_folder):
    """Process a folder using Chloros CLI"""
    cmd = ['chloros-cli', 'process', str(input_folder)]
    
    # Execute command
    result = subprocess.run(
        cmd, 
        capture_output=True, 
        text=True,
        encoding='utf-8'
    )
    
    return result.returncode == 0, result.stdout, result.stderr

def main():
    """Process all datasets in a directory"""
    # Adjust path for your platform
    # Windows: Path('C:/Datasets/2025')
    # Linux:   Path.home() / 'datasets' / '2025'
    datasets_dir = Path('C:/Datasets/2025')
    log_file = Path('processing_log.txt')
    
    successful = []
    failed = []
    
    # Start processing
    print(f"Starting batch processing: {datetime.now()}")
    print(f"Scanning: {datasets_dir}")
    print("=" * 60)
    
    for dataset_folder in sorted(datasets_dir.iterdir()):
        if not dataset_folder.is_dir():
            continue
        
        print(f"\nProcessing: {dataset_folder.name}")
        
        success, stdout, stderr = process_dataset(dataset_folder)
        
        if success:
            print(f"✓ {dataset_folder.name} - SUCCESS")
            successful.append(dataset_folder.name)
        else:
            print(f"✗ {dataset_folder.name} - FAILED")
            failed.append(dataset_folder.name)
            
            # Log error details
            with open(log_file, 'a', encoding='utf-8') as f:
                f.write(f"\n=== {dataset_folder.name} - {datetime.now()} ===\n")
                f.write(f"STDOUT:\n{stdout}\n")
                f.write(f"STDERR:\n{stderr}\n")
    
    # Print summary
    print("\n" + "=" * 60)
    print(f"SUMMARY - Completed: {datetime.now()}")
    print(f"  Successful: {len(successful)}")
    print(f"  Failed: {len(failed)}")
    
    if failed:
        print(f"\nFailed folders:")
        for folder in failed:
            print(f"  - {folder}")
        print(f"\nCheck {log_file} for error details")
        sys.exit(1)
    else:
        print("\nAll datasets processed successfully!")
        sys.exit(0)

if __name__ == '__main__':
    main()
```

***

## ขั้นตอนการประมวลผล

### ขั้นตอนการทำงานมาตรฐาน

1. **อินพุต**: โฟลเดอร์ที่มีคู่ภาพ RAW/JPG
2. **การค้นพบ**: CLI สแกนอัตโนมัติสำหรับไฟล์ภาพที่รองรับ
3. **การประมวลผล**: โหมดขนานจะปรับขนาดตามคอร์ CPU ของคุณ (Chloros+)
4. **เอาต์พุต**: สร้างโฟลเดอร์ย่อยของรุ่นกล้องที่มีภาพที่ประมวลผลแล้ว

### ตัวอย่างโครงสร้างผลลัพธ์

```

MyProject/
├── project.json                             # Project metadata
├── 2025_0203_193056_008.JPG                # Original JPG
├── 2025_0203_193055_007.RAW                # Original RAW
└── Survey3N_RGN/                           # Processed outputs ✓
    ├── 2025_0203_193056_008_Reflectance.tif   # Calibrated reflectance
    ├── 2025_0203_193056_008_Target.tif        # Target detection
    └── ...
```

### การประมาณเวลาดำเนินการ

เวลาในการประมวลผลโดยทั่วไปสำหรับ 100 ภาพ (แต่ละภาพ 12MP):

| แพลตฟอร์ม | โหมด | เวลาโดยประมาณ | หมายเหตุ |
| --- | --- | --- | --- |
| **เดสก์ท็อป 12GB+ GPU** | `GPU_PARALLEL` | 5-10 นาที | ตัวเลือกที่เร็วที่สุด |
| **เดสก์ท็อป 8GB GPU** | `GPU_SINGLE` | 10-15 นาที | ผลงานดี |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 15-25 นาที | การคำนวณขอบ |
| **เจ็ตสัน นาโน 8GB** | `GPU_SINGLE` | 30-60 นาที | หน่วยความจำจำกัด |
| **ซีพียูเท่านั้น** | `CPU_PARALLEL` | 20-40 นาที | ไม่ต้องใช้ GPU |

{% hint style="info" %}
**เคล็ดลับด้านประสิทธิภาพ**: เวลาในการประมวลผลจะแตกต่างกันไปขึ้นอยู่กับจำนวนภาพ ความละเอียด วิธีการ debayer และฮาร์ดแวร์ การดีบาย Texture Aware ใช้เวลานานกว่ามาตรฐานอย่างมาก ดูรายละเอียดใน [การปรับการประมวลผลแบบไดนามิก](processing-architecture/dynamic-compute-adaptation.md)
{% endhint %}

***

## การแก้ไขปัญหา

### ไม่พบ CLI

**ข้อผิดพลาด Windows:**

```
'chloros-cli' is not recognized as an internal or external command
```

**โซลูชั่น Windows:**

1. ตรวจสอบตำแหน่งการติดตั้ง:

```powershell
dir "C:\Program Files\Chloros\resources\cli\chloros-cli.exe"
```

2. ใช้เส้นทางแบบเต็มหากไม่ได้อยู่ใน PATH:

```powershell
"C:\Program Files\Chloros\resources\cli\chloros-cli.exe" process "C:\Datasets\Field_A"
```

3. เพิ่มไปยัง PATH ด้วยตนเอง:
   * คุณสมบัติระบบเปิด → ตัวแปรสภาพแวดล้อม
   * แก้ไขตัวแปร PATH
   * เพิ่ม: `C:\Program Files\Chloros\resources\cli`
   * รีสตาร์ทเทอร์มินัล

**ข้อผิดพลาด Linux:**

```
chloros-cli: command not found
```

**โซลูชั่น Linux:**

1. ตรวจสอบการติดตั้ง:

```bash
which chloros-cli
dpkg -L chloros-amd64  # or chloros-arm64-jp6
```

2. โหลดเชลล์ของคุณใหม่:

```bash
source ~/.bashrc
```

3. ตรวจสอบสิทธิ์:

```bash
sudo chmod +x /usr/bin/chloros-cli
```

***

### แบ็กเอนด์ไม่สามารถเริ่มต้นได้**ข้อผิดพลาด:**

```

Backend failed to start within 30 seconds
```

**แนวทางแก้ไข:**

1. ตรวจสอบว่าแบ็กเอนด์ทำงานอยู่แล้วหรือไม่ (ปิดก่อน)
2. ตรวจสอบว่าไฟร์วอลล์ไม่ได้บล็อก (Windows) หรือตรวจสอบความพร้อมใช้งานของพอร์ต (Linux: `lsof -i :5000`)
3. ลองใช้พอร์ตอื่น:

```bash
# Windows
chloros-cli --port 5001 process "C:\Datasets\Field_A"

# Linux
chloros-cli --port 5001 process ~/datasets/field_a
```

4. บังคับให้รีสตาร์ทแบ็กเอนด์:

```bash
# Windows
chloros-cli --restart process "C:\Datasets\Field_A"

# Linux
chloros-cli --restart process ~/datasets/field_a
```

5. บน Linux มีไฟล์ปฏิบัติการแบ็คเอนด์ตรวจสอบอยู่:

```bash
ls -la /usr/lib/chloros/chloros-backend
```

***

### ปัญหาใบอนุญาต / การรับรองความถูกต้อง**ข้อผิดพลาด:**

```

Chloros+ license required for CLI access
```

**แนวทางแก้ไข:**

1. ตรวจสอบว่าคุณมีการสมัครสมาชิก Chloros+ ที่ใช้งานอยู่
2. เข้าสู่ระบบด้วยข้อมูลประจำตัวของคุณ:

```bash
chloros-cli login user@example.com 'password'
```

3. ตรวจสอบสถานะใบอนุญาต:

```bash
chloros-cli status
```

4. ติดต่อฝ่ายสนับสนุน: info@mapir.camera

***

### ไม่พบรูปภาพ**ข้อผิดพลาด:**

```

No images found in the specified folder
```

**แนวทางแก้ไข:**

1. ตรวจสอบโฟลเดอร์ที่มีรูปแบบที่รองรับ (.RAW, .TIF, .JPG)
2. ตรวจสอบเส้นทางโฟลเดอร์ให้ถูกต้อง (ใช้เครื่องหมายคำพูดสำหรับเส้นทางที่มีช่องว่าง)
3. ตรวจสอบให้แน่ใจว่าคุณมีสิทธิ์ในการอ่านโฟลเดอร์
4. ตรวจสอบนามสกุลไฟล์ให้ถูกต้อง

***

### กำลังดำเนินการแผงลอยหรือแขวน**แนวทางแก้ไข:**

1. ตรวจสอบพื้นที่ว่างในดิสก์ (ตรวจสอบให้แน่ใจว่าเพียงพอสำหรับเอาต์พุต)
2. ปิดแอปพลิเคชั่นอื่นๆ เพื่อเพิ่มหน่วยความจำ
3. ลดจำนวนภาพ (ดำเนินการเป็นชุด)

***

### พอร์ตมีการใช้งานแล้ว**ข้อผิดพลาด:**

```

Port 5000 is already in use
```

**แนวทางแก้ไข:**

**Windows:**

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

**Linux:**

```bash
# Find what's using port 5000
lsof -i :5000

# Use a different port
chloros-cli --port 5001 process ~/datasets/field_a
```

***

## คำถามที่พบบ่อย

### ถาม: ฉันจำเป็นต้องมีใบอนุญาตสำหรับ CLI หรือไม่

**ก. ใช่! CLI ต้องมีใบอนุญาต**Chloros+** แบบชำระเงิน

* ❌ แผนมาตรฐาน (ฟรี): CLI ปิดใช้งาน
* ✅ Chloros+ (ชำระเงิน) แผน: CLI เปิดใช้งานโดยสมบูรณ์

สมัครสมาชิกได้ที่: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

### ถาม: ฉันสามารถใช้ CLI บนเซิร์ฟเวอร์ที่ไม่มี GUI ได้หรือไม่?**ก. ใช่! CLI ทำงานแบบไม่มีหัวโดยสมบูรณ์ นี่เป็นกรณีการใช้งานหลักของ Linux**Windows เซิร์ฟเวอร์:**
* Windows เซิร์ฟเวอร์ 2016 หรือใหม่กว่า
* ติดตั้ง Visual C++ Redistributable แล้ว

**เซิร์ฟเวอร์ Linux:**
* Ubuntu 20.04+ / Debian 11+ (amd64) หรือ JetPack 6 (arm64)
* ติดตั้งผ่านแพ็คเกจ `.deb`

**ทั้งสองแพลตฟอร์ม:**
* RAM ขั้นต่ำ 8GB (แนะนำ 16GB)
* การเปิดใช้งานใบอนุญาตครั้งเดียว: `chloros-cli login user@example.com 'password'`

***

### ถาม: ภาพที่ประมวลผลแล้วจะถูกบันทึกไว้ที่ไหน?**A:**ตามค่าเริ่มต้น ภาพที่ประมวลผลจะถูกบันทึกใน**โฟลเดอร์เดียวกันกับอินพุต** ในโฟลเดอร์ย่อยของรุ่นกล้อง (เช่น `Survey3N_RGN/`)

ใช้ตัวเลือก `-o` เพื่อระบุโฟลเดอร์เอาต์พุตอื่น:

```bash
# Windows
chloros-cli process "C:\Input" -o "D:\Output"

# Linux
chloros-cli process ~/input -o ~/output
```

***

### ถาม: ฉันสามารถประมวลผลหลายโฟลเดอร์พร้อมกันได้หรือไม่**ตอบ:** ไม่ใช่คำสั่งเดียวโดยตรง แต่คุณสามารถใช้สคริปต์เพื่อประมวลผลโฟลเดอร์ตามลำดับได้ ดูส่วน [การทำงานอัตโนมัติและการเขียนสคริปต์](CLI.md#automation--scripting)***

### ถาม: ฉันจะบันทึกเอาต์พุต CLI ลงในไฟล์บันทึกได้อย่างไร**พาวเวอร์เชลล์:**

```powershell
chloros-cli process "C:\Datasets\Field_A" | Tee-Object -FilePath "processing.log"
```

**ชุด:**

```batch
chloros-cli process "C:\Datasets\Field_A" > processing.log 2>&1
```

**Linux ทุบตี:**

```bash
chloros-cli process ~/datasets/field_a 2>&1 | tee processing.log
```

***

### ถาม: จะเกิดอะไรขึ้นถ้าฉันกด Ctrl+C ในระหว่างการประมวลผล?**ตอบ:** CLI จะ:

1. หยุดการประมวลผลอย่างสง่างาม
2. ปิดแบ็กเอนด์
3. ออกด้วยรหัส 130

รูปภาพที่ประมวลผลบางส่วนอาจยังคงอยู่ในโฟลเดอร์เอาท์พุต

***

### ถาม: ฉันสามารถทำให้การประมวลผล CLI เป็นแบบอัตโนมัติได้หรือไม่**ก:** แน่นอน! CLI ได้รับการออกแบบมาเพื่อระบบอัตโนมัติ ดูตัวอย่าง [การทำงานอัตโนมัติและการเขียนสคริปต์](CLI.md#automation--scripting) สำหรับ PowerShell (Windows), Batch (Windows), Bash (Linux) และ Python (ข้ามแพลตฟอร์ม)***

### ถาม: ฉันจะตรวจสอบเวอร์ชัน CLI ได้อย่างไร**ก:**

```bash
chloros-cli --version
```

**ผลลัพธ์:**

```

Chloros CLI 1.1.0
```

***

## การขอความช่วยเหลือ

### วิธีใช้บรรทัดคำสั่ง

ดูข้อมูลวิธีใช้โดยตรงใน CLI:

```bash
# General help
chloros-cli --help

# Command-specific help
chloros-cli process --help
chloros-cli login --help
chloros-cli language --help
```

### ช่องทางการสนับสนุน

* **อีเมล**: info@mapir.camera
* **เว็บไซต์**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **ราคา**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)***

## ตัวอย่างที่สมบูรณ์

### ตัวอย่างที่ 1: การประมวลผลขั้นพื้นฐาน

กระบวนการด้วยการตั้งค่าเริ่มต้น (บทความสั้น การสะท้อนแสง):

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A_2025_01_15"
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a_2025_01_15
```

***

### ตัวอย่างที่ 2: ผลลัพธ์ทางวิทยาศาสตร์คุณภาพสูง

โฟลต 32 บิต TIFF:

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "TIFF (32-bit, Percent)" ^
  --vignette ^
  --reflectance
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a \
  --format "TIFF (32-bit, Percent)" \
  --vignette \
  --reflectance
```

***

### ตัวอย่างที่ 3: การประมวลผลการแสดงตัวอย่างอย่างรวดเร็ว

PNG 8 บิตที่ไม่มีการสอบเทียบเพื่อการตรวจสอบอย่างรวดเร็ว:

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "PNG (8-bit)" ^
  --no-vignette ^
  --no-reflectance
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a \
  --format "PNG (8-bit)" \
  --no-vignette \
  --no-reflectance
```

***

### ตัวอย่างที่ 4: การประมวลผล PPK-Corrected

ใช้การแก้ไข PPK พร้อมการสะท้อนแสง:

**Windows:**

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --ppk ^
  --reflectance
```

**Linux:**

```bash
chloros-cli process ~/datasets/field_a \
  --ppk \
  --reflectance
```

***

### ตัวอย่างที่ 5: ตำแหน่งเอาต์พุตแบบกำหนดเอง

ดำเนินการไปยังสถานที่อื่นด้วยรูปแบบเฉพาะ:

**Windows:**

```powershell
chloros-cli process "C:\Input\Raw_Images" ^
  -o "D:\Output\Processed" ^
  --format "TIFF (16-bit)"
```

**Linux:**

```bash
chloros-cli process ~/input/raw_images \
  -o ~/output/processed \
  --format "TIFF (16-bit)"
```

***

### ตัวอย่างที่ 6: ขั้นตอนการตรวจสอบสิทธิ์

ขั้นตอนการรับรองความถูกต้องเสร็จสมบูรณ์ (เหมือนกันบนทุกแพลตฟอร์ม):

```bash
# Step 1: Login
chloros-cli login user@example.com 'MyP@ssw0rd'

# Step 2: Verify status
chloros-cli status

# Step 3: Process images
# Windows: chloros-cli process "C:\Datasets\Field_A"
# Linux:   chloros-cli process ~/datasets/field_a
chloros-cli process ~/datasets/field_a

# Step 4: Logout (optional, when switching accounts)
chloros-cli logout
```

***

### ตัวอย่างที่ 7: การใช้งานหลายภาษา

เปลี่ยนภาษาอินเทอร์เฟซ (เหมือนกันในทุกแพลตฟอร์ม):

```bash
# List available languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Process with Spanish interface
# Windows: chloros-cli process "C:\Vuelos\Campo_A"
# Linux:   chloros-cli process ~/vuelos/campo_a
chloros-cli process ~/vuelos/campo_a

# Change back to English
chloros-cli language en
```