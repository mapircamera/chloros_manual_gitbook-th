# การติดตั้ง Linux

Chloros ถูกแจกจ่ายสำหรับ Linux ในรูปแบบแพ็กเกจ `.deb` ซึ่งติดตั้ง CLI, เซิร์ฟเวอร์แบ็กเอนด์ และ Python SDK (สำหรับระบบ `python3` ยังรวมถึงไฟล์ wheel ที่ตรงกับเวอร์ชันสำหรับ virtualenvs)

ชื่อไฟล์แพ็กเกจจะระบุเวอร์ชันและสถาปัตยกรรม: `chloros_1.3.0_amd64.deb` สำหรับ x86_64, และ `chloros_1.3.0_arm64_jp6.deb` สำหรับ arm64 (NVIDIA Jetson, JetPack 6) ให้แทนที่ด้วยชื่อไฟล์ที่คุณดาวน์โหลดมาจริงในคำสั่งด้านล่าง

{% hint style="info" %}
**ใหม่ในเวอร์ชัน 1.3.0: โปรแกรมถูกส่งมาในสภาพที่ยังไม่ถูกแตกไฟล์** ส่วนหลัง (backend) และCLI ถูกติดตั้งเป็นโฟลเดอร์ `/usr/lib/chloros/backend/` และ `/usr/lib/chloros/cli/`, แทนที่จะเป็นไฟล์ที่แตกตัวได้เองเพียงไฟล์เดียว โปรแกรมเหล่านี้ไม่แตกตัวเข้าสู่โฟลเดอร์ชั่วคราวทุกครั้งที่เปิดใช้งานอีกต่อไป จึงเริ่มต้นได้เร็วขึ้นมาก (บน x86_64 ส่วนหลังเคยใช้เวลาประมาณ 100 วินาทีในการแตกตัวทุกครั้งที่เริ่มต้น) และไม่จำเป็นต้องมีพื้นที่ว่างหลาย GB ใน `/tmp` อีกต่อไป ส่วน `.deb` สำหรับ amd64 ก็มีขนาดลดลงประมาณครึ่งหนึ่งจากเดิม
{% endhint %}

***

## Linux amd64 (x86_64)

### ข้อกำหนดระบบ

| ข้อกำหนด | ขั้นต่ำ | แนะนำ |
| --- | --- | --- |
| **ระบบปฏิบัติการ** | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS |
| **โปรเซสเซอร์** | x86_64 (Intel/AMD) | Intel Core i7 หรือดีกว่า |
| **หน่วยความจำ (RAM)** | 8GB | 16GB หรือมากกว่า |
| **การ์ดกราฟิก** | ไม่จำเป็น (ใช้ CPU ประมวลผล) | NVIDIA GPU ที่มี VRAM 4GB ขึ้นไป (12GB ขึ้นไปจะปลดล็อก `GPU_PARALLEL`, 7GB ขึ้นไปจะปิด Texture Aware ในเส้นทางภาพเดียว) |
| **พื้นที่จัดเก็บ** | พื้นที่ว่าง 2GB | SSD ที่มีพื้นที่ว่าง 10GB ขึ้นไป |
| **Python** | Python 3.7 ขึ้นไป (สำหรับ SDK) | Python 3.10 ขึ้นไป |

> **Ubuntu 20.04 และ Debian 11 ไม่ได้รับการสนับสนุน** รายการความพึ่งพาของ `.deb`
> มาจากสิ่งที่ backend Chloros เชื่อมโยงจริง ๆ ซึ่งรวมถึง
> `libc6` ที่ใหม่กว่า glibc 2.31 ที่มาพร้อมกับ focal และ bullseye ดังนั้น `apt` จึงปฏิเสธการ
> ติดตั้งทันที แทนที่จะปล่อยให้ล้มเหลวในภายหลังขณะทำงาน

### การติดตั้ง

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
```

{% hint style="info" %}
**ใช้ `apt install ./…` ไม่ใช่ `dpkg -i`** `apt` แก้ไขการพึ่งพาของแพ็กเกจและติดตั้งสิ่งที่มันแนะนำด้วย: ข้อกำหนดของPython SDK, `python3-numpy` และ `python3-requests` ส่วน `dpkg -i` ไม่ติดตั้งทั้งสองรายการนี้ CLI และ backend เป็นระบบที่ทำงานได้ด้วยตัวเองและสามารถทำงานได้โดยไม่ต้องมีแพ็กเกจเหล่านี้ แต่ `import chloros_sdk` จำเป็นต้องมีแพ็กเกจเหล่านี้; ผลลัพธ์การติดตั้งจะระบุชื่อแพ็กเกจที่ขาดหายไป ให้เก็บ `./` ไว้, เพื่อให้ `apt` ติดตั้งไฟล์ดังกล่าวแทนที่จะค้นหาแพ็กเกจที่มีชื่อเดียวกัน แพ็กเกจนี้ยังประกาศ `libgl1` ซึ่ง OpenCV ที่มาพร้อมแพ็กเกจจะโหลดมาใช้ และภาพเซิร์ฟเวอร์แบบขั้นต่ำอาจไม่มี
{% endhint %}

ตรวจสอบการติดตั้ง:

```bash
chloros-cli --version    # prints "Chloros CLI 1.3.0"
```

<!-- SCREENSHOT-NEEDED: Terminal on Ubuntu 22.04 immediately after `sudo apt install ./chloros_1.3.0_amd64.deb`, showing the full postinst output: the "Chloros installed successfully!" banner, the Usage lines, the "Python SDK:" block ("chloros_sdk 1.3.0 for python3" and the "In a virtualenv: pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl" line), any "GPU Acceleration:" detection line, and the closing "Systemd Service (optional): sudo systemctl enable --now chloros-backend@<user>.service" hint -->

***

## Linux arm64 (NVIDIA Jetson)

### ข้อกำหนดของระบบ

| ข้อกำหนด | ขั้นต่ำ | แนะนำ |
| --- | --- | --- |
| **แพลตฟอร์ม** | NVIDIA Jetson พร้อม JetPack 6 | Jetson Orin NX 16GB หรือ AGX Orin |
| **JetPack** | JetPack 6.x | JetPack 6 เวอร์ชันล่าสุด |
| **หน่วยความจำ (RAM)** | 8GB (ใช้ร่วมกันระหว่าง GPU/CPU) | 16GB+ ใช้ร่วมกัน (12GB+ เป็นเกณฑ์ขั้นต่ำสำหรับการประมวลผลแบบขนาน) |
| **พื้นที่จัดเก็บ** | พื้นที่ว่าง 2GB | NVMe SSD ที่มีพื้นที่ว่าง 10GB+ |
| **Python** | Python 3.7+ (สำหรับ SDK SDK) | Python 3.10+ |

### การติดตั้ง

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
```

