# การติดตั้งLinux

Chloros ถูกแจกจ่ายสำหรับ Linux ในรูปแบบแพ็กเกจ `.deb` ซึ่งติดตั้ง CLI และเซิร์ฟเวอร์แบ็กเอนด์ ส่วน Python SDK เป็นแพ็กเกจ pip แยกต่างหาก (ซึ่งถูกรวมไว้ใน `.deb` ในรูปแบบ wheel ที่ตรงกับเวอร์ชัน)

ชื่อไฟล์แพ็กเกจจะระบุเวอร์ชันและสถาปัตยกรรม: `chloros_1.2.0_amd64.deb` สำหรับ x86_64 และ `chloros_1.2.0_arm64_jp6.deb` สำหรับ JetPack 6 Jetson builds. ให้แทนที่ด้วยชื่อไฟล์ที่คุณดาวน์โหลดมาจริงในคำสั่งด้านล่าง

***

## Linux amd64 (x86_64)

### ข้อกำหนดระบบ

| ข้อกำหนด | ขั้นต่ำ | แนะนำ |
| --- | --- | --- |
| **ระบบปฏิบัติการ** | Ubuntu 22.04 LTS+ / Debian 12+ | Ubuntu 24.04 LTS |
| **โปรเซสเซอร์** | x86_64 (Intel/AMD) | Intel Core i7 หรือดีกว่า |
| **หน่วยความจำ (RAM)** | 8GB | 16GB หรือมากกว่า |
| **การ์ดกราฟิก** | ไม่จำเป็น (ใช้การประมวลผลของ CPU) | NVIDIA GPU ที่มี VRAM 4GB ขึ้นไป (12GB ขึ้นไปจะปลดล็อก `GPU_PARALLEL`, 7GB ขึ้นไปจะปิด Texture Aware ในเส้นทางภาพเดียว) |
| **พื้นที่จัดเก็บ** | พื้นที่ว่าง 2GB | SSD ที่มีพื้นที่ว่าง 10GB ขึ้นไป |
| **ระบบปฏิบัติการ Python** | Python 3.7 ขึ้นไป (สำหรับ SDK) | Python 3.10 ขึ้นไป |

> **Ubuntu 20.04 และ Debian 11 ไม่ได้รับการสนับสนุน** รายการความพึ่งพาของ `.deb`
> มาจากสิ่งที่ backend ของ Chloros เชื่อมโยงจริง ๆ ซึ่งรวมถึง
> `libc6 (>= 2.34)` Focal และ bullseye ทั้งสองเวอร์ชันมาพร้อมกับ glibc 2.31 ดังนั้น `apt` จึงปฏิเสธการ
> ติดตั้งทันที แทนที่จะปล่อยให้ล้มเหลวในภายหลังขณะทำงาน

### การติดตั้ง

```bash
sudo dpkg -i chloros_1.2.0_amd64.deb
sudo apt-get install -f    # pulls the declared dependencies (libibverbs1, libcap2-bin)
```

{% hint style="info" %}
`dpkg -i` ไม่สามารถแก้ไขความพึ่งพาได้ หากรายงานว่ามีแพ็กเกจขาด `sudo apt-get install -f` (หรือ `sudo apt --fix-broken install`) จะดำเนินการติดตั้งให้เสร็จสิ้น — นี่เป็นขั้นตอนปกติ ไม่ใช่ข้อผิดพลาด
{% endhint %}

ตรวจสอบการติดตั้ง:

```bash
chloros-cli --version    # prints "Chloros CLI 1.2.0"
```



<!-- SCREENSHOT-NEEDED: Terminal on Ubuntu 22.04 immediately after `sudo dpkg -i chloros_1.2.0_amd64.deb`, showing the full postinst output: the "Chloros installed successfully!" banner, the Usage lines, the "Python SDK:" block naming the bundled wheel path under /usr/lib/chloros/sdk/, any "GPU Acceleration:" detection line, and the closing "Systemd Service (optional): sudo systemctl enable --now chloros-backend.service" hint -->***

