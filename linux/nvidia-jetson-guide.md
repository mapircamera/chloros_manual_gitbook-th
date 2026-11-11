# คู่มือ NVIDIA Jetson

Chloros บน NVIDIA Jetson ช่วยให้สามารถประมวลผลภาพมัลติสเปกตรัมที่ขอบเครือข่าย — ในสนามจริง บน UAV และในสถานที่ติดตั้งห่างไกล Chloros 1.3.0 จะตรวจจับรุ่น Jetson ของคุณเมื่อเริ่มต้นระบบ และปรับกลยุทธ์การประมวลผลให้เหมาะสมกับฮาร์ดแวร์ที่ตรวจพบ **ไม่จำเป็นต้องปรับแต่งด้วยมือ**
{% hint style="info" %}
**แพ็กเกจ Jetson 1.3.0 ดำเนินการประมวลผลบน GPU ของ Jetson.** `chloros_1.3.0_arm64_jp6.deb` ถูกพัฒนาสำหรับ JetPack 6 และใช้ PyTorch เวอร์ชัน CUDA ของ JetPack 6 ดังนั้นการประมวลผล รวมถึงการ debayer ที่คำนึงถึงเท็กซ์เจอร์ (Texture Aware debayer) จะทำงานบน GPU ของ Orin. แพ็กเกจนี้ไม่มาพร้อม CUDA เอง: มันใช้ไลบรารี CUDA และ cuDNN ที่ JetPack 6 ติดตั้งไว้ (`sudo apt install nvidia-jetpack` บนภาพที่ไม่มีไลบรารีเหล่านี้) ในกรณีที่ไม่มี GPU CUDA ที่สามารถใช้งานได้ การประมวลผลจะทำงานบน CPU และ Texture Aware จะใช้ Standard debayer
{% endhint %}

***

## รุ่น Jetson ที่รองรับ

| รุ่น                | RAM            | กลยุทธ์การประมวลผล                                     | การใช้งานที่แนะนำ                                          |
| -------------------- | -------------- | ------------------------------------------------------- | -------------------------------------------------------- |
| **Jetson AGX Orin**  | 32-64GB shared | `GPU_PARALLEL` (2 workers)                              | ประสิทธิภาพสูงสุด, ชุดข้อมูลขนาดใหญ่                      |
| **Jetson Orin NX**   | 8-16GB shared  | `GPU_PARALLEL` (2 workers, 16GB) / `GPU_SINGLE` (8GB)   | คำแนะนำหลักสำหรับการติดตั้งในสนาม              |
| **Jetson Orin Nano** | 8GB shared     | `GPU_SINGLE` (1 worker, sequential)                     | ระบบประมวลผล edge ระดับเริ่มต้น                                 |

{% hint style="info" %}
แพ็กเกจLinux arm64 ต้องการ **JetPack 6** ซึ่งสามารถใช้งานได้บนตระกูล Jetson Orin รุ่นเก่า (Nano, TX2, Xavier NX) ไม่สามารถรัน JetPack 6 ได้ และไม่ได้รับการสนับสนุนจากแพ็กเกจปัจจุบัน
{% endhint %}

***

## ข้อกำหนด

* **JetPack 6.x** (แนะนำให้ใช้เวอร์ชันล่าสุด)
* **NVIDIA CUDA และ cuDNN** จาก JetPack 6 (`sudo apt install nvidia-jetpack` หากภาพระบบของคุณไม่มี)
* **แผน Chloros+ แบบชำระเงิน** — ระดับ Copper หรือสูงกว่า (จำเป็นสำหรับการเข้าถึง CLI/SDK ทั้งหมด; ถูกบังคับใช้ด้านเซิร์ฟเวอร์)

## การติดตั้ง

