# คู่มือ NVIDIA Jetson

Chloros

บน NVIDIA Jetson ช่วยให้สามารถประมวลผลภาพมัลติสเปกตรัมที่ขอบเครือข่าย — ในสนามจริง บน UAV และในสถานที่ติดตั้งห่างไกลChloros

1.2.0 จะตรวจจับรุ่น Jetson ของคุณเมื่อเริ่มต้นระบบ และปรับกลยุทธ์การประมวลผลให้เหมาะสมกับฮาร์ดแวร์ที่ตรวจพบ **ไม่จำเป็นต้องปรับแต่งด้วยมือ**

***

## รุ่น Jetson ที่รองรับ

| รุ่น                | RAM            | กลยุทธ์การประมวลผล                                     | การใช้งานที่แนะนำ                                          |
| -------------------- | -------------- | ------------------------------------------------------- | -------------------------------------------------------- |
| **Jetson AGX Orin**  | 32-64GB shared | `GPU_PARALLEL` (2 workers)                              | ประสิทธิภาพสูงสุด, ชุดข้อมูลขนาดใหญ่                      |
| **Jetson Orin NX**   | 8-16GB shared  | `GPU_PARALLEL` (2 workers, 16GB) / `GPU_SINGLE` (8GB)   | คำแนะนำหลักสำหรับการใช้งานบนอากาศและในสนาม |
| **Jetson Orin Nano** | 8GB shared     | `GPU_SINGLE` (1 worker, sequential)                     | ระบบประมวลผล edge ระดับเริ่มต้น                                 |

{% hint style="info" %}
แพ็กเกจ arm64 ของLinux

ต้องการ **JetPack 6** ซึ่งมีให้ใช้งานบนตระกูล Jetson Orin รุ่นเก่า (Nano, TX2, Xavier NX) ไม่สามารถรัน JetPack 6 ได้ และไม่ได้รับการสนับสนุนจากแพ็กเกจปัจจุบัน
{% endhint %}

***

## ข้อกำหนด

* **JetPack 6.x** (แนะนำให้ใช้เวอร์ชันล่าสุด)
* **NVIDIA CUDA** (มาพร้อมกับ JetPack)
* **แผนบริการChloros

+ แบบชำระเงิน** — ระดับ Copper หรือสูงกว่า (จำเป็นสำหรับการเข้าถึงCLI

/SDK

ทั้งหมด; ถูกบังคับใช้จากฝั่งเซิร์ฟเวอร์)

## การติดตั้ง

```bash
# Install the JetPack 6 .deb package
sudo dpkg -i chloros_1.2.0_arm64_jp6.deb
sudo apt-get install -f

# Verify installation
chloros-cli --version    # prints "Chloros CLI 1.2.0"

# Install Python SDK (optional) — the bundled wheel always matches this build
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl

# Run system diagnostics
chloros-cli selftest
```

สำหรับรายละเอียดการติดตั้งLinux

ทั่วไป ตำแหน่งไฟล์ และการแก้ไขปัญหา ดู [Linux

Installation](linux-installation.md)

{% hint style="info" %}
**วางโฟลเดอร์ที่แตกไฟล์ลงบนพื้นที่จัดเก็บข้อมูลที่เร็ว** ไฟล์ไบนารีที่คอมไพล์แล้วจะคลายการบีบอัดตัวเองไปยังไดเรกทอรีชั่วคราวทุกครั้งที่เริ่มต้น — ซึ่งช้ามากเมื่อใช้การ์ด SDChloros

จะใช้ `/mnt/ssd/tmp` โดยอัตโนมัติหากมีอยู่; มิฉะนั้น ให้ตั้งค่า `TMPDIR` เป็นเส้นทางบน NVMe ของคุณ (`export TMPDIR=/mnt/nvme/tmp`) ของคุณ
{% endhint %}

***

## การปรับแต่งการประมวลผลแบบไดนามิกบน Jetson

### วิธีทำงาน

เมื่อเริ่มต้นระบบChloros

จะวิเคราะห์ระบบของคุณ:

1. **ตรวจจับรุ่น Jetson** ผ่าน `/proc/device-tree/model`
2. **อ่านหน่วยความจำ GPU/CPU ที่ใช้ร่วมกันที่มีอยู่** (Jetson ใช้หน่วยความจำแบบรวม)
3. **เลือกกลยุทธ์การประมวลผล** (`GPU_PARALLEL`, `GPU_SINGLE` หรือ `CPU_PARALLEL`)
4. **ตั้งค่าจำนวน worker, ประเภท pipeline และการจัดสรรหน่วยความจำ** โดยอัตโนมัติ

การตัดสินใจนี้ขึ้นอยู่กับ **ปริมาณ RAM ที่ใช้ร่วมกันทั้งหมด** ไม่ใช่ชื่อโมเดล:

* ****RAM รวมน้อยกว่า 12GB**(Jetson ทั้งหมดที่มี 8GB): `GPU_SINGLE` พร้อม**1 worker — การประมวลผลแบบลำดับที่ตั้งใจ**เนื่องจากหน่วยความจำไม่เพียงพอสำหรับ worker ที่ทำงานพร้อมกัน จึงประมวลผลภาพทีละภาพ สำหรับ Jetson ที่มี**8GB หรือน้อยกว่า**, Thread 3 จะข้าม pool ของ worker ไปทั้งหมด และทำงานต่อภาพแต่ละภาพแบบในกระบวนการ
* **12GB หรือมากกว่า**(Orin NX 16GB, AGX Orin): หน่วยความจำแบบรวม (unified memory) ตรงตามข้อกำหนดของ `GPU_PARALLEL` แต่จำนวน worker**ถูกจำกัดไว้ที่ 2 บน Jetson** — GPU, RAM ของกระบวนการทำงาน และบริบท CUDA ของแต่ละกระบวนการทำงาน ล้วนใช้จากพูลที่แชร์ร่วมกัน ดังนั้นการเพิ่มจำนวนกระบวนการทำงานอาจทำให้มีความเสี่ยงต่อความผิดพลาดเนื่องจากขาดหน่วยความจำ

