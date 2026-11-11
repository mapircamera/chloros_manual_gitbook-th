# CLI : บรรทัดคำสั่ง

> **คู่มืออ้างอิงครบถ้วน:**[คู่มืออ้างอิงCLI](reference/cli-reference.md) อธิบาย**ทุกแฟล็กของทุกคำสั่งย่อย** และได้รับการปรับให้เหมาะสมสำหรับผู้ช่วย AI — วาง URL ของคู่มือนี้ลงในผู้ช่วยของคุณและถามคำสั่งที่ใช้งานได้: `https://mapir.gitbook.io/chloros/reference/cli-reference`
>
> **เคล็ดลับสำหรับเครื่องมือ AI:** หน้าใดก็ตามในคู่มือนี้สามารถเข้าถึงได้ในรูปแบบ Markdown ดิบ โดยการเพิ่ม `.md` ลงในURL ของหน้านั้น (เช่น `https://mapir.gitbook.io/chloros/reference/cli-reference.md`), และ `https://mapir.gitbook.io/chloros/llms.txt` จะจัดทำดัชนีคู่มือทั้งหมดเพื่อใช้กับ LLM

> **ต้องการให้ผู้ช่วยของคุณรัน Chloros เองหรือไม่?** `chloros-mcp` ซึ่งเป็นเซิร์ฟเวอร์ MCP ที่ติดตั้งพร้อม Python SDK สามารถทำงานร่วมกับ Claude Desktop, Claude Code, Cursor และไคลเอนต์ MCP อื่นๆ ได้: ดู [การใช้ Chloros กับผู้ช่วย AI](ai-assistants.md).

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: banner shows CLI 1.1.0; reshoot the CLI welcome/banner output on the 1.3.0 build so the version line reads "Chloros CLI 1.3.0" -->


## CLI
คืออะไร

`chloros-cli` เป็นส่วนหน้าบรรทัดคำสั่ง (CLI
) ของเครื่องยนต์ประมวลผลเดียวกันที่แอปเดสก์ท็อป Chloros
ใช้ มันเป็นไคลเอนต์HTTP
แบบบางที่เชื่อมต่อกับแบ็กเอนด์Chloros
(เซิร์ฟเวอร์ท้องถิ่นบน `127.0.0.1:5000`) — คำสั่งส่วนใหญ่จะเริ่มต้นแบ็กเอนด์โดยอัตโนมัติ ดังนั้นสคริปต์จึงต้องการเพียงการเรียก `chloros-cli process …` ครั้งเดียวเท่านั้น จากนั้น backend จะยังคงทำงานต่อไปเพื่อรับคำสั่งถัดไป: มันจะหยุดทำงานเมื่อเรียกใช้ `chloros-cli logout` (เว้นแต่แอปจะเปิดอยู่ หรือกำลังดำเนินการรันหรือสร้างอยู่), เมื่อเรียกใช้ `chloros-cli backend stop`, หรือหยุดทำงานเองหลังจาก 30 นาทีหากไม่มีไคลเอนต์ การรัน หรืออุปกรณ์ที่เชื่อมต่ออยู่ `chloros-cli backend status` แสดงผลนี้

มันทำงานได้บน **Windows
10/11 (x64)**และ**Linux
(x86_64 และ NVIDIA Jetson arm64 บน JetPack 6)** ในเทอร์มินัลใดก็ได้ โดยไม่จำเป็นต้องใช้ GUI ตรวจสอบการติดตั้งด้วย:

```bash
chloros-cli --version    # prints "Chloros CLI 1.3.0"
```

กลุ่มคำสั่งโดยสรุป:

* **การประมวลผลและบัญชี** — `process`, `login`, `logout`, `status`, `export-status`, `language` (38 ภาษา — ดู [ภาษาที่รองรับ](supported-languages.md)), `set-project-folder` / `get-project-folder` / `reset-project-folder`, `selftest`, `update` (เฉพาะ Linux
/Jetson)
* **ฮาร์ดแวร์แบบสด** — `lattice` (ควบคุมกล้อง LATTICE, คำสั่งย่อยกว่า 45 คำสั่ง), `daq pool-*` (เซ็นเซอร์แสง DAQ), `time-sync` (PTP)
* **ระบบอัตโนมัติ** — `project` (ควบคุมโครงการChloros
ที่บันทึกไว้แบบไม่มีหน้าจอ รวมถึงสูตรการจับภาพ YAML)

ตัวเลือกทั่วไปที่ควรรู้: `--port N` (พอร์ตแบ็กเอนด์, ค่าเริ่มต้น `5000`), `-v/--verbose`, `--restart` (บังคับให้ backend เริ่มต้นใหม่), `--backend-exe PATH` ดู [คู่มืออ้างอิง CLI
](reference/cli-reference.md) เพื่อดูรายการทั้งหมด

***

## การติดตั้ง

CLI
**มาพร้อมกับตัวติดตั้ง Chloros** บนทุกแพลตฟอร์ม — ไม่มีไฟล์ดาวน์โหลด CLI
แยกต่างหาก ดาวน์โหลดตัวติดตั้งจากหน้า [Download](download.md)

### Windows


โปรแกรมติดตั้งจะวาง CLI
ไว้ที่:

```

C:\Program Files\Chloros\cli\chloros-cli.exe
```

และเพิ่มโฟลเดอร์นั้นเข้าสู่ระบบของคุณ `PATH` เพียงครั้งเดียว (การอัปเกรดจะลบรายการซ้ำที่โปรแกรมติดตั้งเวอร์ชันก่อนหน้าทิ้งไว้ด้วย) — **เปิดเทอร์มินัลใหม่**หลังจากติดตั้งเพื่อให้ระบบตรวจพบ `PATH` ที่อัปเดตแล้ว โปรแกรมติดตั้งยังวางสคริปต์ตัวเปิด (`Chloros_CLI.bat` / `Chloros_CLI.ps1`) ในโฟลเดอร์รากของการติดตั้ง พร้อมทั้ง**Chloros
CLI
** ในเมนู Start ซึ่งแต่ละรายการจะเปิดเทอร์มินัลที่มี `chloros-cli` พร้อมใช้งาน

### Linux


ติดตั้ง `.deb` สำหรับสถาปัตยกรรมของคุณด้วย `apt` (ไม่ใช่ `dpkg -i` ซึ่งข้ามข้อกำหนดที่แนะนำของ Python
SDK
):

```bash
# Linux x86_64
sudo apt install ./chloros_1.3.0_amd64.deb

# NVIDIA Jetson (arm64, JetPack 6)
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
```