```bash
# Install the arm64 .deb package (JetPack 6) -- apt, not dpkg -i, so the
# Python SDK's requirements (python3-numpy, python3-requests) come too
sudo apt install ./chloros_1.3.0_arm64_jp6.deb

# Verify installation
chloros-cli --version    # prints "Chloros CLI 1.3.0"
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"   # the .deb installs the SDK

# Run system diagnostics
chloros-cli selftest
```

`.deb` จะติดตั้ง Python SDK สำหรับระบบ `python3` ที่สอดคล้องกับเวอร์ชันนี้ มีเพียง virtualenv เท่านั้นที่จำเป็นต้องติดตั้ง wheel ที่มาพร้อมแพ็กเกจเข้าไป: `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl` (ทำงานแบบออฟไลน์อย่างสมบูรณ์) สำหรับรายละเอียดการติดตั้งบน Linux ทั่วไป ตำแหน่งไฟล์ และการแก้ไขปัญหา โปรดดู [การติดตั้งบน Linux](linux-installation.md).

{% hint style="info" %}
**ไม่จำเป็นต้องแตกไฟล์เมื่อเริ่มต้นอีกต่อไป** ตั้งแต่อัปเดตเวอร์ชัน 1.3.0 เป็นต้นไป ส่วนหลัง (backend) และCLI จะถูกติดตั้งในสภาพที่ยังไม่แตกไฟล์ภายใต้ `/usr/lib/chloros/` ดังนั้นจึงไม่จำเป็นต้องแตกไฟล์ไปยังโฟลเดอร์ชั่วคราวทุกครั้งที่เริ่มต้น ไฟล์ชั่วคราวจะยังคงถูกส่งไปยัง `/mnt/ssd/tmp` โดยอัตโนมัติเมื่อโฟลเดอร์นั้นมีอยู่; หากไม่มี คุณสามารถตั้งค่า `TMPDIR` เป็นเส้นทางบน NVMe ของคุณ (`export TMPDIR=/mnt/nvme/tmp`) ได้
{% endhint %}

***

## การปรับแต่งการประมวลผลแบบไดนามิกบน Jetson

### วิธีการทำงาน

เมื่อเริ่มต้นระบบ Chloros จะวิเคราะห์โปรไฟล์ระบบของคุณ:

1. **ตรวจจับโมเดล Jetson** ผ่าน `/proc/device-tree/model`
2. **อ่านหน่วยความจำ GPU/CPU ที่ใช้ร่วมกันได้** (Jetson ใช้หน่วยความจำแบบรวม)
3. **ถาม PyTorch ว่าสามารถใช้ CUDA ได้หรือไม่** — หากติดตั้ง CUDA และ cuDNN ของ JetPack 6 แล้ว ก็สามารถทำได้
4. **เลือกกลยุทธ์การประมวลผล** (`GPU_PARALLEL`, `GPU_SINGLE` หรือ `CPU_PARALLEL`) และจำนวน worker โดยอัตโนมัติ

การตัดสินใจนี้ขึ้นอยู่กับ **ปริมาณ RAM ที่ใช้ร่วมกันทั้งหมด** ไม่ใช่ชื่อโมเดล:

* **เมื่อ RAM รวมน้อยกว่า 12GB**(Jetson ทั้งหมดที่มี RAM 8GB):**1 worker — การประมวลผลแบบลำดับที่ตั้งใจ**เนื่องจากหน่วยความจำไม่เพียงพอสำหรับ worker ที่ทำงานพร้อมกัน จึงประมวลผลภาพทีละภาพ บน Jetson ที่มี**8GB หรือน้อยกว่า**, Thread 3 จะข้าม pool ของ worker ไปทั้งหมด และทำงานต่อภาพแต่ละภาพแบบ in-process. ชื่อบันทึกของ backend เรียกกลยุทธ์นี้ว่า `GPU_SINGLE`.
* **12GB หรือมากกว่า**(Orin NX 16GB, AGX Orin): หน่วยความจำแบบรวม (unified memory) มีคุณสมบัติตรงตามเกณฑ์สำหรับ `GPU_PARALLEL` แต่จำนวน worker**ถูกจำกัดไว้ที่ 2 บน Jetson** — GPU, RAM ของกระบวนการทำงาน และบริบท CUDA ของแต่ละกระบวนการทำงาน ล้วนใช้จากพูลที่แชร์ร่วมกัน ดังนั้นการเพิ่มจำนวนกระบวนการทำงานจะเพิ่มความเสี่ยงต่อการเกิดข้อผิดพลาดเนื่องจากหน่วยความจำไม่เพียงพอ
* **ไม่มี CUDA ที่ใช้งานได้** (ขาดไลบรารี CUDA ของ JetPack): `CPU_PARALLEL` ที่ 12GB หรือมากกว่า, ภาพหนึ่งภาพต่อครั้งด้านล่าง