คุณสามารถแทนที่การเลือกอัตโนมัติด้วยตัวแปรสภาพแวดล้อม `CHLOROS_STRATEGY` — ดู [Dynamic Compute Adaptation](../processing-architecture/dynamic-compute-adaptation.md#manual-strategy-override).

### พฤติกรรมตามโมเดล

| โมเดล Jetson                | กลยุทธ์       | จำนวนเวิร์กเกอร์ | การดำเนินการ                                      |
| --------------------------- | -------------- | ------- | ---------------------------------------------- |
| **Jetson Orin Nano 8GB**    | `GPU_SINGLE`   | 1       | วงจรวนซ้ำแบบลำดับในกระบวนการ (`tiled_gpu` เมื่อมีความกดดันด้านหน่วยความจำ) |
| **Jetson Orin NX 8GB**      | `GPU_SINGLE`   | 1       | วงจรในกระบวนการแบบลำดับ                     |
| **Jetson Orin NX 16GB**     | `GPU_PARALLEL` | 2       | กระบวนการทำงานพร้อมกันของกระบวนการทำงาน, เส้นทาง `fused_gpu`  |
| **Jetson AGX Orin 32-64GB** | `GPU_PARALLEL` | 2       | กระบวนการทำงานพร้อมกัน, `fused_gpu` path  |

ความแตกต่างหลักระหว่างแพลตฟอร์มคือ **หน่วยความจำ** Jetson 8GB ต้องประมวลผลภาพทีละภาพโดยใช้วิธีการแบ่งเป็นไทล์ที่ประหยัดหน่วยความจำเมื่อมีความกดดันสูง ในขณะที่ Orin 16GB+ สามารถประมวลผลภาพ 2 ภาพผ่าน GPU พร้อมกันโดยใช้ท่อประมวลผลแบบรวมที่มีอัตราผ่านสูง

### งบประมาณ GPU ตามแต่ละรุ่น

แต่ละรุ่น Jetson ยังมีโปรไฟล์ฮาร์ดแวร์ที่กำหนดขีดจำกัดปริมาณทรัพยากรที่การประมวลผลในพูลร่วมสามารถใช้ได้ และปรับขนาดชุดข้อมูล (batch size):

| รุ่น | ขีดจำกัดงบประมาณ GPU | ตัวคูณขนาดชุดข้อมูล | ส่วนที่สำรองไว้สำหรับระบบ/หน้าจอ |
| --- | --- | --- | --- |
| **Jetson Orin Nano** | 70% | ×0.8 | 2.0 GB |
| **Jetson Orin NX** | 75% | ×1.0 | 3.0 GB |
| **Jetson AGX Orin** | 80% | ×1.5 | 4.0 GB |

RAM ที่ตรวจพบจะปรับโปรไฟล์: Jetson ที่รายงาน **16GB หรือมากกว่า** จะได้รับการเพิ่มตัวคูณขนาดชุดเป็น ×1.2 ขนาดชุดพื้นฐานก่อนการคูณคือ 8 ภาพ

สำหรับข้อมูลอ้างอิงเกี่ยวกับการปรับแต่งการคำนวณอย่างครบถ้วน โปรดดู [Dynamic Compute Adaptation](../processing-architecture/dynamic-compute-adaptation.md).

***

## ขีดจำกัดความถี่ GPU สำหรับ Texture Aware บน Nano และ Orin Nano

กระบวนการ debayer ของ Texture Aware ดำเนินการอนุมานเครือข่ายประสาทเทียมบน GPU ซึ่งอาจทำให้เกิด **คำเตือนกระแสเกิน**บนรุ่น Jetson ประหยัดพลังงาน (ระดับ 10-15W) เมื่อ GPU ทำงานที่ความเร็วนาฬิกาสูงสุด ก่อนการประมวลผล Texture Aware บน**Jetson Nano หรือ Orin Nano**ระบบ**Chloros

**จะตรวจสอบความถี่สูงสุดของ GPU และจำกัดความถี่ไว้ที่**510 MHz** (510000000) หากความถี่ปัจจุบันสูงกว่า:

* หากCLI

สามารถเขียนค่าความถี่ GPU ในโหนด sysfs ได้ การจำกัดความถี่จะ **ถูกนำไปใช้โดยอัตโนมัติ** และจะแสดงข้อความยืนยัน
* หากไม่สามารถ (จำเป็นต้องมีสิทธิ์ root) ระบบจะแสดงคำสั่ง `sudo` ที่ใช้เพื่อตั้งค่าจำกัดความถี่ด้วยตนเอง รอสักครู่เพื่อให้คุณอ่านคำสั่งได้ แล้วจึงดำเนินการต่อ — การประมวลผลยังคงทำงานต่อไป แต่อาจแสดงคำเตือนเกี่ยวกับกระแสเกิน

เพื่อตั้งค่าจำกัดความเร็วด้วยตัวเองก่อนการประมวลผล:

```bash
echo 510000000 | sudo tee /sys/devices/platform/bus@0/17000000.gpu/devfreq/17000000.gpu/max_freq
```

รุ่นที่มีกำลังสูง (Orin NX 25W, AGX Orin 60W) ทำงานด้วยความเร็ว GPU เต็มที่; ไม่มีการตั้งค่าจำกัดความเร็วใดๆ Standard debayer ไม่เคยตั้งค่าจำกัดความเร็วบนรุ่นใดทั้งสิ้น

{% hint style="info" %}
**Texture Aware บน Jetson จะทำงานกับภาพเพียงหนึ่งภาพต่อครั้งเท่านั้น.** แต่ละ worker จะต้องการ CUDA context ของตัวเอง (~1GB) พร้อมทั้งสำเนาของโมเดล denoiser ของตัวเอง ซึ่งหน่วยความจำแบบรวม (unified memory) ไม่สามารถรองรับได้ — ดังนั้นบน Jetson เส้นทาง Texture Aware จะถูกตรึงไว้ที่ worker เดียว โดยมีการเข้าถึง GPU แบบลำดับ (serialized) คาดการณ์ว่า Texture Aware จะช้ากว่า Standard อย่างชัดเจนบน Jetson ทุกรุ่น
{% endhint %}

***

## การจัดการความร้อน

อุปกรณ์ Jetson มีพื้นที่ความร้อนที่จำกัด โดยเฉพาะอย่างยิ่งในการติดตั้งในสภาพแวดล้อมที่ปิดหรือบนอากาศChloros

จะติดตามอุณหภูมิ SoC และปรับขนาดชุดงานให้เหมาะสมโดยอัตโนมัติ:

| อุณหภูมิ         | การดำเนินการ                                            |
| ------------------- | ------------------------------------------------- |
| **&lt; 70°C**          | การทำงานปกติ — ความเร็วการประมวลผลเต็ม          |
| **70°C** (คำเตือน)  | ขนาดชุดงานลดลงอย่างค่อยเป็นค่อยไป (100% → 50% ระหว่าง 70°C ถึง 80°C) |
| **80°C** (วิกฤต) | การลดความเร็วอย่างรุนแรง (50% → 0% ระหว่าง 80°C ถึง 90°C) |
| **90°C** (ปิดระบบ) | หยุดการประมวลผล GPU ทั้งหมด — ต้องรอให้เครื่องเย็นลง |

{% hint style="warning" %}
**ต้องมั่นใจว่ามีระบบระบายอากาศและระบายความร้อนที่เพียงพอ** เพื่อการประมวลผลอย่างต่อเนื่อง โดยเฉพาะในตู้อุปกรณ์ปิดหรือระบบที่ติดตั้งบนอากาศ การลดความเร็วเนื่องจากความร้อนจะลดปริมาณงานประมวลผลเพื่อปกป้องฮาร์ดแวร์
{% endhint %}

***

## การจัดการหน่วยความจำ

อุปกรณ์ Jetson ใช้ **หน่วยความจำแบบรวม** — GPU และ CPU ใช้ RAM ทางกายภาพเดียวกัน ความจำ VRAM ที่รายงาน (เช่น ~15.3GB บน Orin NX 16GB) ไม่ใช่ความจำ GPU ที่จัดสรรไว้โดยเฉพาะ แต่เป็น RAM เดียวกันที่ระบบปฏิบัติการและกระบวนการอื่น ๆ ทั้งหมดกำลังใช้อยู่

### คำเตือนและคำแนะนำเกี่ยวกับ Swap

ก่อนการประมวลผลบน Jetson CLI จะนับจำนวนภาพ RAW ในโฟลเดอร์อินพุตของคุณ (`.tif`, `.tiff`, `.raw`, `.dng` — ภาพตัวอย่าง JPG ไม่ถูกนับ), ประมาณการปริมาณหน่วยความจำสูงสุดที่การประมวลผลต้องการ และ **แจ้งเตือนก่อนเริ่ม** หาก RAM + swap มีแนวโน้มไม่เพียงพอ คำเตือนนี้มีหัวข้อว่า `LOW MEMORY WARNING - Jetson Detected`, แสดงจำนวนภาพ RAM พื้นที่ swap ปัจจุบัน และปริมาณสูงสุดที่คาดการณ์ไว้ จากนั้นจะให้คำสั่ง `fallocate` / `chmod` / `mkswap` / `swapon` ที่ปรับขนาดให้เหมาะสมกับโครงการของคุณ (ไม่เคยน้อยกว่า 8GB) มันจะหยุดชั่วคราวไม่กี่วินาทีเพื่อไม่ให้ข้อความหายไปในส่วนที่เลื่อนกลับ แล้วการประมวลผลจะดำเนินต่อไป**การประมาณค่าหน่วยความจำที่ใช้โดยคำเตือน:**

| โหมด Debayer | พื้นฐาน | ต่อภาพ |
| --- | --- | --- |
| Standard | ~1.5 GB | ~10 MB |
| Texture Aware | ~2.5 GB (โมเดล + Python runtime) | ~15 MB |

คำเตือนจะปรากฏขึ้นเมื่อค่าสูงสุดที่คาดการณ์เกิน RAM + swap หักด้วย margin ความปลอดภัย 1GB และมันนับเฉพาะ swap **ที่รองรับด้วยไฟล์** — การตั้งค่าที่ใช้ zram เท่านั้นจะยังคงถูกแจ้งเตือน

เพื่อเพิ่มพื้นที่ swap ด้วยตนเอง (ตัวอย่าง: 8 GB):

```bash
# Check current memory and swap
free -h

# Create a swap file
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make persistent across reboots
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```



<!-- SCREENSHOT-NEEDED: Terminal on a Jetson Orin (SSH session) showing the full "LOW MEMORY WARNING - Jetson Detected" block printed by `chloros-cli process` on a large folder: the image count and debayer mode line, RAM / current swap / estimated peak figures, and the fallocate/chmod/mkswap/swapon command block it recommends -->

### การจัดการ OOM (Out of Memory)

ระหว่างการประมวลผลChloros

จะติดตามหน่วยความจำ GPU และลดประสิทธิภาพลงอย่างค่อยเป็นค่อยไปแทนที่จะเกิดข้อผิดพลาด:

1. เมื่อการใช้งานหน่วยความจำ GPU เกิน **85%** ขนาดแบทช์จะถูกลดลงอย่างเชิงป้องกัน
2. หากยังเกิดเหตุการณ์ขาดหน่วยความจำ ขนาดแบทช์จะ **ลดลงครึ่งหนึ่ง** และลดลงครึ่งหนึ่งอีกครั้งในทุกครั้งที่เกิด OOM ต่อเนื่อง; แบทช์ที่สำเร็จในครั้งถัดไปจะลดการลงโทษนั้นลงหนึ่งขั้น
3. เมื่ออยู่ภายใต้ความกดดันอย่างต่อเนื่อง ท่อประมวลผลจะเปลี่ยนจาก `fused_gpu` ไปยังเส้นทาง `tiled_gpu` ที่ประหยัดหน่วยความจำ และใช้การประมวลผลด้วย CPU เป็นทางเลือกสุดท้าย

***

## การนำไปใช้งานในสนาม

### ข้อพิจารณาด้านพลังงาน

| รุ่น Jetson     | การใช้พลังงานทั่วไป | หมายเหตุ                   |
| ---------------- | ------------------ | ----------------------- |
| Jetson Orin Nano | 7-15W              | DC barrel jack          |
| Jetson Orin NX   | 10-25W             | DC barrel jack          |
| Jetson AGX Orin  | 15-60W             | USB-C PD หรือ barrel jack |

วางแผนงบประมาณพลังงานสำหรับการประมวลผลอย่างต่อเนื่อง — การใช้พลังงานสูงสุดจะเกิดขึ้นระหว่าง Thread 3 (Processing) ที่ใช้ GPU อย่างหนัก

### คำแนะนำเกี่ยวกับพื้นที่จัดเก็บข้อมูล

* **NVMe SSD** แนะนำอย่างยิ่งสำหรับการติดตั้งบน arm64
* การ์ด SD ช้าเกินไปสำหรับการประมวลผล — ใช้เป็นสื่อบูตเท่านั้น
* วางแผนให้ขนาดข้อมูลภาพดิบเป็น 2-3 เท่าของขนาดข้อมูลภาพดิบสำหรับผลลัพธ์ที่ผ่านการประมวลผล

### การทำงานแบบ Headless ผ่านSSH



Chloros

CLI

เป็นตัวเลือกที่เหมาะสมที่สุดสำหรับการติดตั้ง Jetson แบบ Headless:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format "TIFF (32-bit, Percent)"

# Monitor export progress
chloros-cli export-status
```

### ระบบแบ็กเอนด์ที่ทำงานตลอดเวลาสำหรับการซิงค์เวลา LATTICE / DAQ-E

หาก Jetson ของคุณควบคุมกล้อง LATTICE หรือเซ็นเซอร์แสง DAQ-E ในโหมดไม่มีหน้าจอ ให้เปิดใช้งานบริการ systemd ของระบบแบ็กเอนด์ เพื่อให้ PTP grandmaster ทำงานอย่างต่อเนื่อง (อุปกรณ์นี้ถูกติดตั้งไว้แต่ไม่ได้เปิดใช้งานตามค่าเริ่มต้น):

```bash
sudo systemctl enable --now chloros-backend.service
chloros-cli time-sync status
```

ดู [Linux

Installation](linux-installation.md#always-on-ptp-for-headless-hosts) เพื่อรายละเอียดเพิ่มเติม รวมถึงวิธีที่แพ็กเกจนี้ทำให้พอร์ต PTP 319/320 สามารถ bind ได้โดยไม่ต้องใช้สิทธิ์ root

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

`chloros-cli process` จะออกค่าที่ไม่เท่ากับศูนย์เมื่อการรันที่ขอผลิตภัณฑ์ไม่เขียนภาพใดๆ ดังนั้นสถานะความล้มเหลวของ systemd จึงมีความหมายสำหรับการตรวจสอบ

ใช้ร่วมกับตัวจับเวลา systemd เพื่อการประมวลผลตามกำหนดเวลา:

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

## ตัวอย่างเวิร์กโฟลว์

### การประมวลผล Jetson ขั้นพื้นฐาน

```bash
#!/bin/bash
# Process a drone flight dataset on Jetson
chloros-cli process /data/flights/flight_042 \
    --output /data/processed/flight_042 \
    --format "TIFF (32-bit, Percent)" \
    --indices NDVI NDRE GNDVI
```

###Python

SDK

บน Jetson

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

### การประมวลผลแบบแบทช์สำหรับเที่ยวบินหลายเที่ยว

```bash
#!/bin/bash
# Process all flight datasets in a directory
for flight in /data/flights/*/; do
    name=$(basename "$flight")
    echo "Processing $name..."
    chloros-cli process "$flight" \
        --output "/data/processed/$name" \
        --format "TIFF (32-bit, Percent)" \
        --indices NDVI NDRE
    echo "Completed $name"
done
```

***

## ระบบ Jetson ที่แนะนำสำหรับการใช้งานในสนาม

สำหรับการติดตั้งในสนามและบนอากาศ ให้พิจารณาตัวเลือกบอร์ดตัวพา Jetson Orin NX 16GB ต่อไปนี้:

* **บนอากาศ/โดรน**: ระบบที่มีมาตรฐานความทนทานต่อการสั่นสะเทือน (MIL-STD), น้ำหนักเบา (ต่ำกว่า 300 กรัม), ระบบระบายความร้อนแบบพาสซีฟ
* **สภาพแวดล้อมภาคสนามที่ท้าทาย**: ตัวเครื่องกันน้ำมาตรฐาน IP67/IP69K พร้อมการเชื่อมต่อกล้อง PoE GigE
* **แบบประหยัด/งบประมาณต่ำ**: ชุดพัฒนาพร้อมตัวเครื่องเสริม

ติดต่อ [ทีมสนับสนุนMAPIR

](https://www.mapir.camera/community/contact) เพื่อรับคำแนะนำเกี่ยวกับฮาร์ดแวร์ที่เหมาะสมกับสถานการณ์การใช้งานของคุณ

***

## ขั้นตอนต่อไป

* [การติดตั้งLinux

](linux-installation.md) — รายละเอียดทั่วไปเกี่ยวกับการติดตั้งLinux


* [การปรับแต่งการประมวลผลแบบไดนามิก](../processing-architecture/dynamic-compute-adaptation.md) — ข้อมูลอ้างอิงกลยุทธ์การประมวลผลอย่างครบถ้วน
* [ท่อการประมวลผล](../processing-architecture/processing-pipeline.md) — การเข้าใจท่อการประมวลผล 4 เธรด
* [CLI

: Command Line](../CLI.md) — คู่มือCLI


* [API

:Python

SDK

](../api-python-sdk.md) — คู่มือSDK


* [CLI

Reference](../reference/cli-reference.md) และ [SDK

Reference](../reference/sdk-reference.md) — รายการคำสั่ง/API

ที่ครบถ้วนสำหรับเวอร์ชัน 1.2.0