## Linux arm64 (NVIDIA Jetson)

### ข้อกำหนดระบบ

| ข้อกำหนด | ขั้นต่ำ | แนะนำ |
| --- | --- | --- |
| **แพลตฟอร์ม** | NVIDIA Jetson พร้อม JetPack 6 | Jetson Orin NX 16GB หรือ AGX Orin |
| **JetPack** | JetPack 6.x | JetPack 6 เวอร์ชันล่าสุด |
| **หน่วยความจำ (RAM)** | 8GB (ใช้ร่วมกันระหว่าง GPU/CPU) | 16GB+ ใช้ร่วมกัน (12GB+ เป็นเกณฑ์ขั้นต่ำสำหรับ GPU workers แบบขนาน) |
| **พื้นที่จัดเก็บ** | พื้นที่ว่าง 2GB | NVMe SSD ที่มีพื้นที่ว่าง 10GB+ |
| **Python** | Python 3.7+ (สำหรับ SDK) | Python 3.10+ |

### การติดตั้ง

```bash
sudo dpkg -i chloros_1.2.0_arm64_jp6.deb
sudo apt-get install -f
chloros-cli --version
```

มีโครงสร้างที่คล้ายกับ amd64 `.deb` แต่ใช้เวอร์ชัน CUDA ที่ปรับแต่งมาสำหรับ Jetson Orin / Orin NX / Orin Nano สำหรับข้อมูลเกี่ยวกับหน่วยความจำ Jetson, การจัดการความร้อน และพฤติกรรมในการใช้งานจริง โปรดดู [NVIDIA Jetson Guide](nvidia-jetson-guide.md).

***

## การติดตั้ง Python SDK (Linux ทั้งหมด)

SDK เป็นไคลเอนต์แบบบริสุทธิ์ Python HTTP สำหรับแบ็กเอนด์ ดังนั้นแพ็กเกจเดียวกันนี้จึงทำงานได้ทั้งบน amd64 และ arm64 มีสองแหล่งที่มา:**จาก PyPI** — เวอร์ชันเสถียรที่เผยแพร่แล้ว:

```bash
pip install chloros-sdk
```

**จากไฟล์ wheel ที่มาพร้อมแพ็กเกจ** — รับประกันว่าจะตรงกับ CLI /backend ที่คุณเพิ่งติดตั้ง (ใช้ตัวเลือกนี้เมื่อ `.deb` ของคุณใหม่กว่าเวอร์ชันใน PyPI):

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

{% hint style="warning" %}
**ระบบปฏิบัติการตาม PEP 668** (Ubuntu 23.10+, Debian 12+) ไม่อนุญาตให้ติดตั้ง pip ทั่วระบบ ใช้ `pip install --user …`, สภาพแวดล้อมเสมือน (virtual environment), หรือ `sudo pip install --break-system-packages …` โปรแกรมติดตั้งแพ็กเกจจะไม่ติดตั้ง SDK ลงในระบบของคุณโดยอัตโนมัติ Python — การตัดสินใจนี้ขึ้นอยู่กับคุณ
{% endhint %}

ส่วนเสริมที่เลือกได้:

| ส่วนเสริม | คำสั่ง | เพิ่ม |
| --- | --- | --- |
| `progress` | `pip install chloros-sdk[progress]` | `sseclient-py` สำหรับการสตรีมความคืบหน้าแบบเรียลไทม์ |
| `camera` | `pip install chloros-sdk[camera]` | `bleak` สำหรับการส่งข้อมูลแบบ BLE (DAQ-M) |

ตรวจสอบ SDK:

```bash
python -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
`.deb` จะติดตั้ง Chloros CLI และ backend ส่วน Python SDK จะสื่อสารกับ backend ดังกล่าวผ่าน local HTTP API (`http://127.0.0.1:5000`) และจะเริ่มต้นระบบนั้นโดยอัตโนมัติเมื่อจำเป็น ให้ใช้ที่อยู่ IPv4 แบบตัวอักษรเสมอ แทนที่จะใช้ `localhost` — `localhost` อาจถูกแปลงเป็น `::1` และใช้เวลาประมาณสองวินาทีต่อคำขอ
{% endhint %}

