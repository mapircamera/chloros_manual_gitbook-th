# คู่มือ NVIDIA Jetson

Chloros บน NVIDIA Jetson ช่วยให้สามารถประมวลผลภาพแบบหลายสเปกตรัมที่ขอบ — ในภาคสนาม บน UAV และในการติดตั้งระยะไกล Chloros ตรวจจับโมเดล Jetson ของคุณโดยอัตโนมัติและปรับกลยุทธ์การประมวลผลให้เหมาะสมสำหรับฮาร์ดแวร์ของคุณ

***

## รุ่น Jetson ที่รองรับ

| รุ่น | แรม | กลยุทธ์การประมวลผล | แนะนำให้ใช้ |
| -------------------- | -------------- | ------------------------------------------------------------- | ------------------------------------------------------------ |
| **เจ็ตสัน AGX โอริน** | แชร์ 32-64GB | `GPU_PARALLEL` (4 คน) | ประสิทธิภาพสูงสุด ชุดข้อมูลขนาดใหญ่ |
| **เจ็ตสัน โอริน NX** | แชร์ 8-16GB | `GPU_PARALLEL` (3 คน, 16GB) / `GPU_SINGLE` (8GB) | คำแนะนำเบื้องต้นสำหรับการวางกำลังทางอากาศและภาคสนาม |
| **เจ็ตสัน โอริน นาโน** | แชร์ 8GB | `GPU_SINGLE` (คนงาน 1 คน) | Edge Compute ระดับเริ่มต้น |
| **เจ็ตสัน นาโน** | แชร์ 4-8GB | `GPU_SINGLE` (คนงาน 1 คน) | ระดับเริ่มต้น ที่จำกัดหน่วยความจำ |

{% hint style="info" %}
**รุ่น Jetson รุ่นเก่า** (TX2, TX1, Xavier NX) อาจไม่รองรับ ประสิทธิภาพจะแตกต่างกันไปขึ้นอยู่กับหน่วยความจำ GPU ที่มีอยู่และความสามารถของ CUDA
{% endhint %}

***

## ความต้องการ

* **JetPack 6.x** (แนะนำล่าสุด)
* **NVIDIA CUDA** (รวมอยู่ใน JetPack)
* **ใบอนุญาต Chloros+** (จำเป็นสำหรับการเข้าถึง CLI/SDK)

## การติดตั้ง

```bash
# Install the JetPack 6 .deb package
sudo dpkg -i chloros-arm64-jp6.deb

# Verify installation
chloros-cli --version

# Install Python SDK (optional)
pip install chloros-sdk

# Run system diagnostics
chloros-cli selftest
```

สำหรับรายละเอียดการติดตั้ง Linux ทั่วไป โปรดดูที่ [การติดตั้ง Linux](linux-installation.md)

***

## การปรับการคำนวณแบบไดนามิกบน Jetson

Chloros ตรวจจับรุ่น Jetson ของคุณโดยอัตโนมัติและเลือกกลยุทธ์การประมวลผลที่เหมาะสมที่สุด **ไม่จำเป็นต้องปรับจูนด้วยตนเอง**

### มันทำงานอย่างไร

เมื่อเริ่มต้น Chloros จะสร้างโปรไฟล์ระบบของคุณ:

1. **ตรวจจับรุ่น Jetson** ผ่าน `/proc/device-tree/model`
2. **อ่าน GPU/หน่วยความจำที่ใช้ร่วมกันที่มีอยู่**

3.**เลือกกลยุทธ์การประมวลผล** (`GPU_PARALLEL`, `GPU_SINGLE` หรือ `CPU_PARALLEL`)
4. **ตั้งค่าจำนวนผู้ปฏิบัติงาน ประเภทไปป์ไลน์ และการจัดสรรหน่วยความจำ** โดยอัตโนมัติ

### พฤติกรรมต่อรุ่น