การติดตั้งนี้จะติดตั้ง `chloros-cli` ไปจนถึง `/usr/bin/chloros-cli` (ซึ่งมีอยู่แล้วใน `PATH`), โปรแกรมแบ็กเอนด์และCLI
ที่ถูกแตกไฟล์ใน `/usr/lib/chloros/backend/` และ `/usr/lib/chloros/cli/` (`/usr/lib/chloros/chloros-backend` เป็นโปรแกรมเปิดตัวที่เริ่มต้นระบบหลัง), สภาพแวดล้อมการทำงานของ Arena SDK
ที่ระบบหลังต้องการสำหรับกล้อง LATTICE, และ Python
SDK
สำหรับระบบ `python3`. ดู [การติดตั้งบน [Linux
](linux/linux-installation.md) เพื่อทราบรายละเอียดเพิ่มเติม

### ตรวจสอบ

```bash
chloros-cli --version    # "Chloros CLI 1.3.0"
chloros-cli selftest     # 7-step diagnostic: backend, API, GPU/CUDA, denoiser models
chloros-cli status       # license tier + logged-in user
```

***

## การเข้าสู่ระบบและการออกใบอนุญาต

การเข้าถึงผ่าน CLI (และ Python
SDK
) ต้องใช้ **แผน Chloros
+ แบบเสียค่าใช้จ่าย**— ทุกแผนแบบเสียค่าใช้จ่ายมีสิทธิ์นี้; ส่วนแผนฟรีไม่มีสิทธิ์นี้ ข้อจำกัดนี้ถูกบังคับใช้**ด้านเซิร์ฟเวอร์** โดยระบบแบ็กเอนด์ ไม่ใช่โดยไฟล์ไบนารี CLI
: การเรียกที่ไม่ได้เข้าสู่ระบบจะถูกปฏิเสธด้วยรหัสข้อผิดพลาด `401 AUTH_REQUIRED`, ส่วนการเรียกใช้ขณะเข้าสู่ระบบในแพ็กเกจฟรีจะถูกปฏิเสธด้วยรหัสข้อผิดพลาด `403 PLAN_UPGRADE_REQUIRED` ไม่ว่าจะมาจาก `chloros-cli`, SDK
หรือไคลเอนต์ HTTP
ที่พัฒนาเอง อัปเกรดที่ [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing).

เข้าสู่ระบบ **ครั้งเดียวต่อเครื่อง**:

```bash
chloros-cli login user@example.com 'YourPassword'
chloros-cli status
```

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: login success output predates 1.2.0; reshoot `chloros-cli login` followed by `chloros-cli status` on the 1.3.0 build showing the license tier line -->


{% hint style="warning" %}
**รหัสผ่านที่มีตัวอักษรพิเศษ**(`$`, `!`, spaces): wrap the password in**single quotes**, as shown above. In PowerShell double quotes, `$$` จะถูก shell เปลี่ยนแปลง (CLI
จะตรวจพบปัญหานี้เมื่อได้รับรหัส 401 และจะลองใหม่โดยอัตโนมัติ แต่การใช้เครื่องหมายคำพูดเดี่ยวจะช่วยหลีกเลี่ยงปัญหานี้ได้ทั้งหมด).
{% endhint %}

เซสชันจะถูกเก็บไว้ในแคชที่ `~/.chloros/user_session.json` และยังคงทำงานได้แบบออฟไลน์ในช่วงระยะเวลาผ่อนผันของแพ็กเกจ (30 วันสำหรับแพ็กเกจรายเดือน, จนถึงวันหมดอายุสำหรับแพ็กเกจรายปี) `chloros-cli status` ทำงานได้แม้ไม่มีแผนบริการที่ชำระค่าบริการ ดังนั้นเหตุผลของการปฏิเสธจะปรากฏให้เห็นเสมอ

{% hint style="danger" %}
**กำลังกำหนดเวลาทำงานแบบ headless? ให้เข้าสู่ระบบก่อน**คำสั่งที่สร้างกระบวนการในแบ็กเอนด์ (`process`, `status`, `export-status`, …) ที่ทำงาน**โดยไม่มีเซสชันที่เก็บไว้ในแคช**จะไม่ล้มเหลวอย่างรวดเร็ว — แต่จะเปลี่ยนเป็นพรอมต์แบบโต้ตอบ `Email:` / `Password:` บน stdin. ดังนั้น งาน cron หรือขั้นตอน CI ที่ทำงานโดยไม่มีผู้ดูแลจะ**ค้างอยู่ระหว่างรอข้อมูลเข้า** ให้เรียกใช้ `chloros-cli login EMAIL 'PASSWORD'` หนึ่งครั้งบนเครื่องก่อนที่จะกำหนดเวลาทำงานใดๆ
{% endhint %}

***

## การประมวลผลครั้งแรกของคุณ

กำหนด `process` ให้ชี้ไปยังโฟลเดอร์ที่เก็บข้อมูลการจับภาพ — มันจะตรวจจับSurvey3
(`.raw` + `.jpg`), LATTICE (`.tif`/`.tiff`), `.dng` หรือการผสมผสาน:

```bash
chloros-cli process "C:\Images\flight_001"          # Windows
chloros-cli process ~/images/flight_001              # Linux
```

สตรีมความคืบหน้าแบบเรียลไทม์ตามแต่ละเธรดของ pipeline (การตรวจจับ, การวิเคราะห์, การประมวลผล, การส่งออก), และการรันที่สำเร็จจะสิ้นสุดลงด้วยการรายงานจำนวนผลิตภัณฑ์ภาพที่เขียนได้: `Image products written: N` ตามด้วย `(plus M clamp masks)` เมื่อการรันยังเขียน [clamp masks](output-image-formats.md#clamp-masks) ด้วย (ซึ่งไม่นับเป็นผลิตภัณฑ์)



<!-- SCREENSHOT-NEEDED: terminal capture of a `chloros-cli process` run on a LATTICE captures folder completing successfully — per-thread progress lines visible and the final "Image products written: N (plus M clamp masks)" summary line -->
หากแบ็กเอนด์Chloros
กำลังทำงานอยู่แล้ว (เช่น ของแอปเดสก์ท็อป) `process` จะใช้แบ็กเอนด์นั้นและเปิดโครงการในนั้น มันจะปฏิเสธการดำเนินการในขณะที่แอปกำลังเปิดโครงการที่แอปนั้นเปิดไว้: ให้ดำเนินการจากแอป หรือปิดโครงการนั้น หากการรันครั้งก่อนยังกำลังเสร็จสิ้น มันจะรอสูงสุด 15 วินาทีก่อนที่จะปฏิเสธ `--restart` จะบังคับใช้ backend ใหม่ บนLinux
, backend ที่ระบบบริการ systemd ทำงานอยู่จะไม่ถูกเริ่มต้นใหม่: `process` ใช้มันตามสภาพเดิม และในกรณีที่ปกติจะเริ่มต้นใหม่ (`--restart` หรือการนำเข้าที่ไม่พบอะไร), มันจะพิมพ์ `sudo systemctl restart <unit>` แล้วออกจากระบบ `1`.

### สถานที่ที่ผลลัพธ์ถูกบันทึก

`process` จะเขียนผลลัพธ์ลงใน **โฟลเดอร์โครงการ** ไม่ใช่ในโฟลเดอร์ข้อมูลนำเข้าของคุณ:

* หากไม่มี `-o`: โครงการจะถูกสร้างขึ้นในโฟลเดอร์โครงการเริ่มต้นของคุณ (ใช้ร่วมกับ GUI; จัดการด้วย `get-project-folder` / `set-project-folder`; หากไม่มีการตั้งค่าใดๆ `Documents/MAPIR/Chloros Projects` จะอยู่ในโฟลเดอร์ผู้ใช้ของคุณ), ชื่อจะถูกกำหนดโดย `-n/--project-name` หรือตามเวลา (`YYYYMMDD_HHMMSS`) หากไม่ระบุชื่อ
* ด้วย `-o PATH`: โฟลเดอร์นั้น **คือ** โฟลเดอร์โครงการ หากโฟลเดอร์นั้นมี `project.json` อยู่แล้ว จะสร้าง `_1`/`_2`… ที่ต่อท้ายจะถูกสร้างขึ้นแทนที่จะเขียนทับไฟล์เดิม ส่วน `PATH` ที่ระบุแบบสัมพัทธ์จะถูกกำหนดตามโฟลเดอร์ที่คุณเรียกใช้ `chloros-cli` จาก

{% hint style="info" %}
**เส้นทางสัมพัทธ์ (เปลี่ยนแปลงในเวอร์ชัน 1.3.0)** ทุกเส้นทางที่ระบบหลังบ้านเปิด — `-o`/`--output` ในคำสั่งใดก็ตาม (รวมถึง `output` ตามค่าเริ่มต้นของคำสั่ง capture), `--input`, `--burst-dir`, `--profile`, `--save` และ `--mask` — จะถูกกำหนดเส้นทางไปยังโฟลเดอร์ที่คุณเรียกใช้ `chloros-cli` จาก ดังนั้น `-o captures/` จะเขียนข้อมูลลงใน `<current folder>/captures/` ในเวอร์ชัน 1.2.0 ระบบแบ็กเอนด์จะทำการแปลงเส้นทางเหล่านี้ในโฟลเดอร์ติดตั้งของตัวเอง ซึ่งทำให้เกิดข้อผิดพลาด &quot;Access is denied&quot; เมื่อใช้กับระบบแบ็กเอนด์ระยะไกล (`CHLOROS_BACKEND_URL`) เส้นทางจะถูกส่งไปตามที่พิมพ์และถูกแปลงบนเครื่องนั้น
{% endhint %}

{% hint style="warning" %}
**Windows
: ให้ใช้ชื่อโฟลเดอร์โครงการสั้นๆ เว้นแต่จะเปิดใช้งานเส้นทางยาว (long paths)** เมื่อปิดใช้งานเส้นทางยาวใน Windows
(ค่าเริ่มต้นของ Windows
) เส้นทางไฟล์ใด ๆ ก็ไม่สามารถยาวเกิน 260 ตัวอักษรได้ และชื่อผลิตภัณฑ์จะอยู่ได้สูงสุดประมาณ 130 ตัวอักษรใต้ชื่อโฟลเดอร์โครงการ `process` จะตรวจสอบสิ่งนี้ก่อนที่จะเริ่มทำงาน: โฟลเดอร์โครงการที่ลึกเกินไปจะถูกปฏิเสธด้วยข้อความ **&quot;The project folder is too deep for this computer&quot;** พร้อมทั้งแสดงโฟลเดอร์โครงการที่ยาวที่สุดที่ยังเหมาะสม และวิธีเปิดใช้งานเส้นทางยาว — ไม่มีการประมวลผลใดๆ เกิดขึ้น ใช้ `-o` (หรือ `set-project-folder`) ที่สั้นกว่า หรือเปิดใช้งานเส้นทางยาวของ Windows
(ตัวเลือก **Enable Windows
long paths** ของตัวติดตั้ง ซึ่งถูกเลือกไว้เป็นค่าเริ่มต้น) และรีสตาร์ท Chloros

{% endhint %}

ภายในโครงการ ผลิตภัณฑ์ถูกจัดกลุ่ม **ตามกล้อง แล้วตามรูปแบบไฟล์**:

```
<project>/
├── project.json
├── calibration_data.json
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one folder per requested index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

โฟลเดอร์กล้องคือ `LATT-<sensor>-<lens>-F<filter>` สำหรับ LATTICE (ตรงกับ EXIF ของภาพที่ถ่าย `Model`) และ `<model>_<filter>` (เช่น `Survey3N_RGN`) สำหรับSurvey3
โฟลเดอร์รูปแบบไฟล์มีชื่อตามลำดับ `--format`: `tiff16`, `tiff8`, `png8`, `jpg8` หรือ `tiff32` สำหรับ `TIFF (32-bit, Percent)`.

สำหรับกล้อง M3C แบบมัลติสเปกตรัม โฟลเดอร์ `clamp_masks/` อาจปรากฏในโฟลเดอร์รูปแบบได้เช่นกัน ข้างๆ โฟลเดอร์ผลิตภัณฑ์ (เช่น `tiff16/clamp_masks/Reflectance_Calibrated_Images/` และ `tiff32/clamp_masks/Radiance_Images/`) โฟลเดอร์นี้เก็บมาสก์ต่อพิกเซลของผลิตภัณฑ์ความส่องสว่างและความสะท้อนแสง ซึ่งในกระบวนการแยกแบนด์ได้ปรับค่าลบให้เป็นศูนย์ — ดู [Clamp masks](output-image-formats.md#clamp-masks) โฟลเดอร์ผลิตภัณฑ์ใด ๆ ก็ไม่มีมาสก์อยู่ภายใน

{% hint style="info" %}
**ผลิตภัณฑ์ที่ส่งออกทุกชิ้นจะรักษาชื่อไฟล์ต้นฉบับไว้**การส่งออก radiance ของ `capture_..._raw.tif` จะยังคงมีชื่อว่า `capture_..._raw.tif` — แต่มันจะอยู่ใน `tiff32/Radiance_Images/`**โฟลเดอร์คือตัวระบุผลิตภัณฑ์ ไม่ใช่ชื่อไฟล์** ดังนั้นควรใช้คำสั่ง glob เพื่อค้นหาไดเรกทอรี ไม่ใช่เพื่อค้นหาส่วนท้าย `*radiance*`
{% endhint %}

### ตัวเลือกที่คุณจะใช้จริง

| ตัวเลือก | ค่าเริ่มต้น | หน้าที่ |
| --- | --- | --- |
| `-o, --output PATH` | โฟลเดอร์โครงการเริ่มต้น | ตำแหน่งโฟลเดอร์โครงการ (ดูข้างบน) |
| `-n, --project-name NAME` | timestamp | ชื่อโครงการ |
| `--format FMT` | `TIFF (16-bit)` | หนึ่งใน `TIFF (16-bit)`, `TIFF (8-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. Chloros
ไม่ทำการขยายขนาด: สำหรับภาพ 8 บิตทั้งหมด จะเขียนรูปแบบความลึกมากขึ้นเป็น `TIFF (8-bit)` และระบุไว้เพียงครั้งเดียว |
| `--indices NAME [NAME ...]` | ไม่มี | ดัชนีพืชพรรณที่จะส่งออก (ดู [ดัชนีพืชพรรณ](#vegetation-indices)). |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` = การลบเดย์เยอร์ด้วยระบบประสาทเทียม, ช้ากว่า, คุณภาพสูงสุด (Chloros
+, ต้องใช้ GPU CUDA: หากไม่มี GPU CUDA การประมวลผลจะใช้ `standard`; ไม่รองรับการจับภาพ LATTICE ซึ่งจะใช้ `standard` เสมอ). |
| `--vignette / --no-vignette` | เปิด | การแก้ไข vignette |
| `--reflectance / --no-reflectance` | เปิด | การปรับเทียบค่าสะท้อนแสง; สำหรับ LATTICE นี่คือสวิตช์เปิด/ปิดผลิตภัณฑ์ค่าสะท้อนแสงด้วย |
| `--input-level {auto,raw,debayered,processed}` | `auto` | บังคับจุดเข้าของ pipeline สำหรับไฟล์ TIFF ของ LATTICE |

สำหรับสิ่งอื่น ๆ — การปรับแต่งการตรวจจับเป้าหมาย, PPK, พินการเปิดรับแสง, ธงการจัดแนวอาร์เรย์ — ดู [ส่วน `process` ในคู่มืออ้างอิงCLI
](reference/cli-reference.md).

***

## การเลือกข้อมูลที่จะส่งออก (ผลิตภัณฑ์ LATTICE)

การประมวลผล LATTICE จะกระจายไปยัง **ทุกผลิตภัณฑ์ที่เกี่ยวข้องในครั้งเดียว**สวิตช์ 4 ตัวต่อผลิตภัณฑ์ทั้งหมด**ถูกตั้งเป็น ON ตามค่าเริ่มต้น**; ใช้รูปแบบ `--no-` เพื่อปิดสวิตช์หนึ่งตัว:

| สวิตช์ | ผลิตภัณฑ์ |
| --- | --- |
| `--debayered` | การเดโมซิกแบบเชิงเส้น → `Debayered_Images/`: ลบค่าออฟเซ็ตความมืดจากแพ็กการปรับเทียบ แล้วดำเนินการเดโมเซอิกแบบรับรู้ขอบ โดยไม่มีการแก้ไขความเชิงเส้น ฟลัตฟิลด์ หรือการแยกสี — เป็นไฟล์เดียวกันที่การจับภาพบันทึกไว้ที่ระดับ `debayered` ของมัน เก็บไว้ในทิศทางดั้งเดิมของเซ็นเซอร์ (บันทึกใน XMP) |
| `--preview` | พรีวิวบนหน้าจอ (สมดุลสีขาว + แกมมา; การขยายสีเทียมสำหรับมัลติสเปกตรัม) → `Preview_Images/` |
| `--radiance` | ความส่องสว่างแบบ float32, W/m²/sr/nm → `Radiance_Images/` (เสมอ `tiff32/`) |
| `--reflectance` | uint16 ค่าสะท้อนแสง, พร้อมใช้งานกับ Pix4D → `Reflectance_Calibrated_Images/` |

กล้องหลัก RGB
จะส่งข้อมูลเฉพาะแบบ debayered + preview เท่านั้น — ค่า radiance/reflectance ตามแต่ละแถบความยาวคลื่นไม่มีความหมายสำหรับเซ็นเซอร์แบบกว้าง ดังนั้นตัวเลือกเหล่านั้นจึงไม่มีผลสำหรับกล้องเหล่านี้ Survey3
`.raw` จะไม่สนใจตัวเลือกดังกล่าวและใช้เส้นทาง reflectance/target ตามมาตรฐาน

```bash
# Radiance only — no DAQ downwelling needed
chloros-cli process ~/captures/lattice_flight --no-debayered --no-preview --no-reflectance
```

**`--reflectance-source {auto,target,daq}`** (ค่าเริ่มต้น `auto`) เลือกอ้างอิงค่าสะท้อนแสง: `auto` ใช้ [เป้าหมายการปรับเทียบ](calibration-targets.md) ที่ผ่านการตรวจสอบคุณภาพ (QA) ภายในเฟรมเป็นอ้างอิงสัมบูรณ์ และใช้การแบ่งแสงลงของเซ็นเซอร์แสง DAQ เป็นทางเลือกสำรอง (ρ = π·L/E) เมื่อไม่มีเป้าหมาย (เป้าหมายที่ผ่านการตรวจสอบคุณภาพ (QA) พร้อมธงจะแข่งขันกับ DAQ ในคะแนนที่วัดได้); `target` เป็นแบบเคร่งครัด (ไม่มีการแทนที่ด้วย DAQ); `daq` ให้ความสำคัญกับ DAQ เป็นหลัก การสแกนเป้าหมายที่วัดได้ต่อหน่วยสามารถจัดหาได้ด้วย `--target-reflectance-dir`

{% hint style="info" %}
**การอ่านพิกเซลสะท้อนแสง:**ค่า DN ที่หมายถึง ρ = 1.0 เป็น**ต่อแหล่ง** — ไฟล์ LATTICE จะประทับ `Chloros:PixelScale=32768` ใน XMP; ไฟล์ Survey3
ใช้ค่า 65535 และไม่มีการแท็ก `Chloros:PixelScale` (แต่มีแท็กการประมวลผล `Chloros:*` อื่นๆ) อ่านแท็กและแบ่งด้วยค่านั้นแทนที่จะสมมติว่าเป็นค่าคงที่ รายละเอียดและกรณีขอบเขตเดียวที่ไม่ได้ปรับสเกลโดยเจตนาอยู่ใน [CLI
Reference](reference/cli-reference.md).
{% endhint %}

**การประมวลผลจะเริ่มจาก `raw` เสมอ** ผลิตภัณฑ์ที่ได้มา (การส่งออกข้อมูลแบบเดเบเยอร์/เรเดียนซ์/รีเฟลกแทนซ์) จะไม่ถูกส่งกลับเข้าสู่ท่อประมวลผล — การนำเข้าใหม่และประมวลผลอีกครั้งจะทำให้การคำนวณการปรับเทียบถูกนำไปใช้ซ้ำสองครั้ง ดังนั้นChloros
จึงข้ามขั้นตอนเหล่านี้และแจ้งให้ทราบ `--input-level` เป็นทางออกที่ตั้งใจไว้สำหรับกรณีที่คุณจำเป็นต้องกำหนดจุดเริ่มต้นการประมวลผลอย่างจริงจัง

***

## เมื่อการประมวลผลล้มเหลว

ตั้งแต่เวอร์ชัน 1.2.0 เป็นต้นไป `process` จะแสดงข้อผิดพลาดอย่างชัดเจน แทนที่จะ &quot;สำเร็จ&quot; โดยไม่มีผลลัพธ์ใดๆ แสดงออกมา:

* การรันที่ **ขอผลิตภัณฑ์แต่ไม่เขียนอะไรเลย**— เฉพาะ `project.json` และ `calibration_data.json` — จะพิมพ์ `Processing finished but wrote no image products.` และ**ออกด้วยค่าที่ไม่เป็นศูนย์**, ดังนั้นสคริปต์จึงสามารถตรวจพบได้ สาเหตุทั่วไป: โฟลเดอร์อินพุตไม่ถูกรับรู้ว่าเป็นข้อมูลการจับภาพ (ตรวจสอบการจัดวางและ `--input-level`) หรือผลิตภัณฑ์ทุกชิ้นที่ร้องขอไม่สามารถใช้ได้กับกล้องเหล่านั้น (เช่น การร้องขอ radiance/reflectance จากกล้องที่รองรับเฉพาะRGB
)
* **การรันแบบเฉพาะเมตาดาต้าโดยเจตนา** (ปิดสวิตช์ทุกผลิตภัณฑ์, ไม่มี `--indices`) ยังคงถือว่าสำเร็จ — ผลลัพธ์ที่ถูกต้องในกรณีนี้คือภาพเอาต์พุตที่ว่างเปล่า
* ดำเนินการใหม่ด้วย `--verbose` และตรวจสอบบันทึกของระบบหลัง (backend log) เพื่อหาบรรทัด `[LATTICE-EXPORT]` / `[EXPORT-CHECK]` ซึ่งอธิบายการข้ามการประมวลผลตามกล้องแต่ละตัว
* บนWindows
ที่ปิดใช้งานเส้นทางยาว (long paths) ไว้ หากโฟลเดอร์โครงการลึกเกินไปจนเกินขีดจำกัดเส้นทาง 260 ตัวอักษร ระบบจะปฏิเสธ **ก่อน** ที่การประมวลผลจะเริ่มขึ้น โดยใช้โฟลเดอร์โครงการที่ยาวที่สุดที่ยังอยู่ในขีดจำกัด (ดู [Where the outputs land](#where-the-outputs-land)).
* คำสั่งจับภาพ (`lattice capture`, `lattice array-capture` และกลุ่มคำสั่งจับภาพอื่นๆ) จะระบุชื่อทุกไฟล์ที่เขียนไม่ได้และออกจาก `1`; การจับภาพซ้ำจะหยุดเมื่อพื้นที่เก็บข้อมูลเต็ม

รหัสออก: `0` สำเร็จ · `1` ความผิดพลาดทั่วไป · `2` ความผิดพลาดของอาร์กิวเมนต์ · `130` ถูกขัดจังหวะด้วย Ctrl+C.

***

## ดัชนีพืชพรรณ

เรียกใช้ `--indices` พร้อมชื่อที่กำหนดไว้ล่วงหน้าหนึ่งหรือหลายชื่อ; ดัชนีแต่ละตัวจะถูกจัดเก็บในโฟลเดอร์ `<INDEX>_Index_Images/` ของตัวเอง:

```bash
chloros-cli process ~/images/flight_001 --indices NDVI GNDVI OSAVI
```

22 ชื่อที่กำหนดไว้ล่วงหน้า `process --indices` ที่ระบบรับได้:

`NDVI` `GNDVI` `NDRE` `OSAVI` `SAVI` `MSAVI2` `EVI` `MSR` `TDVI` `LAI` `GCI` `GRVI` `GSAVI` `GOSAVI` `NLI` `MNLI` `RDVI` `WDRVI` `CVI` `ENDVI` `GLI` `VARI`

ชื่อที่โครงการยังไม่ได้ระบุจะถูกเพิ่มเข้าไปในโครงการ ชื่อที่ไม่ใช่ค่ากำหนดไว้ล่วงหน้า หรือชื่อที่แถบความถี่ของกล้องไม่สามารถสร้างได้ จะถูกข้ามไปพร้อมคำเตือนที่ระบุชื่อนั้น หากCLI
ไม่สามารถอ่านรายการดัชนีปัจจุบันของโครงการได้ มันจะทิ้งรายการไว้โดยไม่เปลี่ยนแปลงและแสดงคำเตือนแทนที่จะแทนที่มัน

{% hint style="warning" %}
**มีรายการดัชนีสามชุด — อย่าสับสนกัน**เมนูแบบเลื่อนลง &quot;Project Settings&quot; (การตั้งค่าโครงการ) ใน GUI มีสูตร 27 สูตร (เพิ่ม `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` — ห้าสูตรนี้ใช้ได้เฉพาะใน GUI และ**ไม่** ใช้ได้กับ `--indices`). คำสั่ง `lattice index --preset` แบบ live/offline ใช้รายการตั้งค่าล่วงหน้า 22 รายการของตัวเองที่แยกต่างหาก สูตรและคณิตศาสตร์แถบสเปกตรัมถูกบันทึกไว้ใน [สูตรดัชนีมัลติสเปกตรัม](project-settings/multispectral-index-formulas.md).
{% endhint %}

***

## เซ็นเซอร์แสง DAQ: การทดลองใช้งานเบื้องต้น

ตระกูล `daq pool-*` ควบคุมเซ็นเซอร์สเปกตรัม DAQ ของMAPIR
(DAQ-U ผ่าน USB, DAQ-M ผ่าน BLE, DAQ-E ผ่าน Ethernet) ผ่าน pool ที่คงอยู่ของระบบหลัง — GUI, CLI
และ SDK
ทั้งหมดใช้ handle แบบ live เดียวกัน **`pool-*` เป็นเส้นทาง DAQ ที่ได้รับการสนับสนุนในCLI
ที่มาพร้อมกับระบบ**; คำสั่งย่อย `daq` อื่นๆ ที่คุณอาจเห็นถูกอ้างอิงนั้น เป็นพื้นผิวภายในMAPIR
ที่ใช้สำหรับแหล่งข้อมูลเท่านั้น และจะออกด้วยข้อผิดพลาดที่ชัดเจน ซึ่งชี้ให้คุณไปยัง `pool-*`.

```bash
# 1. Open a pooled session (pick the line matching your sensor)
chloros-cli daq pool-connect                              # smart-detect
chloros-cli daq pool-connect --port COM3                  # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF      # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local   # DAQ-E by hostname (reliable)

# 2. List pooled sensors and their ids
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 3. Read the latest calibrated spectrum (W/m²/nm)
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

# 4. Record a calibrated .daq file for 60 s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 5. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

`pool-record` ที่ไม่มี `--duration` จะทำงานไปจนถึง `pool-record --stop`; ไดเรกทอรีผลลัพธ์เริ่มต้นคือ `~/Documents/DAQ Live View/` **บนเครื่องของแบ็กเอนด์**. โปรไฟล์การปรับแก้ cap ถูกเลือกในเวลาเชื่อมต่อ (`--cap-id`, ค่าเริ่มต้นของ backend คือ `sunshine_cosine`) และสามารถเปลี่ยนได้แบบเรียลไทม์ด้วย `pool-set-cap` — โปรไฟล์ cap และช่วงการปรับเทียบของเซ็นเซอร์ได้รับการอธิบายในบท DAQ ของคู่มือนี้

{% hint style="warning" %}
**DAQ-E บนโฮสต์ที่มีหลาย NIC:** การค้นพบอัตโนมัติ `pool-connect --eth` ครั้งแรกหลังการบูตอาจล้มเหลว แม้เซ็นเซอร์จะอยู่ในสภาพดี `--eth-host <ip-or-hostname>` เป็นรูปแบบที่เชื่อถือได้ — ให้ใช้รูปแบบนี้ทุกครั้งที่การค้นพบไม่พบผลลัพธ์
{% endhint %}

***

## กล้อง LATTICE, PTP และการอัตโนมัติของโครงการ

ตระกูล `lattice` (45+ subcommands) ครอบคลุมการทำงานของกล้อง LATTICE แบบครบวงจร: การค้นพบ, การถ่ายภาพครั้งเดียว, มصفوفاتที่ซิงโครไนซ์อย่างต่อเนื่องด้วยกระบวนการเชื่อมต่อ smart-prep ของ GUI, การดูตัวอย่างแบบสดผ่านเบราว์เซอร์, การจัดแนว, การคำนวณดัชนี, และการวินิจฉัย NIC ของโฮสต์ ตัวอย่าง:

```bash
chloros-cli lattice info                                          # discover cameras
chloros-cli lattice capture -o output/                            # one frame, all export types
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4       # persistent synced array
chloros-cli lattice array-capture --processing reflectance -o out/
```

นอกจากนี้: `chloros-cli time-sync` รายงานเกี่ยวกับ PTP grandmaster ที่โฮสต์Chloros
ทำงานอยู่ (กล้อง LATTICE และเซ็นเซอร์ DAQ-E เป็น slave เพื่อสร้าง timestamp ข้ามอุปกรณ์), และ `chloros-cli project` เปิดโครงการChloros
ที่บันทึกไว้และควบคุมกล้อง อาร์เรย์ และเซ็นเซอร์ของมันแบบ headless — รวมถึงสูตรการจับภาพ YAML ที่เขียนด้วยสคริปต์

กลุ่มทั้งสามนี้ (`lattice`, `project`, `daq pool-*`) เป็นกลุ่มเดียวที่รองรับ `CHLOROS_BACKEND_URL` สำหรับการควบคุม backend **ระยะไกล**; คำสั่งหลักจะมุ่งเป้าไปที่เครื่องท้องถิ่นเสมอ

คู่มือการใช้งานแบบละเอียดมีอยู่ในบท LATTICE ของคู่มือนี้; ทุกแฟล็กมีอยู่ใน [CLI
Reference](reference/cli-reference.md).

***

## การแก้ไขปัญหา: 5 ปัญหาหลัก

| อาการ | วิธีแก้ไข |
| --- | --- |
| `Login required` หรืองานที่กำหนดเวลาค้างอยู่ที่พรอมต์ `Email:` | ดำเนินการ `chloros-cli login EMAIL 'PASSWORD'` หนึ่งครั้งบนเครื่องนี้ — คำสั่งที่ไม่มีพรอมต์เซสชันที่เก็บไว้ในแคชจะทำงานแบบโต้ตอบแทนที่จะล้มเหลวอย่างรวดเร็ว |
| `backend unreachable` | เปิดแอปเดสก์ท็อป Chloros
หรือเริ่มทำงานแบ็กเอนด์ด้วยตนเอง: `"C:\Program Files\Chloros\backend\chloros-backend.exe"` บน Windows
(ในโฟลเดอร์ `backend` ของโฟลเดอร์ติดตั้ง; หากไม่มีอยู่ใน `PATH`), `chloros-backend` บนLinux
`chloros-cli backend status` แสดงว่ามีตัวใดกำลังทำงานอยู่หรือไม่; `lattice`/`project`/`daq pool-*` ไม่เคยเริ่มต้นเลย หากคุณกำหนดให้มันชี้ไปยัง backend ระยะไกล ให้ตรวจสอบ `CHLOROS_BACKEND_URL`. |
| การเชื่อมต่อ Array ถูกบล็อก: `FRAMES WILL DROP` / `Reduce ROI to enable` | วงรับสัญญาณ NIC ของโฮสต์ถูกรีเซ็ตกลับสู่ค่าเริ่มต้น — สาเหตุอันดับ 1 ที่ทำให้ระบบที่ทำงานได้ก่อนหน้านี้ไม่สามารถเชื่อมต่อได้ โดยทั่วไปเกิดขึ้นหลังการอัปเดตไดรเวอร์ NIC เรียกใช้ `chloros-cli lattice network --fix` จากเทอร์มินัล **ที่มีสิทธิ์ผู้ดูแลระบบ** (หรือตั้งค่า `ReceiveBufferLen=256`, `PendingReceives=64`); ดูส่วน *Host NIC Setup &amp; Tuning* ในเอกสารอ้างอิง |
| คำสั่งย่อย `daq` ออก: &quot;ต้องการแพ็กเกจ DAQ ครบถ้วน…&quot; | ใช้คำสั่ง `daq pool-*` ซึ่งควบคุมเซ็นเซอร์เดียวกันผ่านระบบหลังบ้าน: การเชื่อมต่อ, การส่งข้อมูลแบบสตรีม, การบันทึก, การเลือกช่วงค่าสูงสุด (cap selection) และสำหรับ DAQ-E ได้แก่ การตั้งค่าเครือข่าย, การรีสตาร์ท และการอัปเดตเฟิร์มแวร์ (หรือ `chloros_sdk.connect_daq_sensor()` จาก Python
) |
| Jetson แสดงคำเตือนเกี่ยวกับ swap ก่อนเปิดโฟลเดอร์ขนาดใหญ่ | เพิ่ม swap ที่ใช้ไฟล์เป็นฐาน — CLI
จะแสดงคำสั่ง `fallocate`/`swapon` ที่ต้องรันอย่างแม่นยำ |

***

## การขอความช่วยเหลือ

```bash
chloros-cli --help              # top-level help
chloros-cli process --help      # per-command help
chloros-cli lattice --help
chloros-cli daq --help          # lists the pool-* subcommands
```

* **ทุกแฟล็ก ทุกคำสั่งย่อย:** [คู่มืออ้างอิง CLI
](reference/cli-reference.md)
* **คำสั่งเทียบเท่าใน Python
:** [Python
SDK
](api-python-sdk.md) และ [คู่มืออ้างอิง SDK
](reference/sdk-reference.md)
* **การสนับสนุน:** info@mapir.camera · [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
