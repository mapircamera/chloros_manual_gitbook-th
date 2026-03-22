# Linux การติดตั้ง

Chloros ได้รับการแจกจ่ายสำหรับ Linux เป็นแพ็คเกจ `.deb` ที่ติดตั้ง CLI และแบ็กเอนด์ Python SDK ได้รับการติดตั้งแยกต่างหากผ่านทาง pip

***

## Linux amd64 (x86_64)

### ความต้องการของระบบ

| ข้อกำหนด | ขั้นต่ำ | แนะนำ |
| --- | --- | --- |
| **การจัดจำหน่าย** | อูบุนตู 20.04+ / เดเบียน 11+ | อูบุนตู 22.04+ |
| **โปรเซสเซอร์** | x86_64 (อินเทล/เอเอ็มดี) | Intel Core i7 หรือดีกว่า |
| **หน่วยความจำ (RAM)** | 8GB | 16GB หรือมากกว่า |
| **กราฟิกการ์ด** | ไม่มี (การประมวลผล CPU) | NVIDIA GPU พร้อม 4GB+ VRAM |
| **การจัดเก็บ** | พื้นที่ว่าง 2GB | SSD พร้อมพื้นที่ว่าง 10GB+ |
| **Python** | Python 3.7+ (สำหรับ SDK) | Python 3.10+ |

### การติดตั้ง

ดาวน์โหลดแพ็คเกจ `.deb` และติดตั้ง:

```bash
sudo dpkg -i chloros-amd64.deb
```

ตรวจสอบการติดตั้ง:

```bash
chloros-cli --version
```

***

## Linux arm64 (NVIDIA Jetson)

### ความต้องการของระบบ

| ข้อกำหนด | ขั้นต่ำ | แนะนำ |
| --- | --- | --- |
| **แพลตฟอร์ม** | NVIDIA Jetson พร้อม JetPack 6 | Jetson Orin NX 16GB หรือ AGX Orin |
| **เจ็ทแพ็ค** | JetPack 6.x | JetPack 6 ล่าสุด |
| **หน่วยความจำ (RAM)** | 8GB (GPU/CPU ที่ใช้ร่วมกัน) | 16GB+ แชร์ |
| **การจัดเก็บ** | พื้นที่ว่าง 2GB | NVMe SSD พร้อม 10GB+ ฟรี |
| **Python** | Python 3.7+ (สำหรับ SDK) | Python 3.10+ |

### การติดตั้ง

ดาวน์โหลดแพ็คเกจ JetPack 6 `.deb` และติดตั้ง:

```bash
sudo dpkg -i chloros-arm64-jp6.deb
```

ตรวจสอบการติดตั้ง:

```bash
chloros-cli --version
```

สำหรับการตั้งค่า Jetson โดยละเอียด รวมถึงการจัดการระบายความร้อนและการปรับใช้ภาคสนาม โปรดดู [NVIDIA Jetson Guide](nvidia-jetson-guide.md)

***

## Python การติดตั้ง SDK (Linux ทั้งหมด)

Python SDK ได้รับการติดตั้งแยกกันผ่าน pip และทำงานได้ทั้งบน amd64 และ arm64:

```bash
pip install chloros-sdk
```

หากต้องการรวมการสนับสนุนการสตรีมความคืบหน้าเพิ่มเติม:

```bash
pip install chloros-sdk[progress]
```

ตรวจสอบ SDK:

```bash
python -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
แพคเกจ `.deb` จะติดตั้ง Chloros CLI และแบ็กเอนด์ Python SDK เป็นแพ็คเกจ pip แยกต่างหากที่สื่อสารกับแบ็คเอนด์ผ่าน HTTP ภายในเครื่อง API
{% endhint %}

***

## ไดเรกทอรีการกำหนดค่า

Chloros บน Linux เป็นไปตาม [XDG Base Directory Specification](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html):

| วัตถุประสงค์ | XPROTX000076เส้นทาง XPROTX | Windows เทียบเท่า |
| --- | --- | --- |
| **การกำหนดค่า** | `~/.config/chloros/` | `%APPDATA%\Chloros\` |
| **ข้อมูล / โครงการ** | `~/.local/share/chloros/` | `%LOCALAPPDATA%\Chloros\` |
| **แคช / ข้อมูลประจำตัว** | `~/.cache/chloros/` | `%APPDATA%\Chloros\cache\` |

## ตำแหน่งที่ดำเนินการแบ็กเอนด์

แพ็คเกจ `.deb` จะติดตั้งแบ็คเอนด์ในตำแหน่งมาตรฐาน CLI และ SDK ตรวจหาเส้นทางแบ็กเอนด์โดยอัตโนมัติ:

| วิธีการติดตั้ง | เส้นทางแบ็กเอนด์ |
| --- | --- |
| `.deb` แพ็คเกจ | `/usr/lib/chloros/chloros-backend` |
| กำหนดเอง / กำหนดเอง | `/opt/mapir/chloros/backend/chloros-backend` |

คุณสามารถแทนที่เส้นทางแบ็คเอนด์ด้วยแฟล็ก `--backend-exe` CLI หรือพารามิเตอร์ตัวสร้าง `backend_exe` SDK

***

## การตั้งค่าครั้งแรก

### 1. เปิดใช้งานใบอนุญาตของคุณ

จำเป็นต้องมีใบอนุญาต Chloros+ สำหรับการเข้าถึง CLI และ SDK:

```bash
chloros-cli login your@email.com 'your-password'
```

### 2. ตรวจสอบสถานะใบอนุญาตของคุณ

```bash
chloros-cli status
```

### 3. ประมวลผลชุดข้อมูลแรกของคุณ

```bash
chloros-cli process ~/datasets/flight001
```

### 4. เรียกใช้การวินิจฉัยระบบ

ตรวจสอบว่าระบบของคุณได้รับการกำหนดค่าอย่างถูกต้อง:

```bash
chloros-cli selftest
```

ซึ่งดำเนินการตรวจสอบวินิจฉัย 7 รายการ รวมถึงเวอร์ชัน การเริ่มต้นแบ็คเอนด์ การเชื่อมต่อ API และความพร้อมใช้งานของ CUDA/GPU

***

## ตัวอย่างการเขียนสคริปต์ Bash

### ประมวลผลชุดข้อมูลหลายชุด

```bash
#!/bin/bash
for dataset in ~/datasets/2026/*/; do
    echo "Processing $(basename "$dataset")..."
    chloros-cli process "$dataset" --format tiff-32
    echo "Done: $(basename "$dataset")"
done
```

### กระบวนการด้วยการตั้งค่าแบบกำหนดเอง

```bash
#!/bin/bash
chloros-cli process ~/datasets/field_a \
    --output ~/output/field_a \
    --format tiff-32 \
    --indices NDVI NDRE GNDVI \
    --debayer texture-aware \
    --no-vignette
```

### การประมวลผลอัตโนมัติด้วย Cron

เพิ่มไปยัง crontab ของคุณ (`crontab -e`) เพื่อประมวลผลชุดข้อมูลใหม่โดยอัตโนมัติ:

```cron
# Process any new datasets at 2 AM daily
0 2 * ** /usr/bin/chloros-cli process /data/incoming --output /data/processed >> /var/log/chloros.log 2>&1
```

### Python SDK ตัวอย่าง

```python
from chloros_sdk import process_folder

# One-line processing
result = process_folder(
    "/home/user/datasets/flight001",
    indices=["NDVI", "NDRE"],
    export_format="TIFF (32-bit, Percent)"
)
```

***

## การแก้ไขปัญหา

### CLI ไม่พบหลังการติดตั้ง

หากไม่พบ `chloros-cli` หลังจากติดตั้งแพ็คเกจ `.deb`:

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# If not in PATH, check the installation
dpkg -L chloros-amd64  # or chloros-arm64-jp6

# Reload your shell
source ~/.bashrc
```

### การอนุญาตถูกปฏิเสธ

```bash
# Ensure the binary is executable
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend
```

### แบ็กเอนด์ไม่สามารถเริ่มต้นได้

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

### ตรวจไม่พบ CUDA

```bash
# Check NVIDIA driver installation
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check JetPack version
cat /etc/nv_tegra_release
```

### ไลบรารีที่ใช้ร่วมกันหายไป

```bash
# Install common dependencies
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries
ldd /usr/lib/chloros/chloros-backend | grep "not found"
```

***

## กำลังอัปเดต Chloros บน Linux

ใช้คำสั่งอัพเดตในตัวเพื่อตรวจสอบและติดตั้งการอัปเดต:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

***

## ขั้นตอนต่อไป

* [NVIDIA Jetson Guide](nvidia-jetson-guide.md) — การเพิ่มประสิทธิภาพและการปรับใช้เฉพาะ Jetson
* [CLI : Command Line](../CLI.md) — การอ้างอิงคำสั่ง CLI แบบเต็ม
* [API : Python SDK](../api-python-sdk.md) — การอ้างอิง SDK แบบเต็ม
* [Dynamic Compute Adaptation](../processing-architecture/dynamic-compute-adaptation.md) — Chloros ปรับให้เข้ากับฮาร์ดแวร์ของคุณอย่างไร