| เจ็ตสันโมเดล | กลยุทธ์ | คนงาน | ไปป์ไลน์ | เห็นพ้องต้องกัน |
| ------------------------------- | -------------- | ------- | ---------------------------------- | ----------- |
| **เจ็ตสัน นาโน 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` (ประหยัดหน่วยความจำ) | ต่อเนื่องกัน |
| **Jetson Orin Nano 8GB** | `GPU_SINGLE` | 1 | `tiled_gpu` | ต่อเนื่องกัน |
| **Jetson Orin NX 8GB** | `GPU_SINGLE` | 2 | `tiled_gpu` | ต่อเนื่องกัน |
| **Jetson Orin NX 16GB** | `GPU_PARALLEL` | 3 | `fused_gpu` (เส้นทาง GPU แบบเต็ม) | พร้อมกัน |
| **Jetson AGX โอริน 32-64GB** | `GPU_PARALLEL` | 4 | `fused_gpu` | พร้อมกัน |

{% hint style="success" %}
**Jetson Orin NX 16GB** คือจุดที่น่าสนใจสำหรับการใช้งาน Edge โดยได้รับกลยุทธ์ `GPU_PARALLEL` พร้อมด้วยผู้ปฏิบัติงานที่ทำงานพร้อมกัน 3 คน มอบการประมวลผล GPU แบบขนานที่แท้จริงในรูปแบบขนาดกะทัดรัด
{% endhint %}

ความแตกต่างที่สำคัญระหว่างแพลตฟอร์มคือ **หน่วยความจำ** Jetson Nano ที่มีหน่วยความจำที่ใช้ร่วมกันขนาด 8GB จะต้องประมวลผลภาพทีละภาพโดยใช้วิธีเรียงต่อกันที่มีประสิทธิภาพหน่วยความจำ ในขณะที่ Orin NX ที่มีขนาด 16GB สามารถเรียกใช้ภาพ 3 ภาพผ่าน GPU พร้อมกันโดยใช้ไปป์ไลน์หลอมรวมที่มีปริมาณงานสูงกว่า

สำหรับการอ้างอิงการปรับการประมวลผลแบบสมบูรณ์ โปรดดู [การปรับการประมวลผลแบบไดนามิก](../processing-architecture/dynamic-compute-adaptation.md)

***

## การจัดการความร้อน

อุปกรณ์ Jetson มีพื้นที่ระบายความร้อนที่จำกัด โดยเฉพาะอย่างยิ่งในการใช้งานแบบปิดหรือทางอากาศ Chloros มีการตรวจสอบความร้อนและการควบคุมปริมาณอัตโนมัติ:

| อุณหภูมิ | การกระทำ |
| ------------------- | ------------------------------------------------- |
| **< 70°C** | การทำงานปกติ — ความเร็วในการประมวลผลเต็ม |
| **70°C** (คำเตือน) | ลดขนาดแบทช์โดยอัตโนมัติ |
| **80°C** (วิกฤต) | การควบคุมปริมาณเชิงรุก — การทำงานพร้อมกันที่ต่ำกว่า |
| **90°C** (ปิดเครื่อง) | หยุดการประมวลผล GPU ทั้งหมด — ต้องระบายความร้อน |

{% hint style="warning" %}
**ตรวจสอบให้แน่ใจว่ามีการระบายอากาศและระบายความร้อนเพียงพอ** สำหรับการประมวลผลที่ยั่งยืน โดยเฉพาะอย่างยิ่งในสนามปิดหรือระบบทางอากาศ การควบคุมปริมาณความร้อนจะลดปริมาณการประมวลผลเพื่อปกป้องฮาร์ดแวร์
{% endhint %}

***

## การจัดการหน่วยความจำ

อุปกรณ์ Jetson ใช้ **หน่วยความจำแบบรวม** — GPU และ CPU ใช้ RAM จริงร่วมกัน ซึ่งหมายความว่า VRAM ที่รายงาน (เช่น 15.3GB บน Orin NX 16GB) ไม่ใช่หน่วยความจำ GPU โดยเฉพาะ มันถูกแชร์กับระบบปฏิบัติการและกระบวนการอื่นๆ

### คำแนะนำการแลกเปลี่ยน

สำหรับชุดข้อมูลขนาดใหญ่หรือการประมวลผล Debayer ของ Texture Aware Chloros อาจแนะนำให้สร้างพื้นที่สว็อป:

```bash
# Check current memory and swap
free -h

# Create a swap file (example: 8GB)
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make persistent across reboots
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

**หน่วยความจำโดยประมาณต่อภาพ:**

* การดีบายมาตรฐาน: \~10 MB ต่อภาพ
* โปรแกรมแก้ไข Texture Aware: \~15 MB ต่อภาพ

Chloros คำนวณหน่วยความจำที่ต้องการโดยอัตโนมัติตามขนาดชุดข้อมูลของคุณ และเตือนคุณหากแนะนำให้สลับ

### ทางเลือก OOM (หน่วยความจำไม่เพียงพอ)

หากตรวจพบสภาวะหน่วยความจำไม่เพียงพอระหว่างการประมวลผล:

1. Chloros ลดจำนวนผู้ปฏิบัติงาน GPU โดยอัตโนมัติ
2. ถอยกลับจาก `fused_gpu` ไปป์ไลน์ `tiled_gpu` (มีประสิทธิภาพหน่วยความจำมากขึ้น)
3. ประมวลผลต่อด้วยปริมาณงานที่ลดลง แทนที่จะหยุดทำงาน

***

## การปรับใช้ภาคสนาม

### ข้อควรพิจารณาด้านพลังงาน

| เจ็ตสันโมเดล | การวาดกำลังโดยทั่วไป | หมายเหตุ |
| ---------------- | ------------------ | ----------------------- |
| เจ็ตสัน นาโน | 5-10W | USB-C หรือแจ็คบาร์เรล |
| เจ็ตสัน โอริน นาโน | 7-15W | แม่แรง DC บาเรล |
| เจ็ตสัน โอริน NX | 10-25W | แม่แรง DC บาเรล |
| Jetson AGX โอริน | 15-60W | USB-C PD หรือแจ็คบาร์เรล |