***

## การตั้งค่าครั้งแรก

### 1. ลงชื่อเข้าใช้

การเข้าถึง CLI และ SDK จำเป็นต้องมีแพ็กเกจ Chloros+ แบบเสียเงิน (**Copper** หรือสูงกว่า) ซึ่งถูกบังคับใช้ด้านเซิร์ฟเวอร์: ผู้เรียกที่ออกจากระบบจะได้รับ `401 AUTH_REQUIRED` ส่วนผู้เรียกที่ใช้แพ็กเกจฟรี (Iron) จะได้รับ `403 PLAN_UPGRADE_REQUIRED`.

```bash
chloros-cli login your@email.com 'your-password'
```

ข้อมูลรับรองถูกเก็บไว้ในแคชที่ `~/.chloros/user_session.json`

{% hint style="warning" %}
**คุณต้องเข้าสู่ระบบใหม่ทุกครั้งหลังการติดตั้งหรืออัปเกรด** สคริปต์ `prerm` ของแพ็กเกจจะล้างข้อมูลใน `~/.chloros/user_session.json` และใบอนุญาตที่ถูกเก็บไว้ในแคชสำหรับผู้ใช้ทุกคนบนเครื่องอย่างตั้งใจ เพื่อให้การสร้างระบบใหม่จะตรวจสอบความถูกต้องของใบอนุญาตเสมอ แทนที่จะเชื่อถือข้อมูลแคชที่ล้าสมัย
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

มีการตรวจสอบ 7 ขั้นตอนตามลำดับ และคำสั่งจะออกค่าที่ไม่เป็นศูนย์หากมีขั้นตอนใดล้มเหลว:

| # | การตรวจสอบ | ผลลัพธ์ |
| --- | --- | --- |
| 1 | **เวอร์ชัน** | CLI รายงานเวอร์ชันของตัวเอง (`v1.2.0`) |
| 2 | **พอร์ตว่าง** | พอร์ต 5000 ว่าง *หรือ* มี backend Chloros ที่ทำงานปกติตอบรับแล้ว (ซึ่งถือว่าผ่านการตรวจสอบ) |
| 3 | **การเริ่มต้นทำงานของ backend** | โปรแกรมไบนารีของ backend เริ่มทำงาน |
| 4 | **การทดสอบ API (`/api/test`)** | backend ตอบกลับด้วย `status: ok` | |
| 5 | **ข้อมูลระบบ** | พิมพ์ `GPU: <name>, CUDA: <bool>, PyTorch: <version>` จาก `/api/system-info` |
| 6 | **โมเดล Denoiser** | ค้นพบโมเดล `*.pth.enc` (บน Linux: `/usr/lib/chloros/models`). |
| 7 | **CUDA + Denoiser**| Texture Aware สามารถใช้งานได้จริง — ต้องใช้ CUDA**และ** ไฟล์โมเดลอย่างน้อยหนึ่งไฟล์ |

การรันสิ้นสุดด้วย `N/7 checks passed` โดยแสดงรายชื่อข้อผิดพลาดทั้งหมด

### 4. ประมวลผลชุดข้อมูลแรกของคุณ

```bash
chloros-cli process ~/datasets/flight001
```

***

## ไฟล์และไดเรกทอรี

### สำหรับผู้ใช้แต่ละคน