มีโครงสร้างที่คล้ายกับ amd64 `.deb` (รวม Python SDK, บัฟเฟอร์รับข้อมูล และบริการแบ็กเอนด์), สำหรับ Jetson Orin / Orin NX / Orin Nano บน **JetPack 6** (L4T R36). PyTorch ของมันคือเวอร์ชัน CUDA ที่ JetPack 6 สร้างขึ้นสำหรับ Orin ดังนั้นการประมวลผลด้วย GPU รวมถึงฟังก์ชัน Texture Aware debayer จะทำงานบน GPU ของ Jetson แพ็กเกจนี้ไม่รวม CUDA เอง: มันใช้ไลบรารี CUDA และ cuDNN ที่ JetPack 6 ติดตั้งไว้ (`sudo apt install nvidia-jetpack` บนภาพที่ไม่มีไลบรารีเหล่านี้) ในกรณีที่ไม่มี GPU CUDA ที่สามารถใช้งานได้ การประมวลผลจะทำงานบน CPU และ `--debayer texture-aware` จะใช้ Standard debayer (ส่วนหลังจะบันทึกบรรทัด `[DEBAYER] … needs a CUDA GPU …` หนึ่งบรรทัดที่อธิบายเหตุผล). สำหรับข้อมูลเกี่ยวกับหน่วยความจำ Jetson, การจัดการความร้อน และพฤติกรรมในการใช้งานจริงในสนาม โปรดดู [NVIDIA Jetson Guide](nvidia-jetson-guide.md).***

## การติดตั้ง Python SDK (Linux ทั้งหมด)**`.deb` จะติดตั้ง SDK ให้คุณ** ทั้งสองแพ็กเกจ (amd64 และ Jetson) จะติดตั้ง SDK สำหรับระบบ `python3` ดังนั้น `import chloros_sdk` จะทำงานได้ทันทีหลังการติดตั้ง โดยไม่จำเป็นต้องใช้ pip หรือเชื่อมต่อเครือข่าย แพ็กเกจของมัน (`chloros_sdk`, `lattice_sdk`, `lattice_cam`, `arena_api` และตัวโหลดโครงการ) จะถูกย้ายไปยัง `/usr/lib/python3/dist-packages` ซึ่งอยู่ภายใต้การควบคุมของแพ็กเกจนี้: การอัปเกรดจะแทนที่แพ็กเกจเหล่านี้ และการลบแพ็กเกจจะลบแพ็กเกจเหล่านี้ไปด้วย เวอร์ชันจะตรงกับCLI และ backend ที่คุณติดตั้งเสมอ

ข้อกำหนดของSDK เอง คือ `python3-numpy` และ `python3-requests` เป็น **สิ่งที่แนะนำ ไม่ใช่สิ่งที่จำเป็น** โดยแพ็กเกจ: `sudo apt install ./chloros_<version>_<arch>.deb` จะติดตั้งพวกมัน ส่วน `dpkg -i` จะไม่ติดตั้ง ผลลัพธ์การติดตั้งจะรายงานเวอร์ชันSDK ที่ `python3` นำเข้า รวมถึงข้อกำหนดใดที่ขาดหายไป, และ `chloros_sdk` ใดก็ตามที่อยู่ในเส้นทางของ `python3` ซึ่งทับซ้อนกับสำเนาของแพ็กเกจ (เช่น ที่ติดตั้งไว้ก่อนหน้านี้ด้วย `pip install --user`)**ใน virtualenv** ซึ่งไม่เห็นแพ็กเกจของระบบ ให้ติดตั้ง wheel ที่มาพร้อมแพ็กเกจเข้าไปในนั้น (แบบออฟไลน์อย่างสมบูรณ์):

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

**จาก PyPI** — เฉพาะสำหรับเครื่องที่ยังไม่ได้ติดตั้งแพ็กเกจ Chloros และ SDK ของเครื่องนั้นควบคุมแบ็กเอนด์บนเครื่องอื่น (ดู [Remote-Backend Mode](../reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel)):

```bash
pip install chloros-sdk
```

ส่วนเสริม (ไม่จำเป็น) สำหรับการติดตั้งผ่าน pip (ใน virtualenv หรือ PyPI):

| ส่วนเสริม | คำสั่ง | เพิ่ม |
| --- | --- | --- |
| `progress` | `pip install chloros-sdk[progress]` | `sseclient-py` สำหรับการสตรีมความคืบหน้าแบบเรียลไทม์ |
| `camera` | `pip install chloros-sdk[camera]` | `bleak` สำหรับการส่งข้อมูลผ่าน BLE (DAQ-M) |

ตรวจสอบSDK:

```bash
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
Python SDK ของ Python สื่อสารกับระบบหลังบ้านที่ `.deb` ติดตั้งไว้ผ่าน HTTP API ท้องถิ่น (`http://127.0.0.1:5000`) `ChlorosLocal` และตัวช่วยการเชื่อมต่อจะเริ่มต้นมันโดยอัตโนมัติเมื่อจำเป็น; `connect_all()` ของโครงการที่บันทึกไว้จะไม่ทำเช่นนั้นเลย, ดังนั้นบนเครื่องที่ไม่มีหน้าจอ ให้เรียกใช้ระบบหลังบ้านในฐานะ [บริการ](#always-on-ptp-for-headless-hosts). ให้ใช้ที่อยู่ IPv4 แบบตรงเสมอแทน `localhost` — `localhost` อาจถูกแปลงเป็น `::1` และใช้เวลาประมาณสองวินาทีต่อคำขอ
{% endhint %}

