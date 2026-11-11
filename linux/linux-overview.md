# ภาพรวม Linux

Chloros 1.3.0 ให้การสนับสนุน Linux แบบเนทีฟสำหรับ **CLI**และ**Python SDK** — การประมวลผลภาพมัลติสเปกตรัมแบบไม่มีหน้าจอ รวมถึงการควบคุมกล้อง LATTICE และเซ็นเซอร์แสง DAQ แบบเรียลไทม์ — บนเครื่องเวิร์กสเตชัน Linux เซิร์ฟเวอร์ และอุปกรณ์เอดจ์ NVIDIA Jetson

{% hint style="info" %}
**ไม่มี GUI บนเดสก์ท็อปสำหรับ Linux**GUI บนเดสก์ท็อปของ Chloros รองรับเฉพาะ Windows เท่านั้น ผู้ใช้ Linux สามารถโต้ตอบกับ Chloros ผ่าน [CLI](../CLI.md) และ [Python SDK](../api-python-sdk.md) `.deb` จะเพิ่มรายการ**Chloros CLI** เข้าสู่เมนูแอปพลิเคชันของคุณ — ซึ่งเพียงแต่เปิดโปรแกรมจำลองเทอร์มินัลที่รัน `chloros-cli`
{% endhint %}

***

## ตารางการสนับสนุนแพลตฟอร์ม

| คุณสมบัติ | Windows (GUI) | Windows (CLI/SDK) | Linux amd64 (CLI/SDK) | Linux arm64 / Jetson (CLI/SDK) |
| --- | --- | --- | --- | --- |
| **GUI บนเดสก์ท็อป** | มี | ไม่ | ไม่มี | ไม่มี |
| **CLI** (`chloros-cli`) | มี | มี | มี | มี |
| **Python SDK** (`chloros-sdk`) | มี | มี | มี | มี |
| **กระบวนการประมวลผลภาพ** | มี | มี | มี | มี |
| **การควบคุมกล้อง LATTICE (แบบเรียลไทม์)** | มี (แท็บ Cameras) | ใช่ (`chloros-cli lattice`, SDK) | ใช่ | ใช่ |
| **เซ็นเซอร์แสง DAQ (แบบเรียลไทม์)** | ใช่ (แท็บ Light Sensors) | ใช่ (`chloros-cli daq pool-*`, SDK) | ใช่ | ใช่ |
| **การซิงค์เวลา PTP (host เป็น grandmaster)** | ใช่ | ใช่ (`chloros-cli time-sync`) | ใช่ | ใช่ |
| **การเร่งความเร็วด้วย GPU (CUDA)** | ใช่ | ใช่ | ใช่ | ใช่ (JetPack 6) |
| **Texture Aware debayer** (ต้องการ GPU CUDA) | ใช่ (Chloros+) | ใช่ (Chloros+) | ใช่ (Chloros+) | ใช่ (Chloros+) |
| **Dynamic Compute Adaptation** | ใช่ | ใช่ | ใช่ | ใช่ |
| **Backend as a system service** (`chloros-backend@<user>.service`) | ไม่ | ไม่ | ใช่ (ต้องเปิดใช้งาน) | ใช่ (ต้องเปิดใช้งาน) |
| **ตัวอัปเดตแบบไม่หยุดทำงาน** (`chloros-cli update`) | ไม่ (ต้องติดตั้งผ่านโปรแกรมติดตั้ง) | ไม่ (ต้องติดตั้งผ่านโปรแกรมติดตั้ง) | ใช่ | ใช่ |***

## สถาปัตยกรรมที่รองรับ

| สถาปัตยกรรม | คำอธิบาย | แพ็กเกจ |
| --- | --- | --- |
| **amd64 (x86_64)** | โปรเซสเซอร์เดสก์ท็อป/เซิร์ฟเวอร์มาตรฐาน (Intel, AMD) | `chloros_<version>_amd64.deb` |
| **arm64 (aarch64)** | โปรเซสเซอร์ ARM — ครอบครัว NVIDIA Jetson Orin (JetPack 6) | `chloros_<version>_arm64_jp6.deb` |

## ระบบปฏิบัติการLinux ที่รองรับ