Chlorosเก็บข้อมูลรับรองและ CLI การตั้งค่าไว้ในไดเรกทอรีข้ามแพลตฟอร์มเดียว **`~/.chloros/`** (บน Windows, `%USERPROFILE%\.chloros\`). ส่วนแคชสองตัวที่เฉพาะสำหรับ Linux จะปฏิบัติตามมาตรฐาน XDG — ซึ่งจะใช้ `XDG_CONFIG_HOME` / `XDG_CACHE_HOME` เมื่อถูกกำหนด

| เส้นทาง | วัตถุประสงค์ |
| --- | --- |
| `~/.chloros/user_session.json` | แคชเซสชันการเข้าสู่ระบบที่เขียนโดย `chloros-cli login` (ถูกล้างทุกครั้งที่มีการติดตั้ง/อัปเกรดแพ็กเกจ) |
| `~/.chloros/working_directory.txt` | การแทนที่โฟลเดอร์โครงการเริ่มต้น (`chloros-cli set-project-folder` / `get-project-folder` / `reset-project-folder`) |
| `~/.chloros/cli_language.json` | การตั้งค่าภาษาของCLI (`chloros-cli language <code>`) |
| `~/.chloros/user.json` | การตั้งค่าภาษาที่ใช้ร่วมกับ GUI ของWindows — `language` ที่นี่มีลำดับความสำคัญสูงกว่า `cli_language.json` |
| `~/.chloros/update_cache.json` | แคชหนึ่งชั่วโมงสำหรับการตรวจสอบการอัปเดตเมื่อเริ่มต้นระบบของ Linux /Jetson |
| `~/.chloros/backend.log` | บันทึกของระบบหลัง (backend) เมื่อระบบหลังถูกเปิดโดย CLI |
| `~/.chloros/camera_cal/<serial>/<bundle_sha>/` | แพ็กการปรับเทียบ LATTICE ที่เก็บไว้ในแคชสำหรับแต่ละกล้อง โดยใช้หมายเลขซีเรียลและแฮชของบันเดิลเป็นคีย์ |
| `~/.chloros/daq_cap_profiles/<u\|m\|e>/<cap_id>.json` | การแทนที่โปรไฟล์การแก้ไขขีดจำกัด DAQ โดยผู้ใช้ (เป็นตัวเลือก) |
| `~/.config/chloros/system_config.json` | โปรไฟล์ฮาร์ดแวร์ที่เก็บไว้ในแคชจาก Dynamic Compute Adaptation — ลบไฟล์นี้เพื่อบังคับให้ระบบตรวจจับฮาร์ดแวร์ใหม่ |
| `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` | บันทึกเซิร์ฟเวอร์แบ็กเอนด์ หนึ่งไฟล์ต่อการเริ่มต้น |
| `~/Chloros Projects/` | โฟลเดอร์โครงการเริ่มต้นเมื่อไม่มีการตั้งค่าทับ |

### ระดับระบบ

| เส้นทาง | วัตถุประสงค์ |
| --- | --- |
| `/usr/bin/chloros-cli` | สคริปต์ห่อหุ้ม — ตั้งค่า `LD_LIBRARY_PATH` สำหรับไลบรารีเนทีฟที่มาพร้อมชุด จากนั้นเรียกใช้ไบนารีจริง |
| `/usr/bin/chloros-backend` | สคริปต์ห่อหุ้ม — เช่นเดียวกัน แต่เพิ่ม `CHLOROS_PRODUCTION=1` เพื่อให้เกตเวย์การตรวจสอบสิทธิ์ของแบ็กเอนด์ไม่สามารถปิดตัวเองได้โดยไม่ให้ผู้ใช้ทราบ |
| `/usr/lib/chloros/chloros-cli`, `/usr/lib/chloros/chloros-backend` | ไบนารีที่คอมไพล์แล้ว |
| `/usr/lib/chloros/arena_runtime/` | Arena SDK runtime ที่กล้อง LATTICE ต้องการ |
| `/usr/lib/chloros/models/*.pth.enc` | แบบจำลองตัวลดสัญญาณรบกวนที่ถูกเข้ารหัส ซึ่งใช้โดย Texture Aware debayer |
| `/usr/lib/chloros/sdk/chloros_sdk-*.whl` | Python SDK wheel ที่ตรงกับเวอร์ชันนี้อย่างแม่นยำ |
| `/usr/lib/chloros/exiftool` | exiftool ที่มาพร้อมชุด (สร้างลิงก์สัญลักษณ์ไปยัง `/usr/local/bin/exiftool` เฉพาะเมื่อระบบไม่มี exiftool อยู่แล้ว) |
| `/etc/chloros/update.conf` | การตั้งค่าช่องอัปเดตที่ `chloros-cli update` อ่าน |
| `/etc/sysctl.d/60-chloros-ptp.conf` | ตั้งค่า `net.ipv4.ip_unprivileged_port_start = 319` เพื่อให้ backend สามารถผูกพอร์ต PTP ได้โดยไม่ต้องใช้สิทธิ์ root |
| `/etc/ld.so.conf.d/Arena_SDK.conf` | ชี้ตัวโหลดแบบไดนามิกไปยัง `/usr/lib/chloros/arena_runtime` |
| `/lib/udev/rules.d/70-chloros-daq.rules` | ให้สิทธิ์ผู้ใช้ที่เข้าสู่ระบบเข้าถึงสะพานซีเรียล USB DAQ-U (CP2102N, `10c4:ea60`) |
| `/lib/systemd/system/chloros-backend.service` | เปิดใช้งานบริการแบ็กเอนด์แบบเปิดตลอดเวลา (ติดตั้งแล้ว, **ยังไม่ได้เปิดใช้งาน**) |
| `/usr/share/applications/chloros-cli.desktop` | รายการเมนูแอปพลิเคชัน &quot;Chloros CLI&quot; ที่เปิดเทอร์มินัล |

## ตำแหน่งไฟล์ปฏิบัติการของระบบหลัง

CLI และ SDK จะตรวจหาตำแหน่งของระบบหลังโดยอัตโนมัติ:

| ส่วนประกอบ | เส้นทาง |
| --- | --- |
| CLI | `/usr/bin/chloros-cli` |
| ระบบหลัง | `/usr/lib/chloros/chloros-backend` |

สามารถกำหนดเส้นทางของ backend ใหม่ได้โดยใช้แฟล็ก CLI `--backend-exe` หรือพารามิเตอร์ของคอนสตรัคเตอร์ SDK `backend_exe`, และพอร์ตด้วย `--port` (ค่าเริ่มต้น `5000`).

{% hint style="info" %}
`CHLOROS_BACKEND_URL` ชี้ไปยัง **`lattice`**,**`project`**และ**`daq pool-*`** ไปยังระบบหลังงานระยะไกล คำสั่งหลัก (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) จะเพิกเฉยต่อมันโดยเจตนาและกำหนดเป้าหมายไปที่ `http://127.0.0.1:<port>` เสมอ
{% endhint %}

***

## กล้อง LATTICE และเซ็นเซอร์แสง DAQ บน Linux

กลุ่มคำสั่ง live-hardware ทั้งหมดทำงานได้บน Linux (amd64 และ Jetson):

* **`chloros-cli lattice`** — ค้นหา เชื่อมต่อ ตั้งค่า และจับภาพจากกล้อง LATTICE และอาร์เรย์ที่ซิงโครไนซ์ `.deb` รวมไลบรารีรันไทม์ Arena SDK ที่จำเป็นและลงทะเบียนกับไดนามิกโหลดเดอร์
* **`chloros-cli daq pool-*`** — เชื่อมต่อเซ็นเซอร์แสง DAQ-U/M/E ผ่าน backend pool, ส่งสเปกตรัมที่ปรับเทียบแล้วแบบสตรีม และบันทึกไฟล์ `.daq` CLI ที่ถูกคอมไพล์แล้ว จะจัดส่งเฉพาะตระกูล `pool-*` เท่านั้น: `pool-connect`, `pool-disconnect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`.
* **`chloros-cli project`** — ควบคุมโครงการที่บันทึกไว้ (รวมถึงกล้อง เซนเซอร์ และการตั้งค่าการประมวลผล) โดยไม่ใช้หน้าจอ
* **`chloros-cli time-sync`** — ตรวจสอบ PTP grandmaster ที่ระบบหลังบ้าน Chloros ทำงานสำหรับกล้อง LATTICE และเซ็นเซอร์ DAQ-E.

```bash
# DAQ-E at a known address — the reliable path on multi-homed hosts
chloros-cli daq pool-connect --eth-host 192.168.2.50

# DAQ-U over USB serial
chloros-cli daq pool-connect --port /dev/ttyUSB0

# What is connected, then the latest calibrated spectrum as JSON
chloros-cli daq pool-list
chloros-cli daq pool-latest --sensor-id daq-e-a1b2c3 --json
```

`--sensor-id` เป็นข้อกำหนดที่จำเป็นสำหรับ `pool-latest`, `pool-stream`, `pool-record` และ `pool-set-cap`; `pool-list` แสดง ID ที่อยู่ในพูลปัจจุบัน

{% hint style="info" %}
**ควรใช้ `--eth-host` สำหรับการเชื่อมต่อ DAQ-E ครั้งแรกบนเครื่องที่มีหลายอินเทอร์เฟซเครือข่าย** การค้นหาอัตโนมัติจะสแกน mDNS และอาจไม่พบอินเทอร์เฟซของเซ็นเซอร์เนื่องจากแคช ARP ที่ว่างเปล่า ดังนั้นการเชื่อมต่อ `pool-connect --eth` ครั้งแรกหลังการบูตอาจล้มเหลว แม้เซ็นเซอร์จะอยู่ในสภาพสมบูรณ์ก็ตาม การระบุ IP หรือชื่อโฮสต์ของเซ็นเซอร์จะข้ามขั้นตอนการค้นหาไปโดยสิ้นเชิง
{% endhint %}

**สิทธิ์การเชื่อมต่อแบบอนุกรมของ DAQ-U** ถูกจัดการโดยกฎ udev ที่ติดตั้งไว้ (`uaccess` + กลุ่ม `dialout`) หากเซ็นเซอร์ที่ต่อไว้แล้วไม่สามารถเข้าถึงได้ ให้โหลดกฎใหม่หรือต่อเซ็นเซอร์ใหม่:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=tty
```

ดู [CLI เอกสารอ้างอิง](../CLI.md) เพื่อดูชุดคำสั่งทั้งหมด

### PTP ที่ทำงานตลอดเวลาสำหรับโฮสต์ที่ไม่มีหน้าจอ

ในการติดตั้งครั้งแรก ระบบจะสร้าง unit systemd `chloros-backend.service` แต่ **ยังไม่เปิดใช้งาน** บน Jetson หรือเซิร์ฟเวอร์แบบไม่มีหน้าจอ (headless) ที่จำเป็นต้องรักษาการซิงค์เวลา PTP ให้ทำงานอย่างต่อเนื่องสำหรับเซ็นเซอร์ DAQ-E และกล้อง LATTICE ให้เปิดใช้งานมัน:

```bash
sudo systemctl enable --now chloros-backend.service
sudo systemctl status chloros-backend.service
```

หากไม่เปิดใช้งาน PTP จะทำงานเฉพาะเมื่อแบ็กเอนด์ Chloros กำลังทำงาน — นั่นคือ ระหว่างเซสชัน CLI / SDK ที่กำลังทำงานอยู่

อุปกรณ์นี้จะผูก backend กับ `127.0.0.1:5000` (การตั้งค่าสภาพแวดล้อม `CHLOROS_HOST` / `CHLOROS_PORT` ภายในอุปกรณ์; แทนที่ด้วย `sudo systemctl edit chloros-backend.service`) และจะเริ่มต้นใหม่หลังจาก 5 วินาทีหากเกิดข้อผิดพลาด

**วิธีที่ PTP ได้รับพอร์ต** PTP ใช้ UDP 319/320 ซึ่งทั้งสองพอร์ตอยู่ต่ำกว่าขีดจำกัดปกติ 1024 ของพอร์ตที่มีสิทธิพิเศษ แพ็กเกจ `postinst` จะเขียนค่า `/etc/sysctl.d/60-chloros-ptp.conf` ด้วย `net.ipv4.ip_unprivileged_port_start = 319` ซึ่งทำให้ส่วนหลัง (backend) สามารถผูกพอร์ตเหล่านั้นได้ขณะทำงานในฐานะผู้ใช้ของคุณ นอกจากนี้ยังใช้ `setcap cap_net_bind_service,cap_net_raw=+ep` กับไฟล์ไบนารีของ backend เพื่อเป็นมาตรการป้องกันเพิ่มเติม — นี่คือเหตุผลที่ `libcap2-bin` ถูกประกาศเป็นความพึ่งพาของแพ็กเกจ***

## ตัวอย่างการเขียนสคริปต์ Bash

{% hint style="info" %}
**รหัสออกที่เหมาะสำหรับการเขียนสคริปต์**`chloros-cli process` จะออก `0` เมื่อสำเร็จ และ**มีค่าไม่เท่ากับศูนย์เมื่อล้มเหลว — รวมถึงการรันที่ขอผลิตภัณฑ์ภาพแต่ไม่เขียนอะไรเลย** (มันจะพิมพ์ `Processing finished but wrote no image products.` พร้อมทั้งระบุชื่อโฟลเดอร์โครงการและสาเหตุทั่วไป) การทำงานที่สำเร็จจะรายงานจำนวนผลิตภัณฑ์ภาพที่เขียนออกมา (`Image products written: N`) รหัสออก: `0` สำเร็จ, `1` ล้มเหลว, `2` ข้อผิดพลาดในอาร์กิวเมนต์, `130` ถูกขัดจังหวะ.
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
    --indices NDVI NDRE GNDVI \
    --debayer texture-aware \
    --no-vignette
```

ค่า `--format` ที่ถูกต้องมีเพียงสี่ค่าเท่านั้น และค่าเหล่านั้นมีช่องว่าง — ต้องใส่เครื่องหมายคำพูดรอบค่าเสมอ:

| ค่า `--format` | โฟลเดอร์ผลลัพธ์ |
| --- | --- |
| `TIFF (16-bit)` *(ค่าเริ่มต้น)* | `tiff16` |
| `TIFF (32-bit, Percent)` | `tiff32` |
| `PNG (8-bit)` | `png8` |
| `JPG (8-bit)` | `jpg8` |

`--debayer` รับ `standard` (ค่าเริ่มต้น) หรือ `texture-aware` (Chloros+).

### การประมวลผลอัตโนมัติด้วย Cron

```cron
# Process any new datasets at 2 AM daily
0 2 * ** /usr/bin/chloros-cli process /data/incoming --output /data/processed >> /var/log/chloros.log 2>&1
```

### ตัวอย่าง Python SDK

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
sudo chmod +x /usr/lib/chloros/chloros-backend
```

### &quot;setcap failed&quot; ระหว่างการติดตั้ง

`.deb` ใช้ `cap_net_bind_service` กับ `/usr/lib/chloros/chloros-backend` เพื่อให้สามารถผูกพอร์ต PTP 319/320 ได้โดยไม่ต้องใช้สิทธิ์ root หาก `libcap2-bin` ไม่ปรากฏขึ้นขณะติดตั้ง การเรียกใช้จะถูกข้ามไป ให้ติดตั้งมันและติดตั้งแพ็กเกจใหม่:

```bash
sudo apt install libcap2-bin
sudo apt reinstall chloros
```

### PTP ไม่เริ่มทำงาน / ไม่สามารถผูกพอร์ต 319 ได้

ตรวจสอบว่าขีดจำกัดขั้นต่ำของพอร์ตที่ไม่ต้องการสิทธิ์พิเศษ (unprivileged-port floor) ได้ถูกลดลงแล้ว หรือไม่ และหากยังไม่ได้ ให้ปรับใช้ใหม่สำหรับการบูตปัจจุบัน:

```bash
sysctl net.ipv4.ip_unprivileged_port_start     # expect 319
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=319
```

จากนั้นตรวจสอบ grandmaster:

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
```

### &quot;ไม่พบไดรเวอร์กล้อง LATTICE&quot;

ไม่สามารถแก้ไขปัญหาการรันไทม์ของ Arena SDK ได้ โปรดตรวจสอบให้แน่ใจว่าไฟล์กำหนดค่า loader ที่แพ็กเกจเขียนไว้มีอยู่และได้รับการอัปเดตแล้ว:

```bash
cat /etc/ld.so.conf.d/Arena_SDK.conf     # expect /usr/lib/chloros/arena_runtime
sudo ldconfig
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

บันทึกของ Backend สำหรับการเริ่มต้นที่ล้มเหลวอยู่ใน `~/.cache/chloros/logs/`.

### ไม่ตรวจพบ CUDA

```bash
# Check NVIDIA driver installation
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check JetPack version
cat /etc/nv_tegra_release
```

`chloros-cli selftest` รายงานเรื่องเดียวกันในบรรทัดเดียว: `GPU: <name>, CUDA: <bool>, PyTorch: <version>`.

### ขาดไลบรารีที่ใช้ร่วมกัน

```bash
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries
ldd /usr/lib/chloros/chloros-backend | grep "not found"
```

### การเริ่มต้นช้าบนระบบที่ใช้การ์ด SD

ไฟล์ไบนารีที่คอมไพล์แล้วจะทำการแตกไฟล์ตัวเองไปยังไดเรกทอรีชั่วคราวทุกครั้งที่เริ่มต้นระบบ หาก `/mnt/ssd/tmp` มีอยู่ Chloros จะใช้มันโดยอัตโนมัติ; หากไม่มี ให้ตั้งค่า `TMPDIR` เป็นระบบไฟล์ที่เร็ว:

```bash
export TMPDIR=/mnt/nvme/tmp
```

***

## การอัปเดต Chloros บน Linux

คำสั่ง `update` ใช้ได้เฉพาะกับ Linux /Jetson เท่านั้น คำสั่งนี้จะตรวจสอบเวอร์ชันที่เผยแพร่ในช่องอัปเดตที่ตั้งค่าไว้ที่ `/etc/chloros/update.conf` และเสนอให้ดาวน์โหลดและติดตั้ง `.deb` ที่ตรงกัน:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

บน Linux /Jetson โปรแกรม CLI ยังดำเนินการตรวจสอบการอัปเดตแบบไม่บล็อกทุกครั้งที่เริ่มต้นระบบ (ผลลัพธ์ถูกเก็บไว้ในแคชเป็นเวลาหนึ่งชั่วโมงใน `~/.chloros/update_cache.json`) และจะแสดงข้อความ `Update available: vX.Y.Z` เมื่อมีเวอร์ชันใหม่กว่า การตั้งค่าและโครงการของคุณจะยังคงอยู่หลังการอัปเดต; คุณจะต้องลงชื่อเข้าใช้อีกครั้งหลังจากนั้น

## การถอนการติดตั้ง

```bash
sudo apt remove chloros
```

การถอนการติดตั้งจะหยุดการทำงานของ `chloros-backend.service`, คืนค่าพอร์ตเริ่มต้นที่ไม่มีสิทธิ์พิเศษ (1024), ลบลิงก์สัญลักษณ์ของ exiftool ที่มาพร้อมชุดโปรแกรมและไฟล์การตั้งค่า Arena loader รวมถึงล้างข้อมูลรับรองที่เก็บไว้ในแคช โครงการและไฟล์ข้อมูล `~/.chloros/` ของคุณจะไม่ถูกลบ

***

## ขั้นตอนต่อไป

* [คู่มือ NVIDIA Jetson](nvidia-jetson-guide.md) — การปรับแต่งและติดตั้งเฉพาะสำหรับ Jetson
* [CLI : Command Line](../CLI.md) — คู่มือ CLI
* [API : Python SDK](../api-python-sdk.md) — คู่มือ SDK
* [CLI Reference](../reference/cli-reference.md) และ [SDK Reference](../reference/sdk-reference.md) — รายการคำสั่ง/API ที่ครบถ้วนสำหรับเวอร์ชัน 1.2.0
* [Dynamic Compute Adaptation](../processing-architecture/dynamic-compute-adaptation.md) — วิธีที่Chloros ปรับตัวให้เข้ากับฮาร์ดแวร์ของคุณ