***

## การตั้งค่าครั้งแรก

### 1. ลงชื่อเข้าใช้

การเข้าถึงผ่าน CLI และ SDK จำเป็นต้องมีแพ็กเกจ Chloros+ แบบเสียค่าใช้จ่าย (**Copper** หรือสูงกว่า) ซึ่งถูกบังคับใช้ด้านเซิร์ฟเวอร์: ผู้เรียกที่ออกจากระบบจะได้รับ `401 AUTH_REQUIRED` ส่วนผู้เรียกที่ใช้แพ็กเกจฟรี (Iron) จะได้รับ `403 PLAN_UPGRADE_REQUIRED`.

```bash
chloros-cli login your@email.com 'your-password'
```

ข้อมูลการเข้าสู่ระบบจะถูกเก็บไว้ใน `~/.chloros/user_session.json`

{% hint style="info" %}
**การอัปเกรดจะรักษาข้อมูลการเข้าสู่ระบบของคุณไว้; การลบจะลบข้อมูลดังกล่าว** การติดตั้ง `.deb` เวอร์ชันใหม่กว่าทับการติดตั้งที่มีอยู่ (รวมถึงผ่าน `chloros-cli update`) จะรักษา `~/.chloros/user_session.json` และใบอนุญาตที่เก็บไว้ในแคชไว้ ดังนั้นงาน CLI/SDK แบบไม่มีอินเทอร์เฟซผู้ใช้ (headless) จะยังคงทำงานได้ การลบแพ็กเกจ (`sudo apt remove chloros`) จะลบข้อมูลการเข้าสู่ระบบที่บันทึกไว้และแคชใบอนุญาตสำหรับผู้ใช้ทุกคนบนเครื่อง ดังนั้นการติดตั้งใหม่จึงต้องติดตั้ง `chloros-cli login` อีกครั้ง
{% endhint %}

### 2. ตรวจสอบสถานะใบอนุญาต

```bash
chloros-cli status
```

`chloros-cli status` ทำงานได้กับทุกระดับ (รวมถึงเวอร์ชันฟรี) ดังนั้นคุณจึงสามารถตรวจสอบได้เสมอว่าทำไมการเข้าถึงจึงมีหรือไม่มี

### 3. ดำเนินการวินิจฉัยระบบ

```bash
chloros-cli selftest
```

มีการตรวจสอบ 7 ขั้นตอนตามลำดับ และคำสั่งจะออกค่าที่ไม่ใช่ศูนย์หากมีขั้นตอนใดล้มเหลว:

| # | การตรวจสอบ | สิ่งที่พิสูจน์ได้ |
| --- | --- | --- |
| 1 | **เวอร์ชัน** | CLI รายงานเวอร์ชันของตัวเอง (`v1.3.0`) |
| 2 | **พอร์ตว่าง** | พอร์ต 5000 ว่าง *หรือ* มีระบบหลังChloros ที่ทำงานปกติตอบกลับแล้ว (ซึ่งถือว่าผ่านการตรวจสอบ) |
| 3 | **การเริ่มต้นระบบหลัง** | โปรแกรมระบบหลังถูกเปิดใช้งาน |
| 4 | **การทดสอบ API (`/api/test`)** | Backend ตอบกลับด้วย `status: ok` |
| 5 | **ข้อมูลระบบ** | พิมพ์ `GPU: <name>, CUDA: <bool>, PyTorch: <version>` จาก `/api/system-info` |
| 6 | **โมเดล Denoiser** | ค้นหาโมเดล `*.pth.enc` (บนLinux: `/usr/lib/chloros/models`). |
| 7 | **CUDA + Denoiser**| Texture Aware สามารถใช้งานได้จริง — ต้องใช้ CUDA**และ** ไฟล์โมเดลอย่างน้อยหนึ่งไฟล์ |

การรันจะสิ้นสุดด้วย `N/7 checks passed` โดยแสดงรายการความล้มเหลวทั้งหมดตามชื่อ

### 4. ประมวลผลชุดข้อมูลแรกของคุณ

```bash
chloros-cli process ~/datasets/flight001
```

***

## ไฟล์และไดเรกทอรี

### สำหรับผู้ใช้แต่ละคน