* **Ubuntu 22.04 LTS หรือเวอร์ชันใหม่กว่า** (amd64)
* **Debian 12 หรือเวอร์ชันใหม่กว่า** (amd64)
* **NVIDIA JetPack 6** (arm64 — แพลตฟอร์ม Jetson Orin)***

## สิ่งที่ผู้ใช้ Linux จะได้รับ

* **Chloros CLI** — อินเทอร์เฟซบรรทัดคำสั่งแบบเต็มรูปแบบสำหรับการประมวลผลแบบแบทช์ การอัตโนมัติ และการเขียนสคริปต์
* **Chloros Python SDK** — อินเทอร์เฟซ Python สำหรับการเขียนโปรแกรมเพื่อใช้ในกระบวนการวิจัยและเครื่องมือที่กำหนดเอง ซึ่งติดตั้งโดย `.deb` สำหรับระบบ `python3` (มี wheel ที่ตรงกับเวอร์ชันสำหรับ virtualenvs ใน `/usr/lib/chloros/sdk/`; PyPI ใช้สำหรับเครื่องที่ไม่มีแพ็กเกจChloros)
* **การควบคุมกล้อง LATTICE** — ค้นหา เชื่อมต่อ ตั้งค่า และถ่ายภาพจากกล้อง LATTICE และกลุ่มกล้องหลายตัวที่ซิงโครไนซ์ผ่าน `chloros-cli lattice` และ SDK; `.deb` รวม runtime Arena SDK ที่กล้องต้องการ
* **การควบคุมเซ็นเซอร์แสง DAQ** — เชื่อมต่อเซ็นเซอร์ DAQ-U/M/E, ส่งสเปกตรัมที่ได้รับการปรับเทียบ และบันทึกไฟล์ `.daq` ผ่าน `chloros-cli daq pool-*` และ SDK
* **การซิงค์เวลา PTP** — ระบบหลังบ้านChloros ทำงานเป็น grandmaster PTP ที่กล้อง LATTICE และเซ็นเซอร์ DAQ-E เชื่อมต่อเป็น slave; ตรวจสอบด้วย `chloros-cli time-sync` และรักษาให้ทำงานแบบ headless ด้วย unit systemd `chloros-backend@<user>.service` (ดู [การติดตั้งบน [Linux]](linux-installation.md#always-on-ptp-for-headless-hosts))
* **การอัตโนมัติของโครงการ** — ดำเนินการโครงการที่บันทึกไว้แบบ headless ด้วย `chloros-cli project` และ `open_project` ของSDK
* **การเร่งความเร็วด้วย GPU** — การประมวลผลที่เร่งความเร็วด้วย CUDA บน GPU ของ NVIDIA (GPU สำหรับเดสก์ท็อปบน amd64 และ GPU ของ Jetson ด้วย JetPack 6)
* **การปรับการคำนวณแบบไดนามิก** — การตรวจจับฮาร์ดแวร์และการเลือกกลยุทธ์การประมวลผลอัตโนมัติ พร้อมตัวเลือกการแทนที่ `CHLOROS_STRATEGY` เป็นทางออกสำหรับผู้เชี่ยวชาญ
* **คุณสมบัติการประมวลผลทั้งหมด** — ท่อประมวลผลเดียวกันกับWindows: การปรับเทียบ, การแก้ไข vignette, ดัชนีพืชพรรณ และทุกรูปแบบการส่งออก
* **คุณสมบัติ Chloros+** — การประมวลผลแบบมัลติเธรด (แบบท่อ), Texture Aware debayer (บน GPU CUDA), และดัชนีที่กำหนดเอง พร้อมแผน Chloros+ แบบเสียค่าใช้จ่าย

## สิ่งที่ผู้ใช้Linux ไม่ได้รับ

* **GUI บนเดสก์ท็อป** — ไม่มีอินเทอร์เฟซกราฟิก; การโต้ตอบทั้งหมดผ่าน CLI หรือ Python SDK
* **โปรแกรมดูภาพ** — ไม่มีโปรแกรมดูภาพแบบโต้ตอบ, มุมมองแบบกริด หรือเครื่องหมายบนแผนที่
* **การจัดการโครงการแบบภาพ** — โครงการถูกสร้างและควบคุมผ่านคำสั่งCLI และการเรียกSDK (อุปกรณ์ฮาร์ดแวร์เอง — กล้อง, เซนเซอร์, การจับภาพ — ยังคงสามารถควบคุมได้เต็มที่จากเทอร์มินัล)***

## ข้อกำหนดด้านใบอนุญาต

การเข้าถึง CLI และ SDK จำเป็นต้องมี **แพ็กเกจ Chloros+ แบบเสียเงิน — Copper หรือสูงกว่า**(Copper, Bronze, Silver, Gold) แพ็กเกจ**Iron** แบบฟรีไม่มีการเข้าถึง CLI/SDK ข้อกำหนดขั้นต่ำนี้ถูกบังคับใช้โดยระบบหลังบ้าน (backend) ไม่ใช่เพียงผ่าน CLI เท่านั้น:

| สถานการณ์ | การตอบสนองจากระบบหลังบ้าน |
| --- | --- |
| ยังไม่เข้าสู่ระบบ | `401` พร้อม `error_code: AUTH_REQUIRED` |
| เข้าสู่ระบบในชั้น Iron ฟรี | `403` พร้อม `error_code: PLAN_UPGRADE_REQUIRED` |

`chloros-cli status` ทำงานได้บนทุกระดับ — เป็นเส้นทางเดียวที่ได้รับการยกเว้นจากเกตเวย์ — ดังนั้นเหตุผลของการปฏิเสธจึงสามารถเห็นได้เสมอ

***

## เริ่มต้นใช้งานบน Linux

1. **ติดตั้ง Chloros** — ดู [การติดตั้งบน Linux](linux-installation.md) สำหรับการติดตั้ง `.deb`
2. **ตรวจสอบ** — `chloros-cli --version` จะแสดงผล `Chloros CLI 1.3.0`; `chloros-cli selftest` จะดำเนินการวินิจฉัย 7 ขั้นตอน
3. **ตรวจสอบPython SDK** — `.deb` จะติดตั้งมัน: `python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"` (ใน virtualenv, `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl`)
4. **ลงชื่อเข้าใช้** — `chloros-cli login your@email.com 'your-password'` (ครั้งเดียวต่อเครื่อง; การอัปเกรดแพ็กเกจจะรักษาข้อมูลการลงชื่อเข้าใช้ไว้ แต่การลบแพ็กเกจจะลบข้อมูลดังกล่าว)
5. **ประมวลผลชุดข้อมูลแรก** — `chloros-cli process ~/datasets/flight001`

สำหรับ NVIDIA Jetson โปรดดู [คู่มือ NVIDIA Jetson](nvidia-jetson-guide.md) ที่จัดทำขึ้นโดยเฉพาะ เพื่อการตั้งค่าเฉพาะแพลตฟอร์ม พฤติกรรมความร้อน และการติดตั้งในสนาม

***

## ขั้นตอนต่อไป

* [การติดตั้ง Linux](linux-installation.md) — การติดตั้งอย่างละเอียด ตำแหน่งไฟล์ และการแก้ไขปัญหาสำหรับ amd64 และ arm64
* [คู่มือ NVIDIA Jetson](nvidia-jetson-guide.md) — การตั้งค่าเฉพาะสำหรับ Jetson, พฤติกรรมด้านหน่วยความจำและอุณหภูมิ, การนำไปใช้งานจริง
* [CLI : Command Line](../CLI.md) — คู่มือ CLI
* [API : Python SDK](../api-python-sdk.md) — คู่มือ SDK
* [CLI Reference](../reference/cli-reference.md) และ [SDK Reference](../reference/sdk-reference.md) — รายการคำสั่ง/API ที่ครบถ้วนสำหรับเวอร์ชัน 1.3.0
* [การปรับแต่งการคำนวณแบบไดนามิก](../processing-architecture/dynamic-compute-adaptation.md) — วิธีที่Chloros ปรับตัวให้เข้ากับฮาร์ดแวร์ของคุณ

{% hint style="info" %}
**การอ่านคู่มือนี้ด้วยโปรแกรม** ทุกหน้ายังถูกให้บริการในรูปแบบ Markdown ดิบที่ URL ของตัวเอง พร้อมด้วย `.md` (เช่น `https://mapir.gitbook.io/chloros/linux/linux-installation.md`) และดัชนีของคู่มือทั้งหมดถูกเผยแพร่ที่ [`https://mapir.gitbook.io/chloros/llms.txt`](https://mapir.gitbook.io/chloros/llms.txt).
{% endhint %}