คุณสามารถแทนที่การเลือกอัตโนมัติด้วยตัวแปรสภาพแวดล้อม `CHLOROS_STRATEGY` — ดู [Dynamic Compute Adaptation](../processing-architecture/dynamic-compute-adaptation.md#manual-strategy-override).

### พฤติกรรมตามรุ่น

| รุ่น Jetson                | กลยุทธ์       | ผู้ทำงาน | การดำเนินการ                                      |
| --------------------------- | -------------- | ------- | ---------------------------------------------- |
| **Jetson Orin Nano 8GB**    | `GPU_SINGLE`   | 1       | วงจรวนซ้ำแบบลำดับในกระบวนการ (`tiled_gpu` เมื่อมีความกดดันด้านหน่วยความจำ) |
| **Jetson Orin NX 8GB**      | `GPU_SINGLE`   | 1       | วงจรในกระบวนการแบบลำดับ                     |
| **Jetson Orin NX 16GB**     | `GPU_PARALLEL` | 2       | กระบวนการทำงานพร้อมกัน, เส้นทาง `fused_gpu`  |
| **Jetson AGX Orin 32-64GB** | `GPU_PARALLEL` | 2       | กระบวนการทำงานพร้อมกัน, เส้นทาง `fused_gpu`  |

ความแตกต่างหลักระหว่างแพลตฟอร์มคือ **หน่วยความจำ** Jetson 8GB ต้องประมวลผลภาพทีละภาพโดยใช้วิธีการแบ่งเป็นไทล์ที่ประหยัดหน่วยความจำเมื่อมีความกดดันสูง ในขณะที่ Orin 16GB+ สามารถประมวลผลภาพ 2 ภาพผ่าน GPU พร้อมกันโดยใช้ท่อประมวลผลแบบรวมที่มีอัตราผ่านสูง

### งบประมาณ GPU ตามรุ่น

แต่ละรุ่น Jetson ยังมีโปรไฟล์ฮาร์ดแวร์ที่กำหนดขีดจำกัดปริมาณการประมวลผลที่กระบวนการในพูลร่วมสามารถใช้ได้ และปรับขนาดชุดข้อมูล (batch size):

| รุ่น | ขีดจำกัดงบประมาณ GPU | ตัวคูณขนาดชุดข้อมูล | ส่วนที่สำรองไว้สำหรับระบบ/จอแสดงผล |
| --- | --- | --- | --- |
| **Jetson Orin Nano** | 70% | ×0.8 | 2.0 GB |
| **Jetson Orin NX** | 75% | ×1.0 | 3.0 GB |
| **Jetson AGX Orin** | 80% | ×1.5 | 4.0 GB |

RAM ที่ตรวจพบจะปรับโปรไฟล์: Jetson ที่รายงาน **16GB หรือมากกว่า** จะได้รับการเพิ่มตัวคูณขนาดชุดเป็น ×1.2 ขนาดชุดพื้นฐานก่อนการคูณคือ 8 ภาพ

สำหรับข้อมูลอ้างอิงการปรับแต่งการคำนวณอย่างครบถ้วน โปรดดู [Dynamic Compute Adaptation](../processing-architecture/dynamic-compute-adaptation.md).

***

## ข้อจำกัดความถี่ GPU สำหรับ Texture Aware บน Nano และ Orin Nano

กระบวนการ debayer ของ Texture Aware ดำเนินการอนุมานเครือข่ายประสาทเทียมบน GPU ซึ่งอาจก่อให้เกิด **คำเตือนกระแสเกิน**บนรุ่น Jetson ประหยัดพลังงาน (ระดับ 10-15W) เมื่อ GPU ทำงานที่ความเร็วนาฬิกาสูงสุด เมื่อคุณส่งผ่าน `--debayer texture-aware` บน**Jetson Nano หรือ Orin Nano**, CLI จะตรวจสอบความถี่สูงสุดของ GPU และจำกัดไว้ที่ **510 MHz** (510000000) หากความถี่ปัจจุบันสูงกว่า:

* หากCLI สามารถเขียนค่าความถี่ GPU ลงในโหนด sysfs ได้ การจำกัดความถี่จะถูก **นำไปใช้โดยอัตโนมัติ** และจะแสดงข้อความยืนยัน
* หากไม่สามารถ (จำเป็นต้องมีสิทธิ์ root)CLI จะแสดงคำสั่ง `sudo` ที่ถูกต้องเพื่อตั้งค่าจำกัดความเร็วแบบแมนนวล รอสักครู่เพื่อให้คุณอ่านได้ แล้วจึงดำเนินการต่อ — การประมวลผลยังคงทำงานอยู่ แต่อาจแสดงคำเตือนเรื่องกระแสเกิน

เพื่อตั้งค่าจำกัดความเร็วด้วยตัวเองก่อนการประมวลผล:

```bash
echo 510000000 | sudo tee /sys/devices/platform/bus@0/17000000.gpu/devfreq/17000000.gpu/max_freq
```

รุ่นที่มีกำลังสูง (Orin NX 25W, AGX Orin 60W) ทำงานด้วยความเร็ว GPU เต็มกำลัง; ไม่มีการตั้งค่าจำกัดความเร็ว Standard debayer ไม่เคยทริกเกอร์การตั้งค่าจำกัดความเร็วบนรุ่นใดก็ตาม

{% hint style="info" %}
**Texture Aware บน Jetson จะทำงานกับภาพหนึ่งภาพต่อครั้งเสมอ** แต่ละ worker จะต้องการบริบท CUDA ของตัวเอง (~1GB) พร้อมทั้งสำเนาของโมเดล denoiser ของตัวเอง ซึ่งหน่วยความจำแบบรวม (unified memory) ไม่สามารถรองรับได้ — ดังนั้นบน Jetson เส้นทาง Texture Aware จะถูกตรึงไว้ที่ worker เดียว โดยมีการเข้าถึง GPU แบบลำดับ (serialized) คาดการณ์ว่า Texture Aware จะช้ากว่า Standard อย่างชัดเจนบน Jetson ทุกรุ่น Texture Aware ใช้กับภาพSurvey3: การจับภาพ LATTICE จะใช้การเดเบย์แบบ Standard เสมอ หากไม่มี GPU CUDA ที่ใช้งานได้ การประมวลผลจะใช้ Standard และระบบหลังบ้านจะบันทึกบรรทัด `[DEBAYER] … needs a CUDA GPU …` หนึ่งบรรทัดเพื่ออธิบายเหตุผล (แสดงร่วมกับ `chloros-cli --verbose`)
{% endhint %}

***

## การจัดการความร้อน

อุปกรณ์ Jetson มีพื้นที่ความร้อนที่จำกัด โดยเฉพาะอย่างยิ่งในการติดตั้งในสภาพแวดล้อมที่ปิดหรือบนอากาศChloros ติดตามอุณหภูมิ SoC และปรับขนาดชุดงานให้เหมาะสมโดยอัตโนมัติ:

| อุณหภูมิ         | การดำเนินการ                                            |
| ------------------- | ------------------------------------------------- |
| **< 70°C**          | การทำงานปกติ — ความเร็วการประมวลผลเต็ม          |
| **70°C** (คำเตือน)  | ขนาดชุดงานลดลงอย่างค่อยเป็นค่อยไป (100% → 50% ระหว่าง 70°C ถึง 80°C) |
| **80°C** (วิกฤต) | ลดความเร็วอย่างรุนแรง (50% → 0% ระหว่าง 80°C ถึง 90°C) |
| **90°C** (ปิดระบบ) | หยุดการประมวลผล GPU ทั้งหมด — ต้องรอให้ระบบเย็นลง |

{% hint style="warning" %}
**ต้องมั่นใจว่ามีระบบระบายอากาศและตัวระบายความร้อนที่เพียงพอ** เพื่อการประมวลผลอย่างต่อเนื่อง โดยเฉพาะในตู้อุปกรณ์ภาคสนามแบบปิดหรือระบบที่ติดตั้งบนอากาศ การลดความเร็วเนื่องจากความร้อนจะลดปริมาณงานประมวลผลเพื่อปกป้องฮาร์ดแวร์
{% endhint %}

***

## การจัดการหน่วยความจำ

อุปกรณ์ Jetson ใช้ **หน่วยความจำแบบรวม** — GPU และ CPU ใช้ RAM ทางกายภาพเดียวกัน ความจุ VRAM ที่แสดง (เช่น ~15.3GB บน Orin NX 16GB) ไม่ใช่หน่วยความจำ GPU ที่จัดสรรเฉพาะ แต่เป็น RAM เดียวกันที่ระบบปฏิบัติการและกระบวนการอื่น ๆ ทั้งหมดกำลังใช้งานอยู่

### คำเตือนและคำแนะนำเกี่ยวกับการสวอป

ก่อนการประมวลผลบน Jetson, CLI จะนับจำนวนภาพ RAW ในโฟลเดอร์อินพุตของคุณ (`.tif`, `.tiff`, `.raw`, `.dng` — ไม่รวมภาพตัวอย่าง JPG), ประมาณการปริมาณหน่วยความจำสูงสุดที่กระบวนการทำงานต้องการ และ **แจ้งเตือนก่อนเริ่ม** หาก RAM + swap มีแนวโน้มไม่เพียงพอ การแจ้งเตือนนี้มีหัวข้อว่า `LOW MEMORY WARNING - Jetson Detected`, แสดงจำนวนภาพของคุณ, RAM, swap ปัจจุบัน และค่าสูงสุดที่คาดการณ์ไว้ จากนั้นจะแสดงคำสั่ง `fallocate` / `chmod` / `mkswap` / `swapon` ที่ปรับขนาดให้พอดีกับ โครงการของคุณ (ไม่เคยน้อยกว่า 8GB) มันจะหยุดชั่วคราวไม่กี่วินาทีเพื่อไม่ให้ข้อความหายไปในส่วนที่เลื่อนกลับ (scrollback) แล้วการประมวลผลจะดำเนินต่อไป**การประมาณการหน่วยความจำที่คำเตือนนี้ใช้:**

| โหมด Debayer | พื้นฐาน | ต่อภาพ |
| --- | --- | --- |
| มาตรฐาน | ~1.5 GB | ~10 MB |
| Texture Aware | ~2.5 GB (โมเดล + Python runtime) | ~15 MB |

คำเตือนจะปรากฏขึ้นเมื่อค่าสูงสุดที่คาดการณ์ไว้เกินกว่า RAM + swap ลบด้วย margin ความปลอดภัย 1GB และจะนับเฉพาะ swap ที่ **มีไฟล์รองรับ** เท่านั้น — การตั้งค่าที่ใช้ zram อย่างเดียวจะยังคงถูกแจ้งเตือน

เพื่อเพิ่ม swap ด้วยมือ (ตัวอย่าง: 8 GB):

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

ระหว่างการประมวลผลChloros จะติดตามหน่วยความจำ GPU และลดประสิทธิภาพอย่างค่อยเป็นค่อยไปแทนที่จะเกิดข้อผิดพลาด:

1. เมื่อการใช้งานหน่วยความจำ GPU เกิน **85%** ขนาดแบทช์จะถูกลดลงอย่างเชิงป้องกัน
2. หากเกิดเหตุการณ์ขาดหน่วยความจำ (OOM) ขึ้นอีก ขนาดแบทช์จะ **ลดลงครึ่งหนึ่ง** และลดลงครึ่งหนึ่งอีกครั้งในทุกครั้งที่เกิด OOM ต่อเนื่อง; แบทช์ที่สำเร็จในครั้งถัดไปจะลดการลงโทษนี้ลงหนึ่งขั้น
3. เมื่ออยู่ภายใต้ความกดดันอย่างต่อเนื่อง ท่อประมวลผลจะเปลี่ยนจาก `fused_gpu` ไปยังเส้นทาง `tiled_gpu` ที่ประหยัดหน่วยความจำ และใช้การประมวลผลด้วย CPU เป็นทางเลือกสุดท้าย

เนื่องจาก GPU ใช้ RAM ของ Jetson ร่วมกัน ดังนั้น RAM บวกกับพื้นที่ swap ยังคงเป็นข้อจำกัดของการทำงาน — คำเตือนเกี่ยวกับ swap ข้างต้นคือสิ่งที่ต้องให้ความสนใจ

***

## การนำไปใช้งานในสนาม

### ข้อพิจารณาด้านพลังงาน

| รุ่น Jetson     | การใช้พลังงานทั่วไป | หมายเหตุ                   |
| ---------------- | ------------------ | ----------------------- |
| Jetson Orin Nano | 7-15W              | DC barrel jack          |
| Jetson Orin NX   | 10-25W             | DC barrel jack          |
| Jetson AGX Orin  | 15-60W             | USB-C PD or barrel jack |

วางแผนงบประมาณพลังงานสำหรับการประมวลผลอย่างต่อเนื่อง — การใช้พลังงานสูงสุดจะเกิดขึ้นระหว่าง Thread 3 (Processing) ที่ใช้ GPU อย่างหนัก

### คำแนะนำเกี่ยวกับพื้นที่จัดเก็บข้อมูล

* **NVMe SSD** แนะนำอย่างยิ่งสำหรับการติดตั้งบน arm64
* การ์ด SD ช้าเกินไปสำหรับการประมวลผล — ใช้เป็นสื่อบูตเท่านั้น
* วางแผนให้พื้นที่จัดเก็บข้อมูลผลลัพธ์ที่ผ่านการประมวลผลมีขนาด 2-3 เท่าของขนาดข้อมูลภาพดิบ

### การทำงานแบบไม่มีหน้าจอผ่านSSH

ChlorosCLI
เป็นตัวเลือกที่เหมาะสมที่สุดสำหรับการติดตั้ง Jetson แบบไม่มีหน้าจอ:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format "TIFF (32-bit, Percent)"

# Monitor export progress
chloros-cli export-status
```

### ระบบแบ็กเอนด์ที่ทำงานตลอดเวลาสำหรับการซิงค์เวลา LATTICE / DAQ-E

หาก Jetson ของคุณควบคุมกล้อง LATTICE หรือเซ็นเซอร์แสง DAQ-E ในโหมด Headless ให้เปิดใช้งานบริการ systemd ของระบบแบ็กเอนด์ **ในฐานะผู้ใช้ของคุณ** เพื่อให้ PTP grandmaster ทำงานอย่างต่อเนื่อง และสคริปต์ SDK สามารถตรวจพบระบบแบ็กเอนด์ที่กำลังทำงานอยู่ (อุปกรณ์นี้ติดตั้งไว้แล้วแต่ไม่เปิดใช้งานโดยค่าเริ่มต้น):

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
chloros-cli time-sync status
```

ระบบแบ็กเอนด์จะทำงานในนามของคุณ: ข้อมูลที่บันทึกไว้จะเป็นของคุณ และมันจะใช้ `chloros-cli login` ของคุณ ดู [การติดตั้งบน [Linux](linux-installation.md#always-on-ptp-for-headless-hosts) สำหรับรายละเอียดเพิ่มเติม รวมถึง `chloros-backend.service` ของ root และวิธีที่แพ็กเกจทำให้พอร์ต PTP 319/320 สามารถ bind ได้โดยไม่ต้องใช้สิทธิ์ root

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

`chloros-cli process` จะออกค่าที่ไม่เท่ากับศูนย์เมื่อการรันที่ขอผลิตภัณฑ์ไม่เขียนภาพใดๆ ดังนั้นสถานะความล้มเหลวของ systemd จึงมีความหมายสำหรับการติดตาม

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

### การประมวลผลพื้นฐานบน Jetson

```bash
#!/bin/bash
# Process a drone flight dataset on Jetson
chloros-cli process /data/flights/flight_042 \
    --output /data/processed/flight_042 \
    --format "TIFF (32-bit, Percent)" \
    --indices NDVI GNDVI OSAVI
```

### PythonSDK
บน Jetson

```python
from chloros_sdk import ChlorosLocal

with ChlorosLocal() as chloros:
    chloros.create_project("field_survey_042")
    chloros.import_images("/data/flights/flight_042")
    chloros.configure(
        indices=["NDVI", "GNDVI", "OSAVI"],
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
        --indices NDVI OSAVI
    echo "Completed $name"
done
```

***

## ระบบ Jetson ที่แนะนำสำหรับการใช้งานในสนาม

สำหรับการติดตั้งในสนามและบนอากาศ ให้พิจารณาตัวเลือกบอร์ดตัวพา Jetson Orin NX 16GB ต่อไปนี้:

* **บนอากาศ/โดรน**: ระบบที่มีระดับความทนทานต่อการสั่นสะเทือน (MIL-STD), น้ำหนักเบา (ต่ำกว่า 300g), ระบบระบายความร้อนแบบพาสซีฟ
* **สภาพแวดล้อมภาคสนามที่ท้าทาย**: ตัวเครื่องกันน้ำตามมาตรฐาน IP67/IP69K พร้อมการเชื่อมต่อกล้อง PoE GigE
* **แบบประหยัด/งบประมาณต่ำ**: ชุดพัฒนา (Developer kits) พร้อมตัวเครื่องเสริม

ติดต่อ [ทีมสนับสนุน MAPIR](https://www.mapir.camera/community/contact) เพื่อรับคำแนะนำเกี่ยวกับฮาร์ดแวร์ที่เหมาะสมกับสถานการณ์การใช้งานของคุณ

***

## ขั้นตอนต่อไป

* [การติดตั้ง Linux](linux-installation.md) — รายละเอียดทั่วไปเกี่ยวกับการติดตั้ง Linux

* [การปรับแต่งการประมวลผลแบบไดนามิก](../processing-architecture/dynamic-compute-adaptation.md) — เอกสารอ้างอิงกลยุทธ์การประมวลผลอย่างครบถ้วน
* [ท่อประมวลผล](../processing-architecture/processing-pipeline.md) — การเข้าใจท่อประมวลผล 4 เธรด
* [CLI : Command Line](../CLI.md) — คู่มือ CLI

* [API: Python SDK](../api-python-sdk.md) — คู่มือ SDK

* [คู่มืออ้างอิง CLI](../reference/cli-reference.md) และ [คู่มืออ้างอิง SDK](../reference/sdk-reference.md) — รายการคำสั่ง/API ที่ครบถ้วนสำหรับเวอร์ชัน 1.3.0