Chloros เก็บข้อมูลรับรองและการตั้งค่า CLI ไว้ในไดเรกทอรีข้ามแพลตฟอร์มเดียว คือ **`~/.chloros/`** (บน Windows คือ `%USERPROFILE%\.chloros\`). ส่วนแคชสองตัวที่เฉพาะสำหรับLinux จะปฏิบัติตามมาตรฐาน XDG — ซึ่งจะใช้ `XDG_CONFIG_HOME` / `XDG_CACHE_HOME` เมื่อถูกตั้งค่า

| เส้นทาง | วัตถุประสงค์ |
| --- | --- |
| `~/.chloros/user_session.json` | แคชเซสชันการเข้าสู่ระบบที่เขียนโดย `chloros-cli login` (เก็บไว้แม้เมื่ออัปเกรดแพ็กเกจ; ล้างเมื่อลบแพ็กเกจ) |
| `~/.chloros/working_directory.txt` | การแทนที่โฟลเดอร์โครงการเริ่มต้น (`chloros-cli set-project-folder` / `get-project-folder` / `reset-project-folder`) |
| `~/.chloros/cli_language.json` | การตั้งค่าภาษาสำหรับ CLI (`chloros-cli language <code>`) |
| `~/.chloros/user.json` | การตั้งค่าภาษาที่ใช้ร่วมกับ GUI ของWindows — ค่า `language` ที่นี่มีลำดับความสำคัญสูงกว่า `cli_language.json` |
| `~/.chloros/update_cache.json` | แคชหนึ่งชั่วโมงสำหรับการตรวจสอบการอัปเดตเมื่อเริ่มต้นระบบLinux/Jetson |
| `~/.chloros/backend.log` | บันทึกของแบ็กเอนด์เมื่อแบ็กเอนด์ถูกเปิดโดยCLI |
| `~/.chloros/camera_cal/<serial>/<bundle_sha>/` | แพ็กเกจการปรับเทียบ LATTICE ที่เก็บไว้ในแคชสำหรับกล้องแต่ละตัว โดยใช้หมายเลขซีเรียลและแฮชของบันเดิลเป็นคีย์ |
| `~/.chloros/daq_cap_profiles/<u\|m\|e>/<cap_id>.json` | การแทนที่โปรไฟล์การแก้ไขขีดจำกัด DAQ โดยผู้ใช้ (เป็นตัวเลือก) |
| `~/.config/chloros/system_config.json` | โปรไฟล์ฮาร์ดแวร์ที่เก็บไว้ในแคชจาก Dynamic Compute Adaptation — ลบไฟล์นี้เพื่อบังคับให้ระบบตรวจจับฮาร์ดแวร์ใหม่ |
| `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` | บันทึกเซิร์ฟเวอร์แบ็กเอนด์ หนึ่งไฟล์ต่อการเริ่มต้น |
| `~/Documents/MAPIR/Chloros Projects/` | โฟลเดอร์โครงการเริ่มต้นเมื่อไม่มีการตั้งค่าทับ |

### ระดับระบบ

| เส้นทาง | วัตถุประสงค์ |
| --- | --- |
| `/usr/bin/chloros-cli` | สคริปต์ห่อหุ้ม — ตั้งค่า `LD_LIBRARY_PATH` สำหรับไลบรารีเนทีฟที่มาพร้อมชุด (และ `TMPDIR` เป็น `/mnt/ssd/tmp` เมื่อโฟลเดอร์นั้นมีอยู่) แล้วจึงเรียกใช้ไฟล์ไบนารีจริง |
| `/usr/bin/chloros-backend` | สคริปต์ห่อหุ้ม — ทำเช่นเดียวกันสำหรับส่วนหลัง (backend) โดยกำหนดให้มันเป็นเวอร์ชันผลิตที่ติดตั้งแล้ว |
| `/usr/lib/chloros/cli/`, `/usr/lib/chloros/backend/` | CLI และ backend ที่ถูกคอมไพล์แล้ว ติดตั้งแบบไม่บีบอัด (ใหม่ในเวอร์ชัน 1.3.0); โปรแกรมคือ `cli/chloros-cli` และ `backend/chloros-backend` |
| `/usr/lib/chloros/chloros-cli`, `/usr/lib/chloros/chloros-backend` | สคริปต์ตัวเปิดใช้งานสองบรรทัดที่เริ่มโปรแกรมจริงข้างต้น — เก็บไว้ที่เส้นทางเหล่านี้เพราะSDK, CLI และสคริปต์เวอร์ชันเก่าจะค้นหาโปรแกรมเหล่านี้ที่นี่ |
| `/usr/lib/chloros/arena_runtime/` | สภาพแวดล้อมการทำงานของ Arena SDK ที่กล้อง LATTICE ต้องการ |
| `/usr/lib/chloros/models/*.pth.enc` | แบบจำลองการลดสัญญาณรบกวนที่ถูกเข้ารหัส ซึ่งใช้โดย Texture Aware debayer |
| `/usr/lib/python3/dist-packages/chloros_sdk/` (และ `lattice_sdk`, `lattice_cam`, `arena_api` — โปรแกรมโหลดโครงการ) | Python SDK ที่ติดตั้งสำหรับระบบ `python3` และเป็นของแพ็กเกจ |
| `/usr/lib/chloros/sdk/chloros_sdk-*.whl` | Python SDK wheel ที่ตรงกับเวอร์ชันนี้อย่างแม่นยำ สำหรับ virtualenv |
| `/usr/lib/chloros/exiftool` | ตัวเรียกใช้ ExifTool ที่มาพร้อมแพ็กเกจ (สร้างลิงก์สัญลักษณ์ไปยัง `/usr/local/bin/exiftool` เฉพาะเมื่อไม่มี exiftool ของระบบ). ตั้งแต่เวอร์ชัน 1.3.0 สคริปต์ ExifTool มาพร้อมกับไลบรารี Perl ของตัวเองใน `/usr/lib/chloros/exiftool-dist/` ดังนั้นจึงสามารถทำงานได้โดยไม่ต้องใช้แพ็กเกจระบบ `libimage-exiftool-perl` |
| `/etc/chloros/update.conf` | การตั้งค่าช่องอัปเดตที่ `chloros-cli update` อ่าน |
| `/etc/sysctl.d/60-chloros-ptp.conf` | ตั้งค่า `net.ipv4.ip_unprivileged_port_start = 319` เพื่อให้ส่วนหลังสามารถผูกพอร์ต PTP ได้โดยไม่ต้องใช้สิทธิ์ root |
| `/etc/sysctl.d/60-chloros-gige.conf` | เพิ่มขนาดบัฟเฟอร์รับของซ็อกเก็ตในเคอร์เนล (`net.core.rmem_max`, `net.core.rmem_default`) เป็น 32 MB สำหรับสตรีมกล้อง LATTICE และนำไปใช้เมื่อติดตั้ง (ค่าที่ตั้งไว้สูงกว่าแล้วจะไม่ถูกเปลี่ยนแปลง) ตามค่าเริ่มต้นของเคอร์เนล แพ็กเก็ตจากกล้องจะถูกทิ้งในเคอร์เนล และอาร์เรย์จะสูญเสียเฟรมและอัตราเฟรม; `chloros-cli lattice network` จะแจ้งเตือนเมื่อบัฟเฟอร์เหลือน้อย ดู [เครื่องโฮสต์ [Linux] — บัฟเฟอร์รับข้อมูลผ่านซ็อกเก็ต](../reference/cli-reference.md#linux-hosts--socket-receive-buffers) |
| `/etc/ld.so.conf.d/Arena_SDK.conf` | ชี้ตัวโหลดแบบไดนามิกไปยัง `/usr/lib/chloros/arena_runtime` |
| `/lib/udev/rules.d/70-chloros-daq.rules` | ให้สิทธิ์ผู้ใช้ที่เข้าสู่ระบบเข้าถึงสะพานซีเรียล USB DAQ-U (CP2102N, `10c4:ea60`) |
| `/lib/systemd/system/chloros-backend@.service` | บริการแบ็กเอนด์แบบเปิดใช้งานตลอดเวลา (Opt-in) ที่ทำงานแบ็กเอนด์ในฐานะผู้ใช้ที่คุณกำหนดชื่อ (ติดตั้งแล้ว, **ยังไม่ได้เปิดใช้งาน**) — ตัวนี้ต้องเปิดใช้งาน ดู [Always-On PTP](#always-on-ptp-for-headless-hosts) |
| `/lib/systemd/system/chloros-backend.service` | บริการเดียวกันที่ทำงานแบ็กเอนด์ในฐานะ **root**(ติดตั้งแล้ว,**ยังไม่ได้เปิดใช้งาน**), เก็บไว้เพื่อให้เครื่องที่เปิดใช้งานบริการนี้ยังคงทำงานต่อไป |
| `/var/log/chloros/` | บันทึกรายละเอียดของบริการ root ซึ่งกลุ่ม `adm` สามารถอ่านได้ (ถูกลบเมื่อแพ็กเกจถูกล้าง) |
| `/usr/share/applications/chloros-cli.desktop` | รายการเมนูแอปพลิเคชัน &quot;Chloros CLI&quot; ที่เปิดเทอร์มินัล |

**ไฟร์วอลล์ (ใหม่ในเวอร์ชัน 1.3.0).** หาก `ufw` หรือ `firewalld` กำลังทำงานอยู่ขณะติดตั้ง แพ็กเกจจะเปิดพอร์ต UDP `5002` (ข้อมูล DAQ-E), `5353` (การค้นพบ mDNS), `319` และ `320` (PTP) และจะลบกฎเหล่านั้นเมื่อลบแพ็กเกจออก หากมีไฟร์วอลล์ติดตั้งไว้แต่ยังไม่เปิดใช้งาน ระบบจะไม่เพิ่มกฎใดๆ และจะแสดงรายการพอร์ตที่ควรอนุญาตไว้เพื่อใช้ในกรณีที่คุณเปิดใช้งานไฟร์วอลล์ในภายหลัง ระบบจะไม่เปิดพอร์ต TCP ใดๆ: การอัปเดตเฟิร์มแวร์ DAQ-E ซึ่งเซ็นเซอร์จะเชื่อมต่อกลับมายังคอมพิวเตอร์นี้ผ่าน TCP เพื่อเก็บภาพ จะล้มเหลวด้วยรหัสข้อผิดพลาด `… device never connected back on TCP <port> …` หากมีไฟร์วอลล์ที่เปิดใช้งานอยู่และบล็อกการเชื่อมต่อดังกล่าว

## ตำแหน่งไฟล์ปฏิบัติการของ Backend

CLI และ SDK จะตรวจจับ backend โดยอัตโนมัติ:

| Component | Path |
| --- | --- |
| CLI | `/usr/bin/chloros-cli` |
| Backend | `/usr/lib/chloros/chloros-backend` (โปรแกรมเปิดตัวสำหรับ `/usr/lib/chloros/backend/chloros-backend`) |

สามารถกำหนดเส้นทางของแบ็กเอนด์ใหม่ได้โดยใช้แฟล็ก `--backend-exe` ของCLI หรือพารามิเตอร์คอนสตรัคเตอร์ `backend_exe` ของSDK, และกำหนดพอร์ตด้วย `--port` (ค่าเริ่มต้นคือ `5000`)

{% hint style="info" %}
`CHLOROS_BACKEND_URL` ชี้ไปยัง **`lattice`**,**`project`**และ**`daq pool-*`** ที่แบ็กเอนด์ระยะไกล คำสั่งหลัก (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) จะเพิกเฉยต่อมันโดยเจตนาและกำหนดเป้าหมายไปที่ `http://127.0.0.1:<port>` เสมอ
{% endhint %}

***

## กล้อง LATTICE และเซ็นเซอร์แสง DAQ บนระบบLinux

กลุ่มคำสั่ง live-hardware ทั้งหมดทำงานได้บนระบบLinux (amd64 และ Jetson):

* **`chloros-cli lattice`** — ค้นหา เชื่อมต่อ ตั้งค่า และจับภาพจากกล้อง LATTICE และอาร์เรย์ที่ซิงโครไนซ์ `.deb` รวมรันไทม์ Arena SDK ที่จำเป็นและลงทะเบียนกับไดนามิกโหลดเดอร์
* **`chloros-cli daq pool-*`** — เชื่อมต่อเซ็นเซอร์แสง DAQ-U/M/E ผ่าน backend pool, ส่งสเปกตรัมที่ปรับเทียบแล้ว, บันทึกไฟล์ `.daq` และสลับโปรไฟล์ cap รวมถึงสำหรับ DAQ-E สามารถเปลี่ยนการตั้งค่าเครือข่าย, รีสตาร์ท และอัปเดตเฟิร์มแวร์ได้ `chloros-cli daq discover` สแกนหาเซ็นเซอร์ที่คุณสามารถเชื่อมต่อได้ และ `chloros-cli daq --help` แสดงรายการคำสั่งย่อยของ `pool-*`
* **`chloros-cli project`** — ควบคุมโครงการที่บันทึกไว้ (รวมถึงกล้อง เซ็นเซอร์ และการตั้งค่าการประมวลผล) แบบ headless
* **`chloros-cli time-sync`** — ตรวจสอบ PTP grandmaster ที่ backend Chloros ทำงานสำหรับกล้อง LATTICE และเซ็นเซอร์ DAQ-E

```bash
# DAQ-E at a known address — the reliable path on multi-homed hosts
chloros-cli daq pool-connect --eth-host 192.168.2.50

# DAQ-U over USB serial
chloros-cli daq pool-connect --port /dev/ttyUSB0

# What is connected, then the latest calibrated spectrum as JSON
chloros-cli daq pool-list
chloros-cli daq pool-latest --sensor-id daq-e-a1b2c3 --json
```

`--sensor-id` เป็นคำสั่งที่จำเป็นสำหรับ `pool-latest`, `pool-stream`, `pool-record` และ `pool-set-cap`; `pool-list` แสดง ID ที่อยู่ในพูลปัจจุบัน

{% hint style="info" %}
**`--eth-host` ข้ามขั้นตอนการค้นหา.** `pool-connect --eth` ค้นหา DAQ-E ผ่าน mDNS พร้อมด้วยกลไกสำรอง ARP ที่ทำงานได้แม้จากแคช ARP ที่ว่างเปล่า; การส่ง IP หรือชื่อโฮสต์ของเซ็นเซอร์ผ่าน `--eth-host` จะเชื่อมต่อกับมันโดยตรง
{% endhint %}

**สิทธิ์การเชื่อมต่อแบบอนุกรมของ DAQ-U** ถูกจัดการโดยกฎ udev ที่ติดตั้งไว้ (`uaccess` + กลุ่ม `dialout`) หากเซ็นเซอร์ที่เสียบไว้แล้วไม่สามารถเข้าถึงได้ ให้โหลดกฎใหม่หรือเสียบเซ็นเซอร์ใหม่:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=tty
```

ดู [คู่มืออ้างอิง [CLI]](../CLI.md) เพื่อดูชุดคำสั่งทั้งหมด

### PTP ที่ทำงานตลอดเวลาสำหรับโฮสต์ที่ไม่มีหน้าจอ

แพ็กเกจนี้จะติดตั้งบริการ systemd สองตัวสำหรับส่วนหลัง (backend) แต่ทั้งสองยังไม่ถูกเปิดใช้งาน บน Jetson หรือเซิร์ฟเวอร์แบบ headless ที่จำเป็นต้องให้ระบบซิงค์เวลา PTP ทำงานอย่างต่อเนื่องสำหรับเซ็นเซอร์ DAQ-E และกล้อง LATTICE หรือในกรณีที่สคริปต์SDK ใช้เพื่อเชื่อมต่อโครงการที่บันทึกไว้ (`connect_all()` ไม่เคยเริ่มต้น backend), ให้เปิดใช้งานบริการ **ด้วยบัญชีผู้ใช้ของคุณ**:

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
systemctl status chloros-backend@$USER.service
```

`chloros-backend@<user>.service` จะทำงานแบ็กเอนด์ในฐานะผู้ใช้ดังกล่าว: ข้อมูลที่บันทึกเป็นของผู้ใช้ดังกล่าว, บันทึกรายละเอียดอยู่ใน `~/.cache/chloros/logs` ของผู้ใช้ดังกล่าว, และใช้ `chloros-cli login` (`~/.chloros`) ดังนั้น ให้ลงชื่อเข้าใช้ด้วยผู้ใช้ดังกล่าวก่อนหนึ่งครั้ง การติดตั้งแพ็กเกจจะแสดงคำสั่งนี้พร้อมชื่อผู้ใช้ของคุณที่ถูกกรอกไว้แล้ว

หากไม่มีบริการ ระบบแบ็กเอนด์ (รวมถึง PTP grandmaster) จะทำงานเฉพาะเมื่อมีสิ่งใดนั้นเริ่มต้นมัน — เช่น คำสั่ง `chloros-cli` หรือสคริปต์ SDK

อุปกรณ์นี้จะผูกระบบแบ็กเอนด์กับ `127.0.0.1:5000` (การตั้งค่าสภาพแวดล้อม `CHLOROS_HOST` / `CHLOROS_PORT` ภายในหน่วย; สามารถแทนที่ด้วย `sudo systemctl edit chloros-backend@.service`) และจะรีสตาร์ทมันใหม่หลังจาก 5 วินาทีหากเกิดข้อผิดพลาด

**บริการ root**`chloros-backend.service` ทำงาน backend ในฐานะ**root** มันยังคงถูกติดตั้งไว้เพื่อให้เครื่องที่เปิดใช้งานมันยังคงทำงานต่อไปได้, แต่ทุกสิ่งที่แบ็กเอนด์ root เขียนจะอยู่ในสิทธิ์ของ root ดังนั้นผู้ใช้จึงไม่สามารถลบข้อมูลที่บันทึกไว้ของตนเองได้; บันทึกของมันอยู่ใน `/var/log/chloros` ซึ่งกลุ่ม `adm` สามารถอ่านได้ เพื่อเปลี่ยนไปใช้บัญชีผู้ใช้ของคุณ:

```bash
sudo systemctl disable --now chloros-backend.service
sudo systemctl enable --now chloros-backend@$USER.service
```

**แบ็กเอนด์ของบริการเป็นแบบแชร์ และไม่เคยถูกหยุดโดยไคลเอนต์** สคริปต์ `chloros-cli` และSDK ใช้มันตามที่พบ: `process` ทำงานบนมัน โดยโหลดโครงการของตัวเองมาแทนที่โครงการสุดท้าย ในกรณีที่CLI จะรีสตาร์ทแบ็กเอนด์ (`--restart` หรือ `process` ที่การนำเข้าไม่พบอะไร) มันจะตั้งชื่อหน่วยนั้น พิมพ์ `sudo systemctl restart <unit>` แล้วออกจาก `1` เพราะแบ็กเอนด์ที่ถูกหยุดด้วยวิธีนี้จะยังคงไม่ทำงานต่อไป ให้รีสตาร์ทด้วยตนเองโดยใช้ `sudo systemctl restart chloros-backend@$USER.service` แบ็กเอนด์ของบริการจะไม่หยุดทำงานเองเมื่ออยู่ในสถานะว่าง และ `chloros-cli backend stop` จะปฏิเสธการหยุดนั้น โดยระบุชื่อคำสั่ง `systemctl stop`**วิธีที่ PTP ได้รับพอร์ต** PTP ใช้ UDP 319/320 ซึ่งทั้งสองพอร์ตอยู่ต่ำกว่าขีดจำกัดปกติของพอร์ตพิเศษที่ 1024 แพ็กเกจ `postinst` จะเขียนค่า `/etc/sysctl.d/60-chloros-ptp.conf` ด้วย `net.ipv4.ip_unprivileged_port_start = 319` ซึ่งทำให้ส่วนหลังสามารถผูกพอร์ตเหล่านั้นได้ขณะทำงานในฐานะผู้ใช้ของคุณ ตั้งแต่เวอร์ชัน 1.3.0 แพ็กเกจนี้ไม่ตั้งค่าความสามารถของไฟล์ (`setcap`) บนแบ็กเอนด์ และ `libcap2-bin` ไม่เป็นข้อพึ่งพาอีกต่อไป: บนแบ็กเอนด์ที่ยังไม่ได้แพ็ก ความสามารถของไฟล์จะทำให้มันไม่สามารถเริ่มต้นได้ ดังนั้นอย่าเพิ่มสิทธิ์ดังกล่าวด้วยตนเอง***

## ตัวอย่างการเขียนสคริปต์ Bash

{% hint style="info" %}
**รหัสออกที่เหมาะสำหรับการเขียนสคริปต์**`chloros-cli process` จะออก `0` เมื่อสำเร็จ และ**มีค่าไม่เท่ากับศูนย์เมื่อล้มเหลว — รวมถึงการรันที่ขอผลิตภัณฑ์ภาพแต่ไม่เขียนอะไรเลย** (มันจะพิมพ์ `Processing finished but wrote no image products.` พร้อมทั้งชื่อโฟลเดอร์โครงการและสาเหตุทั่วไป). การทำงานที่สำเร็จจะรายงานจำนวนผลิตภัณฑ์ภาพที่เขียนออกมา (`Image products written: N`, พร้อมทั้ง `(plus M clamp masks)` เมื่อเขียนมาสก์). รหัสออก: `0` สำเร็จ, `1` ล้มเหลว, `2` ข้อผิดพลาดของอาร์กิวเมนต์, `130` ถูกขัดจังหวะ.
{% endhint %}

### ประมวลผลชุดข้อมูลหลายชุด

```bash
#!/bin/bash
for dataset in ~/datasets/2026/*/; do
    echo "Processing $(basename "$dataset")..."
    if chloros-cli process "$dataset" --format "TIFF (32-bit, Percent)"; then
        echo "Done: $(basename "$dataset")"
    else
        echo "FAILED: $(basename "$dataset")" >&2
    fi
done
```

### ประมวลผลด้วยตั้งค่าที่กำหนดเอง

```bash
#!/bin/bash
chloros-cli process ~/datasets/field_a \
    --output ~/output/field_a \
    --format "TIFF (32-bit, Percent)" \
    --indices NDVI GNDVI OSAVI \
    --debayer texture-aware \
    --no-vignette
```

ค่า `--format` ที่ถูกต้องมีเพียงสี่ค่าเท่านั้น และแต่ละค่ามีช่องว่าง — ต้องใส่เครื่องหมายคำพูดรอบค่าเสมอ:

| ค่า `--format` | โฟลเดอร์ผลลัพธ์ |
| --- | --- |
| `TIFF (16-bit)` *(ค่าเริ่มต้น)* | `tiff16` |
| `TIFF (8-bit)` | `tiff8` |
| `TIFF (32-bit, Percent)` | `tiff32` |
| `PNG (8-bit)` | `png8` |
| `JPG (8-bit)` | `jpg8` |

`--debayer` รับค่า `standard` (ค่าเริ่มต้น) หรือ `texture-aware` (Chloros+).

### การประมวลผลอัตโนมัติด้วย Cron

```cron
# Process any new datasets at 2 AM daily
0 2 * ** /usr/bin/chloros-cli process /data/incoming --output /data/processed >> /var/log/chloros.log 2>&1
```

### ตัวอย่าง PythonSDK

```python
from chloros_sdk import process_folder

# One-line processing
result = process_folder(
    "/home/user/datasets/flight001",
    indices=["NDVI", "OSAVI"],
    export_format="TIFF (32-bit, Percent)"
)
```

***

## การแก้ไขปัญหา

### ไม่พบ CLI หลังการติดตั้ง

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# List everything the package installed
dpkg -L chloros

# Reload your shell
source ~/.bashrc
```

### ถูกปฏิเสธสิทธิ์

```bash
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend /usr/lib/chloros/backend/chloros-backend
```

### Backend ไม่สามารถเริ่มต้นได้หลังจากเพิ่มสิทธิ์ไฟล์

อย่าเรียกใช้ `setcap` บน backend. ตั้งแต่เวอร์ชัน 1.3.0 แพ็กเกจนี้ไม่ตั้งค่าความสามารถของไฟล์ และพอร์ต 319/320 ของ PTP มาจากการตั้งค่า `sysctl` ที่อธิบายไว้ใน [วิธีที่ PTP ได้รับพอร์ต](#always-on-ptp-for-headless-hosts). ความสามารถของไฟล์จะทำให้แบ็กเอนด์ที่ยังไม่ถูกแพ็กอยู่ในโหมดการทำงานที่ปลอดภัย ซึ่งทำให้มันไม่สามารถค้นหาไลบรารีของตัวเองได้ จึงไม่เริ่มทำงานได้ หากคุณได้เพิ่มความสามารถนี้ไว้ ให้ลบมันออก:

```bash
sudo setcap -r /usr/lib/chloros/backend/chloros-backend
```

### PTP ไม่เริ่มต้น / ไม่สามารถผูกพอร์ต 319 ได้

ตรวจสอบว่าค่าขั้นต่ำของพอร์ตที่ไม่มีสิทธิ์พิเศษ (unprivileged-port floor) ได้ถูกลดลงแล้วหรือไม่ และหากยังไม่ได้ ให้ปรับใช้ใหม่สำหรับการบูตปัจจุบัน:

```bash
sysctl net.ipv4.ip_unprivileged_port_start     # expect 319
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=319
```

จากนั้นตรวจสอบ grandmaster:

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
```

### คำสั่ง `lattice` ล้มเหลวด้วยข้อความ &quot;ไม่พบไดรเวอร์กล้อง LATTICE&quot;

แบ็กเอนด์ไม่สามารถโหลด runtime ของกล้อง LATTICE ที่ติดตั้งมาพร้อมกับมันได้ ทุกคำสั่ง `lattice` ทำงานบนแบ็กเอนด์; ส่วนCLI เองไม่บรรจุไดรเวอร์กล้องใดๆ ติดตั้งChloros ใหม่ (`sudo apt install --reinstall ./chloros_1.3.0_<arch>.deb`): `.deb` จะติดตั้ง runtime ไว้ข้างๆ backend ใน `/usr/lib/chloros/arena_runtime` และลงทะเบียนมันกับ dynamic loader เพื่อตรวจสอบการลงทะเบียนนั้น:

```bash
cat /etc/ld.so.conf.d/Arena_SDK.conf     # expect /usr/lib/chloros/arena_runtime
ls /usr/lib/chloros/arena_runtime | head
```

### Backend Failed to Start

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

บันทึกของแบ็กเอนด์สำหรับการเริ่มต้นที่ล้มเหลวอยู่ใน `~/.cache/chloros/logs/` หากพอร์ต 5000 เป็นของบริการแบ็กเอนด์ (`systemctl status chloros-backend@$USER.service`) ให้รีสตาร์ทบริการแทนที่จะยุติกระบวนการของมัน

### ไม่ตรวจพบ CUDA

```bash
# Check NVIDIA driver installation (x86_64)
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check the L4T release (JetPack 6 is R36) and that JetPack's CUDA libraries are installed
cat /etc/nv_tegra_release
sudo apt install nvidia-jetpack
```

`chloros-cli selftest` รายงานเรื่องเดียวกันในบรรทัดเดียว: `GPU: <name>, CUDA: <bool>, PyTorch: <version>`.

### ขาดไลบรารีที่ใช้ร่วมกัน

```bash
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries (the program itself, not the launcher script)
ldd /usr/lib/chloros/backend/chloros-backend | grep "not found"
```

### การเริ่มต้นช้าบนระบบที่ใช้การ์ด SD

ตั้งแต่เวอร์ชัน 1.3.0 เป็นต้นไป บริการแบ็กเอนด์และCLI จะถูกติดตั้งในสภาพที่ยังไม่ถูกแตกไฟล์ ดังนั้นจึงไม่จำเป็นต้องแตกไฟล์ไปยังไดเรกทอรีชั่วคราวทุกครั้งที่เริ่มต้นระบบอีกต่อไป ไฟล์ชั่วคราวจะถูกย้ายไปยัง `/mnt/ssd/tmp` โดยอัตโนมัติหากโฟลเดอร์นั้นมีอยู่; หากไม่มี คุณสามารถกำหนดให้ไฟล์เหล่านั้นถูกจัดเก็บในระบบไฟล์ที่เร็วได้:

```bash
export TMPDIR=/mnt/nvme/tmp
```

***

## การอัปเดตChloros บน Linux

คำสั่ง `update` ใช้ได้เฉพาะบน Linux/Jetson เท่านั้น คำสั่งนี้จะตรวจสอบเวอร์ชันที่เผยแพร่ในช่องอัปเดตที่ตั้งค่าไว้ที่ `/etc/chloros/update.conf` และเสนอให้ดาวน์โหลดและติดตั้ง `.deb` ที่ตรงกัน:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

บนLinux/Jetson CLI จะดำเนินการตรวจสอบการอัปเดตแบบไม่บล็อกทุกครั้งที่เริ่มต้นระบบ (ผลลัพธ์ถูกเก็บไว้ในแคชเป็นเวลาหนึ่งชั่วโมงใน `~/.chloros/update_cache.json`) และแสดงผล `Update available: vX.Y.Z` เมื่อมีเวอร์ชันใหม่กว่า การตั้งค่า โครงการ และการเข้าสู่ระบบของคุณจะยังคงอยู่หลังการอัปเดต

การอัปเกรดแพ็กเกจจะหยุดบริการแบ็กเอนด์ทั้งหมดระหว่างการติดตั้ง ดังนั้นสคริปต์ที่กำลังทำงานอยู่จะสูญเสียการเชื่อมต่อกับกล้องและเซ็นเซอร์ และจะเริ่มทำงานใหม่สำหรับแต่ละบริการที่เปิดใช้งาน (บริการที่เริ่มทำงานด้วยมือแต่ไม่เคยเปิดใช้งานจะยังคงหยุดอยู่ เช่นเดียวกับหลังการรีบูต)

## การถอนการติดตั้ง

```bash
sudo apt remove chloros
```

การถอนการติดตั้งจะหยุดและปิดใช้งานบริการแบ็กเอนด์ทั้งหมด คืนค่าพอร์ตเริ่มต้นที่ไม่มีสิทธิ์พิเศษ (1024) ลบไฟล์การตั้งค่าบัฟเฟอร์รับ (ค่าที่กำลังทำงานอยู่จะคงอยู่จนกว่าจะรีบูต) ลิงก์สัญลักษณ์ของ exiftool ที่มาพร้อมแพ็กเกจ, ไฟล์กำหนดค่า Arena loader, กฎไฟร์วอลล์ที่แพ็กเกจเพิ่มเข้ามา และ Python SDK ที่ติดตั้งไว้ รวมถึงล้างข้อมูลรับรองที่เก็บไว้ในแคช โครงการของคุณและไฟล์ข้อมูล `~/.chloros/` จะไม่ถูกลบ `sudo apt purge chloros` ยังลบ `/var/log/chloros` ด้วย

***

## ขั้นตอนต่อไป

* [คู่มือ NVIDIA Jetson](nvidia-jetson-guide.md) — การปรับแต่งและติดตั้งเฉพาะสำหรับ Jetson
* [CLI : Command Line](../CLI.md) — คู่มือ CLI
* [API : Python SDK](../api-python-sdk.md) — คู่มือ SDK
* [คู่มืออ้างอิง CLI](../reference/cli-reference.md) และ [คู่มืออ้างอิง SDK](../reference/sdk-reference.md) — รายการคำสั่ง/API ที่ครบถ้วนสำหรับเวอร์ชัน 1.3.0
* [การปรับแต่งการประมวลผลแบบไดนามิก](../processing-architecture/dynamic-compute-adaptation.md) — วิธีที่Chloros ปรับตัวให้เข้ากับฮาร์ดแวร์ของคุณ