วางแผนงบประมาณด้านพลังงานของคุณเพื่อการประมวลผลที่ยั่งยืน — การดึงพลังงานสูงสุดจะเกิดขึ้นในระหว่างที่ Thread 3 (การประมวลผล) ที่ใช้ GPU มาก

### คำแนะนำในการจัดเก็บ

* **NVMe SSD** ขอแนะนำอย่างยิ่งสำหรับการปรับใช้ arm64
* การ์ด SD ช้าเกินไปสำหรับการประมวลผล — ใช้เป็นสื่อสำหรับบูตเท่านั้น
* วางแผนสำหรับขนาดข้อมูลภาพดิบของคุณ 2-3 เท่าสำหรับเอาต์พุตที่ประมวลผล

### การทำงานแบบไม่มีหัวผ่าน SSH

Chloros CLI เหมาะอย่างยิ่งสำหรับการใช้งาน Jetson แบบไม่มีหัว:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format tiff-32

# Monitor export progress
chloros-cli export-status
```

### การประมวลผลอัตโนมัติด้วย systemd

สร้างบริการ systemd สำหรับการประมวลผลอัตโนมัติ:

```ini
# /etc/systemd/system/chloros-process.service
[Unit]
Description=Chloros Automated Processing
After=network.target

[Service]
Type=oneshot
User=chloros
ExecStart=/usr/bin/chloros-cli process /data/incoming --output /data/processed
StandardOutput=append:/var/log/chloros-process.log
StandardError=append:/var/log/chloros-process.log

[Install]
WantedBy=multi-user.target
```

จับคู่กับตัวจับเวลา systemd สำหรับการประมวลผลตามกำหนดเวลา:

```ini
# /etc/systemd/system/chloros-process.timer
[Unit]
Description=Run Chloros Processing Every Hour

[Timer]
OnCalendar=hourly
Persistent=true

[Install]
WantedBy=timers.target
```

```bash
sudo systemctl enable chloros-process.timer
sudo systemctl start chloros-process.timer
```

***

## ตัวอย่างขั้นตอนการทำงาน

### การประมวลผล Jetson ขั้นพื้นฐาน

```bash
#!/bin/bash
# Process a drone flight dataset on Jetson
chloros-cli process /data/flights/flight_042 \
    --output /data/processed/flight_042 \
    --format tiff-32 \
    --indices NDVI NDRE GNDVI
```

### Python SDK บน Jetson

```python
from chloros_sdk import ChlorosLocal

with ChlorosLocal() as chloros:
    chloros.create_project("field_survey_042")
    chloros.import_images("/data/flights/flight_042")
    chloros.configure(
        indices=["NDVI", "NDRE", "GNDVI"],
        export_format="TIFF (32-bit, Percent)",
        reflectance_calibration=True
    )
    chloros.process(mode="parallel")

print("Processing complete!")
```

### การประมวลผลเป็นกลุ่มหลายเที่ยวบิน

```bash
#!/bin/bash
# Process all flight datasets in a directory
for flight in /data/flights/*/; do
    name=$(basename "$flight")
    echo "Processing $name..."
    chloros-cli process "$flight" \
        --output "/data/processed/$name" \
        --format tiff-32 \
        --indices NDVI NDRE
    echo "Completed $name"
done
```

***

## ระบบ Jetson ที่แนะนำสำหรับการใช้งานภาคสนาม

สำหรับการใช้งานภาคสนามและทางอากาศ ให้พิจารณาตัวเลือกบอร์ดผู้ให้บริการ Jetson Orin NX 16GB เหล่านี้:

* **ทางอากาศ/โดรน**: ระบบที่มีระดับการสั่นสะเทือน (MIL-STD) น้ำหนักเบา (ต่ำกว่า 300 กรัม) ระบบระบายความร้อนแบบพาสซีฟ
* **สนามที่ทนทาน**: เคสกันน้ำระดับ IP67/IP69K พร้อมการเชื่อมต่อกล้อง PoE GigE
* **ขั้นต่ำ/งบประมาณ**: ชุดนักพัฒนาพร้อมกล่องเสริม

ติดต่อ [MAPIR Support](https://www.mapir.camera/community/contact) เพื่อขอคำแนะนำด้านฮาร์ดแวร์เฉพาะสำหรับสถานการณ์การใช้งานของคุณ

***

## ขั้นตอนต่อไป

* [การติดตั้ง Linux](linux-installation.md) — รายละเอียดการติดตั้งทั่วไป Linux
* [การปรับการประมวลผลแบบไดนามิก](../processing-architecture/dynamic-compute-adaptation.md) — การอ้างอิงกลยุทธ์การประมวลผลแบบเต็ม
* [การประมวลผลไปป์ไลน์](../processing-architecture/processing-pipeline.md) — ทำความเข้าใจกับไปป์ไลน์ 4 เธรด
* [CLI : Command Line](../CLI.md) — การอ้างอิง CLI แบบเต็ม
* [API : Python SDK](../api-python-sdk.md) — การอ้างอิง SDK แบบเต็ม