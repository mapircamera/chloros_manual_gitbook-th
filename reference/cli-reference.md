# Chloros CLI Reference

**เวอร์ชัน:**

1.2.0**สร้างขึ้น:**29 กรกฎาคม 2026 19:19 ·**แก้ไข:** 30 สิงหาคม 2026**กลุ่มเป้าหมาย:** ปรับให้เหมาะสมสำหรับการใช้งานของ LLM; อ่านได้โดยมนุษย์**ขอบเขต:** ทุกคำสั่งย่อยของ `chloros-cli` ที่ผู้ใช้สามารถใช้งานได้ พร้อมตัวเลือกและตัวอย่างที่สามารถคัดลอกและวางได้

เอกสารนี้เป็นคู่มืออ้างอิงครบถ้วนสำหรับเครื่องมือบรรทัดคำสั่ง `chloros-cli` ที่มาพร้อมกับ MAPIR Chloros เอกสารนี้ถูกออกแบบให้ครอบคลุมทุกด้าน เพื่อให้ LLM (หรือมนุษย์) สามารถสร้างเวิร์กโฟลว์ที่รองรับได้ทุกประเภทจากรายการด้านล่างโดยไม่ต้องตรวจสอบโค้ดต้นฉบับ

หากคุณต้องการเพียงส่วนสำคัญเท่านั้น ให้ข้ามไปยัง:
- [เริ่มต้นอย่างรวดเร็วใน 5 นาที](#five-minute-quickstart)
- [เวิร์กโฟลว์การเชื่อมต่อกล้อง LATTICE เป็นครั้งแรก](#lattice-camera-first-connect-workflow)
- [เวิร์กโฟลว์การเชื่อมต่อครั้งแรกของเซ็นเซอร์ DAQ](#daq-sensor-first-connect-workflow)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)
- [โหมดการจับภาพ, เครื่องบันทึก และกระบวนการประมวลผลใหม่แบบออฟไลน์](#capture-modes-recorders--offline-reprocess)

---

## กฎเกณฑ์

- ทุกคำสั่งมีคำนำหน้าเป็น `chloros-cli` สำหรับ Windows ไฟล์ไบนารีคือ `chloros-cli.exe` ส่วนสำหรับ Linux /Jetson คือ `chloros-cli`
- อาร์กิวเมนต์ที่เลือกใช้ได้จะแสดงเป็น `--flag` ส่วนอาร์กิวเมนต์ตำแหน่งที่จำเป็นจะแสดงโดยไม่ใช้เครื่องหมายวงเล็บ
- ในกรณีที่มีค่าเริ่มต้น หากไม่ระบุแฟล็ก ระบบจะใช้ค่านั้น
- CLI เป็นไคลเอนต์แบบบาง HTTP ที่ทำงานผ่านแบ็กเอนด์ Chloros (เซิร์ฟเวอร์ Flask บน `127.0.0.1:5000`) แบ็กเอนด์นี้จะถูกเริ่มต้นอัตโนมัติโดยคำสั่งส่วนใหญ่ `CHLOROS_BACKEND_URL=<url>` ชี้ไปยัง **`lattice`**,**`project`**และ**`daq pool-*`** ไปยังแบ็กเอนด์ระยะไกล — คำสั่งหลัก (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) จะกำหนดให้ `http://127.0.0.1:<port>` อยู่ในสถานะคงที่และเพิกเฉยต่อมัน (การใช้ IPv4 แบบตัวอักษรช่วยหลีกเลี่ยงการลงโทษประมาณ 2 วินาทีต่อคำขอของ Windows&#x27; `localhost`→`::1`). ดู [ตัวแปรสภาพแวดล้อม](#environment-variables).
- จำเป็นต้องใช้บัญชี Chloros+ เพื่อเข้าสู่ระบบสำหรับการเรียกใช้ SDK / CLI ทุกครั้ง (เรียกใช้ `chloros-cli login` ครั้งเดียวต่อเครื่อง; เก็บไว้ในแคชที่ `~/.chloros/`).
- ตัวอย่างใช้เส้นทาง Linux; บน Windows ให้แทนที่ `/home/user/...` ด้วย `C:/Users/.../...`.

---

## สรุปภาพรวมระดับสูงสุด

```
chloros-cli [global options] COMMAND [command options]
```

### ตัวเลือกทั่วไป

| ตัวเลือก | คำอธิบาย |
| --- | --- |
| `--backend-exe PATH` | กำหนดค่าทับตัวดำเนินการของ backend ที่ตรวจจับอัตโนมัติ |
| `--port N` | พอร์ต HTTP ของ backend (ค่าเริ่มต้น: `5000`). |
| `-v, --verbose` | เปิดใช้งานการแสดงผลแบบละเอียด |
| `--restart` | บังคับให้เริ่มต้นใหม่ backend (ปิดกระบวนการ `backend_server.py` ที่กำลังทำงานอยู่ทั้งหมด). |
| `--version` | แสดงเวอร์ชัน (`Chloros CLI 1.2.0`). |
| `--help` | แสดงคู่มือระดับสูงสุด |

### ดัชนีคำสั่ง

| คำสั่ง | วัตถุประสงค์ |
| --- | --- |
| [`process`](#chloros-cli-process) | ประมวลผลโฟลเดอร์ที่บันทึกข้อมูลแบบ end-to-end ด้วยSurvey3 หรือ LATTICE |
| [`login`](#chloros-cli-login) | ยืนยันตัวตนของเครื่องนี้ด้วยบัญชี Chloros+ |
| [`logout`](#chloros-cli-logout) | ลบข้อมูลรับรองที่เก็บไว้ในแคช |
| [`status`](#chloros-cli-status) | แสดงสถานะใบอนุญาต / การยืนยันตัวตนปัจจุบัน |
| [`export-status`](#chloros-cli-export-status) | แสดงความคืบหน้าการส่งออก Thread-4 แบบเรียลไทม์ระหว่างการรัน `process` | |
| [`language`](#chloros-cli-language) | ตั้งหรือแสดงรายชื่อภาษาแสดงผลของCLI (รองรับ 38 ภาษา) |
| [`set-project-folder`](#project-folder-commands) / [`get-project-folder`](#project-folder-commands) / [`reset-project-folder`](#project-folder-commands) | โฟลเดอร์โครงการเริ่มต้น (ใช้ร่วมกับ GUI) |
| [`update`](#chloros-cli-update) | ตรวจสอบและติดตั้งการอัปเดตCLI (Linux /Jetson). |
| [`selftest`](#chloros-cli-selftest) | การวินิจฉัยระบบ + การทดสอบเบื้องต้น |
| [`time-sync`](#chloros-cli-time-sync) | สถานะ/การควบคุม PTP grandmaster |
| [`lattice`](#chloros-cli-lattice) | การควบคุมและบันทึกภาพจากกล้อง LATTICE (45+ คำสั่งย่อย). |
| [`daq`](#chloros-cli-daq) | การควบคุมเซ็นเซอร์สเปกตรัม DAQ (DAQ-U / DAQ-M / DAQ-E). |
| [`project`](#chloros-cli-project) | เปิดและควบคุมโครงการChlorosที่บันทึกไว้ (กล้อง + DAQ) |

---

## การติดตั้ง

`chloros-cli` มาพร้อมกับตัวติดตั้งเดสก์ท็อป Chloros บนทุกแพลตฟอร์มที่รองรับ — ไม่มีไฟล์ดาวน์โหลดแยกต่างหากสำหรับ CLI การติดตั้งแพ็กเกจแพลตฟอร์มจะเพิ่ม `chloros-cli` เข้าสู่ `PATH` ของคุณ พร้อมกับแอปเดสก์ท็อปและไบนารีแบ็กเอนด์ ที่มันควบคุม

ไฟล์ดาวน์โหลดล่าสุด: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

> โปรแกรมติดตั้งยังมาพร้อมกับสคริปต์ตัวเปิดใช้งานที่สะดวก (`Chloros_CLI.bat` / `Chloros_CLI.ps1`, `Launch_CLI.*`, `chloros-cli.sh`) ที่เปิด shell CLI ที่พร้อมใช้งาน; ข้อมูลเกี่ยวกับสคริปต์เหล่านี้มีอยู่ใน [CLI User Guide](../CLI.md) และไม่ถูกซ้ำที่นี่

### Windows (.exe)

1. ดาวน์โหลดตัวติดตั้ง Windows จากหน้าดาวน์โหลด
2.  실행 `Chloros-Setup-x.y.z.exe` และทำตามขั้นตอนของตัวช่วยติดตั้ง เส้นทางติดตั้งเริ่มต้นคือ `C:\Program Files\Chloros\` (CLI จะถูกจัดเก็บใน `C:\Program Files\Chloros\cli\` ซึ่ง โปรแกรมติดตั้งจะเพิ่มเข้าไปใน PATH)
3. เปิดเทอร์มินัลใหม่ (`cmd.exe`, PowerShell หรือเทอร์มินัลของ Windows) เพื่อให้ระบบสามารถตรวจพบ `PATH` ที่อัปเดตแล้ว

```powershell
chloros-cli --version
```

โปรแกรมติดตั้งจะเพิ่ม `chloros-cli.exe` เข้าสู่ระบบ `PATH` ของคุณโดยอัตโนมัติ และรวม runtime Arena SDK ที่จำเป็นสำหรับกล้อง LATTICE

### Linux amd64 (.deb)

สำหรับ Ubuntu 22.04 LTS หรือเวอร์ชันใหม่กว่า / เครื่องทำงาน x86_64 ที่ใช้ระบบ Debian

> **Ubuntu 20.04 ไม่ได้รับการสนับสนุน** รายชื่อความพึ่งพาของแพ็กเกจนี้ถูกกำหนดจาก
> สิ่งที่แบ็กเอนด์เชื่อมโยงจริง ๆ ซึ่งรวมถึง `libc6 (>= 2.34)`;
> focal ส่งมอบ glibc 2.31 `apt` จะปฏิเสธการติดตั้งแทนที่จะปล่อยให้ล้มเหลวที่
> ขณะทำงาน

```bash
sudo dpkg -i chloros-amd64.deb
sudo apt-get install -f         # only if dpkg reports missing dependencies
chloros-cli --version
```

ไฟล์ .deb ติดตั้ง:
- `chloros-cli` ไปยัง `/usr/bin/chloros-cli`
- Backend ที่ถูกคอมไพล์ไปยัง `/usr/lib/chloros/chloros-backend`
- สภาพการทำงานของ Arena SDK (สำหรับกล้อง LATTICE)
- แบบจำลอง Denoiser, ชุดข้อมูลการปรับเทียบ และกำหนดค่าช่องอัปเดต

### Linux arm64 — Jetson (JetPack 6)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
sudo apt-get install -f
chloros-cli --version
```

มีโครงสร้างที่เหมือนกับไฟล์ .deb ของ amd64 แต่ใช้การคอมไพล์ CUDA ที่ปรับแต่งสำหรับ Jetson Orin / Orin NX / Orin Nano

### การยืนยันตัวตนครั้งเดียวต่อเครื่อง

ทุกแพลตฟอร์มจำเป็นต้องเข้าสู่ระบบ Chloros+ ครั้งเดียว ก่อนที่การเรียกใช้ SDK / CLI จะทำงานได้:

```bash
chloros-cli login user@example.com 'YourPassword'
```

ข้อมูลการเข้าสู่ระบบถูกเก็บไว้ในแคชที่ `~/.chloros/user_session.json`

### ตรวจสอบการติดตั้ง

```bash
chloros-cli --version           # prints "Chloros CLI 1.2.0"
chloros-cli selftest            # full 7-step diagnostic (backend, GPU, models, CUDA)
chloros-cli status              # shows license tier + logged-in user
```

> **Chloros+ จำเป็นต้องสมัครสมาชิก**CLI ต้องการแผน Chloros+ ที่ยังใช้งานอยู่**Copper**เป็นระดับเริ่มต้น Chloros+ — ทุกแผน Chloros+ ที่ชำระเงินแล้วมีสิทธิ์เข้าถึง CLI / SDK; เฉพาะระดับ**Iron** ที่ฟรีเท่านั้นที่ไม่มีสิทธิ์ (แผน-id map: `0`=Iron/free, `1`=Copper, `2`=Bronze, `3`=Silver, `4`=Gold.) อัปเกรดได้ที่ [`https://cloud.mapir.camera/pricing`](https://cloud.mapir.camera/pricing).
>
> ขีดจำกัดขั้นต่ำนี้ถูกบังคับใช้โดยระบบหลังบ้าน (backend) ไม่ใช่เพียงโดย CLI เท่านั้น: คำขอที่มีแฟล็ก SDK / CLI แต่ไม่มีแผนบริการแบบชำระเงินจะถูกปฏิเสธด้วยรหัสข้อผิดพลาด `403 PLAN_UPGRADE_REQUIRED` ไม่ว่าจะมาจาก `chloros-cli`, Python SDK หรือไคลเอนต์ที่พัฒนาเอง HTTP ผู้เรียกที่ออกจากระบบจะได้รับรหัสข้อผิดพลาด `401 AUTH_REQUIRED` แทน การเข้าถึงจะทำงานได้แบบออฟไลน์ในช่วงระยะเวลาผ่อนผันของแพ็กเกจ (30 วันต่อเดือน สำหรับแพ็กเกจรายเดือน และจนถึงวันหมดอายุสำหรับแพ็กเกจรายปี) และจะหยุดเมื่อระยะเวลาดังกล่าวหมดลง; รหัสข้อผิดพลาด `chloros-cli status` จะยังคงทำงานต่อไปเพื่อให้เหตุผลปรากฏชัดเจน (นั่นคือ เป็นเส้นทางเดียว SDK / CLI ที่ได้รับการยกเว้นจากข้อจำกัดระดับ — `GET /api/license-status`).

---

## คู่มือเริ่มต้น 5 นาที

```bash
# 1. Sign in once on this machine
chloros-cli login user@example.com 'YourPassword'

# 2. Survey3 / LATTICE folder → finished radiance + NDVI in one call
chloros-cli process "/home/user/captures/flight_001" \
  --vignette --reflectance --indices NDVI NDRE GNDVI

# 3. Take a single LATTICE photo with the first camera found
chloros-cli lattice capture -o output/

# 4. Connect a 4-cam LATTICE array with the GUI's smart-prep flow
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 5. Read a spectrum from a connected DAQ-U
chloros-cli daq pool-connect --port COM3
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F   # id from 'daq pool-list'
```

---

## `chloros-cli process`

ประมวลผลโฟลเดอร์ภาพผ่านกระบวนการเต็มรูปแบบของ Chloros (การตรวจจับเป้าหมาย → การปรับเทียบ → vignette → การสะท้อน → การส่งออกดัชนี).

### สรุป

```
chloros-cli process INPUT [OPTIONS]
```

### อาร์กิวเมนต์ตำแหน่ง

| อาร์กิวเมนต์ | คำอธิบาย |
| --- | --- |
| `INPUT` | เส้นทางไปยังโฟลเดอร์อินพุตที่ประกอบด้วยไฟล์ `.raw + .jpg` (Survey3), `.tif/.tiff` (LATTICE) หรือ `.dng` |

### ตัวเลือกทั่วไป

| ตัวเลือก | ค่าเริ่มต้น | คำอธิบาย |
| --- | --- | --- |
| `-o, --output PATH` | โฟลเดอร์ใหม่ที่มีเวลาประทับอยู่ภายใต้เส้นทางโครงการเริ่มต้นของคุณ (`~/Chloros Projects` เว้นแต่จะกำหนดค่าไว้) | โฟลเดอร์โครงการที่จะสร้างหรือใช้ซ้ำ หากโฟลเดอร์ดังกล่าวมีไฟล์ `project.json`, จะสร้างโฟลเดอร์ `_1`/`_2` ที่อยู่ข้างเคียงแทนที่จะเขียนทับ |
| `-n, --project-name NAME` | อัตโนมัติ (เวลาประทับ) | ชื่อโครงการ |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` ใช้การลบเดเบย์แบบประสาทChloros+; ช้ากว่าแต่คุณภาพดีกว่า |
| `--vignette / --no-vignette` | `--vignette` | การแก้ไขวิเนตต์ |
| `--reflectance / --no-reflectance` | `--reflectance` | การปรับเทียบค่าสะท้อน (ใช้เป้าหมายแผงหากพบ, การปรับเทียบตามหมายเลขซีเรียลของ NIST สำหรับ LATTICE). สำหรับ LATTICE multispectral ตัวเลือกนี้ยังทำหน้าที่เป็นสวิตช์ **ผลิตภัณฑ์** ค่าสะท้อน — ดู [สวิตช์ส่งออกตามผลิตภัณฑ์](#per-product-export-toggles-lattice-multispectral). |
| `--ppk` | off | ใช้การแก้ไข PPK GNSS จากไฟล์ sidecar |
| `--exposure-pin-1 MODEL` | off | ล็อก &quot;pin-1&quot; ของอุปกรณ์กล้องคู่Survey3 |
| `--exposure-pin-2 MODEL` | off | ล็อกโมเดล &quot;pin-2&quot; |
| `--recal-interval SECONDS` | 0 | บังคับให้คำนวณการปรับเทียบใหม่ทุก N วินาทีของเวลาการบันทึก |
| `--timezone-offset HOURS` | local | กำหนดค่าออฟเซ็ตเขตเวลาทับทับค่าที่ฝังอยู่ในเมตาดาต้าผลลัพธ์ |
| `--format FORMAT` | `TIFF (16-bit)` | หนึ่งใน `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)` |
| `--indices NAME [NAME ...]` | ไม่มี | ดัชนีพืชพรรณ (`NDVI`, `NDRE`, `GNDVI`, `EVI`, `SAVI`, `OSAVI`, `CIG`, …). |
| `--input-level {auto,raw,debayered,processed}` | `auto` | บังคับจุดเข้าของ pipeline สำหรับ LATTICE TIFF (ไฟล์ .raw ของ Survey3 ไม่ได้รับผลกระทบ) นอกจากนี้ยังมีทางออกฉุกเฉินที่อนุญาตให้ไฟล์ capture ที่ **ไม่มี raw*** — ดู [ลักษณะของโฟลเดอร์ capture](#what-a-captures-folder-looks-like). |
| `--debayered / --no-debayered` | on | ส่งผลลัพธ์การถอดเบย์เออร์แบบเชิงเส้น (`Debayered_Images`). ดู [สวิตช์ส่งออกตามผลิตภัณฑ์](#per-product-export-toggles-lattice-multispectral). |
| `--preview / --no-preview` | เปิด | ส่งภาพตัวอย่างบนหน้าจอ (`Preview_Images`): RGB = การปรับสมดุลสีขาว (แหล่งกำเนิดแสง DAQ-แหล่งแสงเมื่อมีอยู่ มิฉะนั้นใช้โลกสีเทา) + แกมมา; multispec = การขยายสีเท็จ |
| `--radiance / --no-radiance` | เปิด | ส่งค่า radiance แบบ float32 (`Radiance_Images`, W/m²/sr/nm). |
| `--reflectance-source {daq,target,auto}` | `auto` | อ้างอิงสำหรับผลิตภัณฑ์การสะท้อนแสง LATTICE: `auto` = เป้าหมายในเฟรมที่ผ่านการตรวจสอบคุณภาพ (QA-passing) เป็นอ้างอิงสัมบูรณ์, DAQ-downwelling (ρ = π·L/E) เป็นตัวเลือกสำรอง; `target` = แบบเคร่งครัด (ไม่ใช้การแทนที่ DAQ); `daq` = DAQ เป็นมาตรฐานหลัก. ดู [สวิตช์ส่งออกตามผลิตภัณฑ์](#per-product-export-toggles-lattice-multispectral). |
| `--target-reflectance-dir DIR` | ไม่มี | ไดเรกทอรีของ **การวัด** (`<serial>.csv`); หากไม่พบข้อมูล จะใช้สเปกตรัม T3/T4P มาตรฐานแทน |
| `--array-alignment / --no-array-alignment` | เปิด | มصفوف LATTICE: ใช้การปรับแนวโมดูล-to-module alignment ที่ถูกบันทึกใน XMP ของ `Chloros:Alignment*` ของการจับภาพแต่ละครั้ง ไปยังผลิตภัณฑ์ที่ผ่านการประมวลผลทุกชนิด (debayered / preview / radiance / reflectance / index) ไม่ดำเนินการสำหรับภาพที่ไม่มีแท็กดังกล่าว |
| `--array-alignment-crop / --no-array-alignment-crop` | crop | ตัดส่วนที่จัดแนวแล้วให้อยู่ในพื้นที่ทับซ้อนร่วมของอาร์เรย์ เพื่อให้ทุกโมดูลใช้พื้นที่เดียวกัน; `--no-…` รักษาพื้นที่เซ็นเซอร์เต็ม (เติมสีดำด้านนอกแหล่งที่มา) |
| `--array-alignment-interp {bilinear,nearest,cubic}` | `bilinear` | การปรับตัวอย่างใหม่ (resampling) สำหรับการบิดเบือนการจัดแนว (alignment warp) `nearest` รักษาค่า DN ของแหล่งที่มาไว้อย่างแม่นยำ (ไม่มีการผสมค่าเรดิโอเมตริกระหว่างพิกเซล) |

### ตัวเลือกการตรวจจับเป้าหมาย

| ตัวเลือก | คำอธิบาย |
| --- | --- |
| `--min-target-size PIXELS` | ขนาดเป้าหมายขั้นต่ำของแผง (px) สำหรับเครื่องตรวจจับ |
| `--target-clustering 0-100` | ความไวในการจัดกลุ่ม |
| `--target / --targets` | ถือว่าโฟลเดอร์ที่ป้อนเข้าเป็น เป้าหมายเฉพาะแผง (ข้ามการตรวจจับแบบสำรวจ) |

### ตัวอย่าง

```bash
# Simplest: defaults are good for most surveys
chloros-cli process "/home/user/images/survey_001"

# Multi-index with explicit format
chloros-cli process "/home/user/images/survey_001" \
  --vignette \
  --reflectance \
  --format "TIFF (32-bit, Percent)" \
  --indices NDVI NDRE GNDVI OSAVI

# Texture-aware debayer for highest quality (Chloros+ only)
chloros-cli process "/home/user/images/survey_001" \
  --debayer texture-aware \
  --indices NDVI

# Process LATTICE captures explicitly (auto-detects from EXIF normally)
chloros-cli process "/home/user/captures/lattice_flight" \
  --input-level processed

# LATTICE multispectral → float32 radiance only (no DAQ downwelling needed)
chloros-cli process "/home/user/captures/lattice_flight" \
  --no-debayered --no-preview --no-reflectance

# LATTICE reflectance anchored to an in-frame target (strict, no DAQ fallback),
# with per-unit measured target scans looked up by serial
chloros-cli process "/home/user/captures/lattice_flight" \
  --reflectance-source target --target-reflectance-dir "/home/user/target_scans"

# LATTICE array capture: keep native geometry (ignore stamped alignment)
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment

# Aligned, uncropped, value-preserving resampling
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment-crop --array-alignment-interp nearest

# Save to a custom output location with a project name
chloros-cli process "C:/input" -o "C:/output" -n "Field_A_2026-05-26"
```

### สวิตช์การส่งออกตามผลิตภัณฑ์ (LATTICE multispectral)

การประมวลผล LATTICE จะกระจายไปยัง **ทุกผลิตภัณฑ์ที่เกี่ยวข้องในครั้งเดียว**. สวิตช์สี่ตัวตามประเภท — `--debayered`, `--preview`, `--radiance`, `--reflectance` — ล้วน**เปิดอยู่ตามค่าเริ่มต้น**; ใช้รูปแบบ `--no-<type>` เพื่อปิดสวิตช์หนึ่งตัว RGB master cams ส่งข้อมูลออกเฉพาะแบบ debayered + preview (ไม่มี radiance/reflectance ตามแต่ละแถบความยาวคลื่น) ดังนั้น `--radiance`/`--reflectance` จึงไม่ทำงานสำหรับกล้องเหล่านี้ การสลับสถานะจะถูกเพิกเฉยสำหรับ Survey3 `.raw` (ซึ่งใช้เส้นทาง reflectance/target ตามมาตรฐาน) *(ธง `--radiometric-output {reflectance,radiance,sensor-response}` แบบเก่าได้ **ถูกลบ** และแทนที่ด้วยการสลับสถานะเหล่านี้; ไม่มีระดับ `sensor-response` อีกแล้ว.)*

| ผลิตภัณฑ์ | ผลลัพธ์ | จำเป็นต้องใช้ DAQ downwelling? |
| --- | --- | --- |
| `--debayered` | การถอดโมเสกแบบเชิงเส้น (`Debayered_Images`). | ไม่ |
| `--preview` | การแสดงตัวอย่าง (`Preview_Images`): RGB = WB + gamma; multispec = false-colour stretch. | ไม่ |
| `--radiance` | float32 W/m²/sr/nm จากห่วงโซ่รังสีวัดเต็มรูปแบบ (`Radiance_Images`). | No. |
| `--reflectance` | uint16 ค่าสะท้อนแสง ρ (`32768` = 1.0), พร้อมใช้งานกับ Pix4D. | **ใช่**เว้นแต่มีเป้าหมายภายในเฟรมที่ผ่านการตรวจสอบคุณภาพ (QA) มาตรฐานยึดตำแหน่งไว้ (ดูด้านล่าง). |

`--reflectance-source` เลือกจุดอ้างอิงการสะท้อนแสง:**`auto`**(ค่าเริ่มต้น) ใช้เป้าหมายภายในเฟรมที่ผ่านการตรวจสอบคุณภาพ (QA) เป็น**จุดอ้างอิงสัมบูรณ์**— ห่วงโซ่เส้นเชิงประจักษ์ที่ผูกกับเป้าหมายจะถูกประเมินข้ามกันบนแผงที่เก็บไว้ และผู้ชนะที่วัดได้จะถูกนำไปใช้ — กลับสู่การแบ่งการลงของ DAQ (ρ = π·L/E) เมื่อไม่มีเป้าหมายหรือ QA ล้มเหลว;**`target`**เป็นแบบเคร่งครัด (ไม่มีการแทนที่ด้วย DAQ);**`daq`**เลือกใช้พฤติกรรมที่อิงตาม DAQ เป็นหลัก รูปทรงเป้าหมาย (ArUco / ROI คงที่ / แถบ) มาจากกำหนดค่าเป้าหมายของโครงการ; `--target-reflectance-dir DIR` เก็บการสแกน**ที่วัดได้** ต่อหน่วย (`<serial>.csv`) ที่ค้นหาตามหมายเลขซีเรียล/QR ของหน่วยเป้าหมาย โดยใช้สเปกตรัม T3/T4P เป็นตัวเลือกสำรอง

เส้นทางสะท้อนแสงของ DAQ จะปรับ **เวลาประทับ**ที่ตรงกัน**จาก**`.daq`**(DAQ-U/M/E) ที่บันทึกไว้**หรือ `.csv` แบบเนทีฟของ DAQ-M**ที่พบพร้อมกับภาพ หากชุดข้อมูลการปรับเทียบ**กล้องหรือ DAQ**ต่อหน่วย-กล้องหรือชุดข้อมูลการปรับเทียบ DAQ ไม่ได้ถูกเก็บไว้ในแคชท้องถิ่น ระบบ**จะดึงข้อมูลนั้นจาก AWS โดยอัตโนมัติ** เมื่อใช้งานครั้งแรก (ต้องเชื่อมต่ออินเทอร์เน็ตเพียงครั้งเดียว; ข้อมูลจะถูกเก็บไว้ในแคชภายใต้ชื่อ `~/.chloros/`)

#### การอ่านพิกเซลการสะท้อนแสง (Pix4D / Metashape / สคริปต์ของคุณเอง)

ค่าการสะท้อนถูกเก็บไว้เป็น DN แบบจำนวนเต็ม และ **ค่า DN ที่แสดงว่า ρ = 1.0 ขึ้นอยู่กับกล้องต้นทาง**:

| กล้องต้นทาง | ρ = 1.0 คือ | วิธีตรวจสอบ |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (มีพื้นที่ว่างถึง ρ 2.0) | XMP `Chloros:PixelScale=32768` ถูกประทับลงบนไฟล์ |
| Survey3 | `65535` (ถูกตัดที่ ρ 1.0) | ไม่มีแท็ก XMP `Chloros:*` — การไม่มีแท็กนี้ *คือ* สัญญาณ |

**อ่านค่า `Chloros:PixelScale` แล้วหารด้วยค่านั้น** แทนที่จะสมมติว่าเป็นค่าคงที่ แท็กนี้ถูกกำหนดในโดเมน uint16 ดังนั้นมันจะยังคงเป็น `32768` ในทุกรูปแบบผลลัพธ์ที่มีการปรับสเกล — `TIFF (16-bit)`, `PNG (8-bit)`, `JPG (8-bit)` และ `TIFF (32-bit, Percent)` ทั้งหมดล้วนเป็นแบบที่อธิบายตัวเองได้ (ปรับประเภทข้อมูลที่เก็บไว้ให้กลับเป็น uint16 ก่อน: ×257 จาก 8-bit, ×65535 จาก float).

> **มีกรณีหนึ่งที่ตามการออกแบบแล้วไม่มีการปรับสเกล** เมื่อการจับภาพจากแหล่งที่มา 8-bit (BayerRG8) ถูกเขียนเป็น 8-bit TIFF, ระบบจะ *ตัดค่า* ให้อยู่ในช่วง 0..255 แทนที่จะปรับสเกลใหม่ ดังนั้นค่าทุกค่าที่เกิน ρ≈0.008 จะถูกลดระดับเป็น 255 และไฟล์นั้นจะไม่มีการกำหนดสเกล Chloros ได้ละเว้นทั้ง `Chloros:PixelScale` และ `MicaSense:RadiometricCalibration` tuple ที่นั่น และบันทึกเหตุผลไว้ **หากแท็กนี้ไม่มีในไฟล์ reflectance ของ LATTICE อย่าสมมติว่ามีสเกล — ส่งออกใหม่ที่ 16-bit หรือ 32-bit** แทนที่จะแบ่งพิกเซลที่ไม่สามารถแบ่งได้

#### EXIF ที่ถูกส่งต่อไปยังไฟล์ส่งออก

`process` คัดลอก **บล็อก GPS และ ExifIFD** ของการจับภาพต้นฉบับไปยังทุกผลิตภัณฑ์ ดังนั้น
การส่งออกจะนำ `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` และ
`CameraSerialNumber` ไปพร้อมกับข้อมูลการอ้างอิงตำแหน่งทางภูมิศาสตร์

**`FocalLength` ไม่ใช่ตัวเลือกสำหรับงานโฟโตแกรมเมตรี** Pix4D คำนวณระยะตัวอย่างพื้นดินจาก
ความยาวโฟกัสบวกกับความสูง; หากแท็กนี้ขาดหายไป ระบบจะกลับไปใช้มาตราส่วนที่ผิดพลาดอย่างรุนแรง ในหนึ่ง
เที่ยวบินถ่ายภาพสวนส้ม 49 ภาพ แท็กที่ขาดหายไปทำให้พื้นที่ขนาด 411 ม. × 160 ม. ถูกสร้างใหม่เป็น
47.8 km × 13 km — ภาพออร์โธเมตริก 455 MP ที่ส่วนใหญ่เป็นข้อมูลว่าง (nodata) ซึ่งถูกตีความว่าเป็นปัญหาการจัดเรียงไทล์และ
ปัญหา BigTIFF ก่อนที่ใครจะตรวจสอบ GSD หากภาพออร์โธของคุณออกมาในสเกล
ที่ไม่น่าเชื่อถือ ให้รัน `exiftool -FocalLength` บนผลิตภัณฑ์ที่ส่งออกมาก่อน

สำเนานี้ **ไม่ได้** ใช้ `-all:all` โดยเจตนา: แท็กโครงสร้างของ IFD0 จะทำให้ผลลัพธ์ของ LATTICE เสียหายเมื่อ
คัดลอก, และ `ExifImageWidth` / `ExifImageHeight` ถูกยกเว้นเพราะพวกมันอธิบายการ
*จับภาพต้นทาง* — การส่งออกที่เคยถูกปรับขนาดจะนำขนาด
ที่ขัดแย้งกับราสเตอร์ของตัวเองมาด้วย XMP ถูกเขียนโดยตรงแทนที่จะคัดลอก เพราะ ExifTool
จะทิ้งแท็ก XMP ที่ถูกเรียกใช้ในครั้งเดียวกันเมื่อบล็อก XMP ถูกคัดลอก (ซึ่งจะทำให้แท็กการปรับเทียบ MAPIR
ถูกลบไป)

### สถานที่จัดเก็บผลลัพธ์

ผลิตภัณฑ์จะถูกเขียน **ในโฟลเดอร์โครงการ โดยจัดกลุ่มตามกล้องและตามรูปแบบไฟล์**:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── <INDEX>_Index_Images/        # e.g. NDVI_Index_Images
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

โฟลเดอร์กล้องคือ `LATT-<sensor>-<lens>-F<filter>` สำหรับ LATTICE (ตรงกับ EXIF
`Model`) และ `<model>_<filter>` สำหรับ Survey3 — กล้องสองตัวที่ใช้เซ็นเซอร์และฟิลเตอร์ร่วมกัน แต่มี
เลนส์ที่แตกต่างกัน จึงถูกจัดเก็บในโครงสร้างโฟลเดอร์แยกกัน เนื่องจากปรากฏการณ์วินเนตต์ มุมมองภาพ และความบิดเบี้ยวแตกต่างกัน รูปแบบ
folder ตามรูปแบบ `--format`: `tiff16`, `tiff8`, `png8`, `jpg8` หรือ `tiff32` สำหรับ
`TIFF (32-bit, Percent)`

> **ทุกผลิตภัณฑ์ที่ส่งออกจะรักษาชื่อไฟล์ต้นฉบับ** การส่งออก radiance ของ
> `capture_…_raw.tif` ยังคงถูกเรียกว่า `capture_…_raw.tif` — มันเพียงแต่อยู่ภายใน
> `tiff32/Radiance_Images/` **โฟลเดอร์คือตัวระบุผลิตภัณฑ์ ไม่ใช่ชื่อไฟล์** ดังนั้นการค้นหาแบบกลอบ
> สำหรับ `*radiance*.tif` จะไม่พบอะไร; ให้ใช้การค้นหาในไดเรกทอรีแทน

### การบันทึกจากเซ็นเซอร์แสง — `.daq` + `.csv` ที่ได้รับการปรับเทียบ

`process` ยังจัดการกับข้อมูลบันทึก `.daq` ในโฟลเดอร์ข้อมูลเข้าของคุณด้วย และมัน **ไม่**
ต้องการภาพใดๆ เพื่อดำเนินการ: DAQ-U / DAQ-M / DAQ-E ที่บินด้วยตัวเองถือเป็นการ
บันทึกข้อมูลที่สมบูรณ์ และโฟลเดอร์ที่มีเพียงไฟล์ `.daq` เท่านั้นก็ถือเป็นข้อมูลเข้าที่ถูกต้อง

DAQ สามารถบันทึก **โดยไม่ต้อง** มีการสอบเทียบ — นั่นคือสิ่งที่เครื่องบันทึกสาธารณะ
[`chloros_scripts`](https://github.com/mapircamera/chloros_scripts)
(`record_daq.py`) ทำตามค่าเริ่มต้น: พวกมันเขียนค่าการนับจากเซ็นเซอร์แบบดิบและประทับเวลาในไฟล์เพื่อให้
Chloros ดึงข้อมูลการสอบเทียบจากโรงงานของเซ็นเซอร์นั้น **ผ่านหมายเลขซีเรียล** (จากแคชท้องถิ่นก่อน,
แล้วจาก Cloud ของ MAPIR) และนำไปใช้ `process` เขียนผลลัพธ์กลับออกมา:

```
<project>/
└── Light Sensor/
    ├── <name>_calibrated.daq        # reprocessable archive, declares its bundle
    └── <name>_calibrated.csv        # W/m^2/nm per reading + photometric columns
```

`.csv` ส่งข้อมูลหนึ่งแถวต่อการอ่านค่า: เวลาประทับตรา UTC, เวลาการรวมค่า, กำลังรวม,
lux แบบ photopic/scotopic, PPFD (และส่วนแบ่งสีฟ้า/เขียว/แดง), ความยาวคลื่นสูงสุด, แล้ว
สเปกตรัมเต็มบนกริดความยาวคลื่นของเซ็นเซอร์เอง `.daq` นำเข้าข้อมูลใหม่โดยไม่
ต้อง calibrate อีกครั้ง

หากดำเนินการสำเร็จ รายงานการรันจะแสดงเป็น `Light-sensor products written: N (calibrated .daq + .csv)`
ส่วนที่อยู่ในวงเล็บอธิบายสิ่งที่ถูกเขียนจริง ดังนั้นจะอ่านเป็น
`(RAW COUNTS — this sensor has no calibration bundle)` สำหรับเซ็นเซอร์ที่ไม่มีชุดข้อมูล และ
`(N calibrated, M raw counts)` สำหรับโฟลเดอร์ที่เก็บทั้งสองไว้ หัวข้อของระบบหลังบ้านเอง
`[DAQ-EXPORT]` และ `[RUN-SUMMARY]` กำหนดคำอธิบายด้วยวิธีเดียวกัน — ไม่มี
หัวข้อใดในสามหัวข้อนี้ที่สามารถเรียกการส่งออกข้อมูลดิบว่าได้รับการปรับเทียบแล้ว

การบันทึกของ DAQ-U / DAQ-M / DAQ-E ที่บันทึกลง ซึ่งไม่สามารถดึงชุดข้อมูลการปรับเทียบได้ — คุณ
อยู่ในโหมดออฟไลน์ หรือเซ็นเซอร์นั้นไม่มีข้อมูลการปรับเทียบในไฟล์ — จะ **ถูกข้ามไปพร้อมเหตุผล** บนบรรทัด
`[DAQ-EXPORT]` — ไม่เคยถูกบันทึกเป็นไฟล์ &quot;ที่ผ่านการปรับเทียบ&quot; ซึ่งเก็บค่าดิบไว้
เชื่อมต่อกับอินเทอร์เน็ตและรันใหม่ เหตุผลคือเหตุผลที่ผู้อ่านได้
กำหนดไว้สำหรับไฟล์นั้น (สคีมาอ่านไม่ได้, ไม่มีชุดข้อมูล, ข้อผิดพลาดในการเขียน) และรายการสรุป
สรุปการรันจะแสดง **เหตุผลที่ต่างกัน** — ไฟล์ 20 ไฟล์ที่ถูกข้ามไปเนื่องจากสาเหตุเดียวจะแสดงเป็น
สาเหตุเดียว ไม่ใช่การซ้ำของสาเหตุนั้น 20 ครั้ง

#### การส่งออกข้อมูลบันทึก DAQ-A ในรูปแบบค่าดิบ

ตระกูล **DAQ-A** มีอยู่ก่อนระบบบันเดิลต่อซีเรียล และไม่มีบันเดิลการสอบเทียบ
ให้ดึงข้อมูล — แต่ถูกปรับเทียบในสนามโดยใช้เป้าหมายการสะท้อนแสงแทน ซึ่ง
เป็นเหตุผลที่มันไม่เคยต้องการบันเดิลเลย การปฏิเสธบันทึกเหล่านั้นทำให้ไม่มีทาง
นำตัวเลขออกมาได้เลย ดังนั้นจึงส่งออกภายใต้ **ชื่อที่ต่างกัน**:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq        # NOT _calibrated
    └── <name>_raw.csv        # raw spectral sensor counts, NOT irradiance
```

ใช้ชื่อไฟล์ที่ต่างออกไปแทนที่จะใช้แฟล็กภายในไฟล์ เพราะข้อมูลนี้ต้องคงอยู่
เมื่อส่งต่อทางอีเมลด้วยชื่อไฟล์เปล่าๆ ส่วนส่วนหัว `.csv` ระบุว่า
`raw spectral sensor counts (NOT irradiance)` และเตือนว่าค่าเหล่านั้นสามารถเปรียบเทียบได้
**ภายใน** ไฟล์ — ซึ่งคือวัตถุประสงค์ที่การปรับเทียบแบบเป้าหมายใช้ข้อมูลเหล่านี้ — และ
ไม่ใช่ระหว่างเซ็นเซอร์ต่าง ๆ คอลัมน์โฟโตเมตริกที่ขึ้นอยู่กับกำลัง (กำลังรวม, ลักซ์แบบโฟโตปิกและ
สโคโตปิก, PPFD) ถูกเขียนเป็น **NULL** แทนที่จะคำนวณจากการนับ และสรุปการรัน
สรุปการรันระบุว่า `RAW COUNTS` ดังนั้นข้อมูลที่ &quot;ส่งออก&quot; ในไฟล์บันทึกจึงไม่สามารถอ่านเป็นความเข้มรังสีได้

การบันทึกแบบเก่า **v1.01 / v1.02** (ที่ DAQ-A-SD เขียนไว้) ไม่มี epoch สำหรับแต่ละการอ่าน
มีเพียงเวลาเขียนของไฟล์เท่านั้น เครื่องจับคู่ภาพ↔แสงลงยังคงปฏิเสธข้อมูลเหล่านี้ — การจับคู่
เฟรมกับเวลาเขียนจะผิดพลาดโดยที่มองไม่เห็น — แต่ตัวส่งออกสามารถอ่านข้อมูลเหล่านี้ได้ และ
CSV จะพิมพ์ `clock=daq_created_on` ดังนั้นผลิตภัณฑ์จึงระบุได้ว่ากำลังใช้ clock ตัวใด

### หมายเหตุ

- `process` จะตรวจจับอัตโนมัติว่าโฟลเดอร์ของคุณเป็นSurvey3 LATTICE หรือแบบผสม
- ความคืบหน้าถูกส่งผ่าน Server-Sent Events; CLI แสดงความคืบหน้าแบบเรียลไทม์ต่อแต่ละเธรด (Detecting, Analyzing, Processing, Exporting).
- สำหรับ Linux /Jetson, CLI จะตรวจสอบพื้นที่ swap และอาจแจ้งเตือนก่อนการประมวลผลโฟลเดอร์ขนาดใหญ่ ส่วน debayer ที่รองรับ Texture- debayer ที่รองรับ Texture จะตั้งค่าจำกัดความถี่ GPU โดยอัตโนมัติบน Jetson ที่ใช้พลังงานต่ำ (Nano, Orin Nano)
- เมื่อดำเนินการสำเร็จ รายงานจะแสดงจำนวนผลิตภัณฑ์ภาพที่เขียนได้ (`Image products written: N`)

#### การดำเนินการที่ไม่ได้เขียนภาพจะล้มเหลว

หากคุณร้องขอผลิตภัณฑ์และ การรันเขียน **ไม่มี** — มีเพียง `project.json` และ
`calibration_data.json` — `process` จะถือว่านั่นเป็นความล้มเหลว: มันจะพิมพ์
`Processing finished but wrote no image products.` และ **ออกค่าที่ไม่ใช่ศูนย์** ดังนั้นสคริปต์จึงสามารถ
ตรวจพบได้ ข้อความดังกล่าวระบุชื่อโฟลเดอร์โครงการและสาเหตุทั่วไป ได้แก่:

- โฟลเดอร์ข้อมูลเข้าไม่ได้รับการระบุว่าเป็นข้อมูลการจับภาพ (ตรวจสอบการจัดวางและ `--input-level`), หรือ
- ผลิตภัณฑ์ทุกชนิดที่ขอถูกข้ามไปเนื่องจากไม่เหมาะสมกับกล้องเหล่านั้น (เช่น การขอ
  ค่า radiance/reflectance จากกล้องที่รองรับเฉพาะ RGB เท่านั้น).

รันใหม่ด้วย `--verbose` และตรวจสอบบันทึกของระบบหลัง (backend log) สำหรับ `[LATTICE-EXPORT]` / `[EXPORT-CHECK]`,
ซึ่งอธิบายการข้ามข้อมูลตามกล้องแต่ละตัว ที่ไม่ปรากฏในผลลัพธ์ของCLI

การรันแบบตั้งใจให้ใช้เฉพาะเมตาดาต้า — ปิดทุกผลิตภัณฑ์และไม่มี `--indices` — ยังคงเป็น
**ความสำเร็จ** เพราะผลลัพธ์ภาพที่ว่างเปล่าคือผลลัพธ์ที่ถูกต้องในกรณีนี้**การรันที่ใช้เฉพาะเซ็นเซอร์แสง** ก็เช่นกัน: โฟลเดอร์ที่บันทึกด้วย `.daq` ไม่มีภาพที่จะส่งออก
ตามนิยาม และการรันนี้ถูกประเมินจากไฟล์ `.daq` / `.csv` ที่ถูกเขียนแทน

---

## `chloros-cli login`

ยืนยันตัวตนของเครื่องนี้ด้วยบัญชีคลาวด์ Chloros+ ข้อมูลรับรองจะถูกเก็บไว้ในแคชอย่างปลอดภัยใน `~/.chloros/user_session.json`

```
chloros-cli login EMAIL PASSWORD
```

### ตัวอย่าง

```bash
chloros-cli login user@example.com 'YourPassword'

# Passwords containing $ should use SINGLE quotes
chloros-cli login user@example.com 'my$ecret$pass'
```

> **PowerShell `$$` mangling is auto-corrected.** In double quotes PowerShell expands `$$` (การตัดส่วนจาก หรือคัดลอกส่วนหนึ่งของรหัสผ่าน). เมื่อได้รับข้อผิดพลาด 401 ระบบ CLI จะลองใหม่โดยอัตโนมัติด้วย `$$` ที่ต่อท้ายใหม่ แล้วตามด้วยครึ่งหนึ่งของรหัสผ่านที่กำจัดส่วนซ้ำแล้ว; หากการลองใหม่สำเร็จ ระบบจะเข้าสู่ระบบให้คุณและแสดงไวยากรณ์เครื่องหมายคำพูดเดี่ยวที่ถูกต้องเพื่อใช้ในครั้งต่อไป

> **การใช้งานแบบไม่มีอินเทอร์เฟซผู้ใช้/ผ่านสคริปต์: การไม่มีเซสชันที่เก็บไว้ในแคชหมายความว่าจะปรากฏพรอมต์แบบโต้ตอบ ไม่ใช่การล้มเหลวอย่างรวดเร็ว** คำสั่งใดก็ตามที่สร้างกระบวนการหลัง (`process`, `status`, `export-status`, `time-sync`, …) ที่ทำงานโดยไม่มี ใบอนุญาต/เซสชันที่เก็บไว้ในแคช จะเข้าสู่หน้าต่างโต้ตอบ `Email:` / `Password:` บน stdin ก่อนที่จะดำเนินการต่อ ดังนั้น งานที่ทำงานโดยไม่มีผู้ดูแลและไม่มีเซสชันที่เก็บไว้ในแคช จะค้างอยู่ระหว่างรอข้อมูลเข้า — ให้เรียกใช้ `chloros-cli login EMAIL PASSWORD` ครั้งต่อเครื่องก่อนที่จะจัดกำหนดงานแบบไม่มีหน้าจอ

---

## `chloros-cli logout`

ล้างเซสชันที่เก็บไว้ในแคชและบังคับให้เข้าสู่ระบบใหม่ในการเรียกครั้งต่อไป

```bash
chloros-cli logout
```

---

## `chloros-cli status`

แสดงระดับใบอนุญาตปัจจุบัน (Iron/Copper/Bronze/Silver/Gold), ผู้ใช้ที่ได้รับการยืนยันตัวตน และจำนวนการผูกอุปกรณ์

```bash
chloros-cli status
```

---

## `chloros-cli export-status`

ตรวจสอบความคืบหน้าการส่งออก Thread-4 แบบเรียลไทม์ สามารถเรียกใช้ได้อย่างปลอดภัย **ระหว่าง** การทำงานของ `process` จากเชลล์อื่น

```bash
chloros-cli export-status
```

---

## `chloros-cli language`

ตั้งค่าภาษาแสดงผลของ CLI (รองรับ 38 ภาษา รวมถึง CJK, RTL และ Indic) จะเปลี่ยนกลับไปใช้ภาษาอังกฤษอย่างราบรื่นบนคอนโซลรุ่นเก่าที่ไม่สามารถแสดงผลสคริปต์ได้

```
chloros-cli language [LANG_CODE] [--list]
```

### ตัวอย่าง

```bash
# List all available languages
chloros-cli language --list

# Switch to Spanish
chloros-cli language es

# Show the currently-active language
chloros-cli language
```

---

## คำสั่งสำหรับโฟลเดอร์โครงการ

คำสั่งเหล่านี้ใช้จัดการตำแหน่งโฟลเดอร์โครงการเริ่มต้น (ใช้ร่วมกับ GUI)

```bash
chloros-cli set-project-folder "/home/user/Chloros Projects"
chloros-cli get-project-folder
chloros-cli reset-project-folder
```

---

## `chloros-cli update`

Linux/ เฉพาะ Jetson เท่านั้น ตรวจสอบ `version_url` จาก `/etc/chloros/update.conf` และเสนอให้ดาวน์โหลด + ติดตั้ง `.deb` ที่ตรงกัน

```bash
chloros-cli update            # check + install
chloros-cli update --check    # check only
```

บน Linux /Jetson โปรแกรม CLI ยัง **ตรวจสอบการอัปเดตอัตโนมัติทุกครั้งที่เริ่มต้น** (ไม่บล็อก ไม่ทำให้คำสั่งล่าช้า): มันอ่าน `/etc/chloros/update.conf` เก็บผลลัพธ์ไว้ในแคช XPR เป็นเวลา 1 ชั่วโมงOTX000307 และพิมพ์ `Update available: vX.Y.Z / Run: chloros-cli update` เมื่อมีเวอร์ชันใหม่กว่า จะข้ามไปโดยเงียบหากเกิดข้อผิดพลาดหรือเมื่ออยู่ในสถานะ Windows

---

## `chloros-cli selftest`

ดำเนินการทดสอบเบื้องต้น 7 ขั้นตอน: เวอร์ชัน, ความพร้อมใช้งานของพอร์ต, การเริ่มต้นระบบหลังบ้าน, `/api/test`, `/api/system-info` (GPU/CUDA/PyTorch), การมีอยู่ของโมเดลลดสัญญาณรบกวน, ความพร้อมของ CUDA+โมเดลลดสัญญาณรบกวน

```bash
chloros-cli selftest
```

---

## `chloros-cli time-sync`

สถานะและการควบคุม PTP grandmaster เครื่องโฮสต์Chlorosทำงานเป็น PTP grandmaster; LATTICE cams และ DAQ-E units ทำงานเป็น slave เพื่อสร้าง timestamp ข้ามอุปกรณ์

| Subcommand | Description |
| --- | --- |
| `status` | แสดงสถานะ grandmaster, ลำดับความสำคัญ BMCA, และเอกลักษณ์นาฬิกา |
| `peers` | แสดงรายการอุปกรณ์สเลฟที่ตรวจพบผ่าน Delay_Req (กล้อง + เซนเซอร์ DAQ-E) |
| `cameras` | ต่อ- PTP Health ของกล้องแต่ละตัว (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`). |
| `restart` | เริ่มต้นกระบวนการ grandmaster ใหม่ |
| `set-priority --priority1 N --priority2 N` | กำหนดลำดับความสำคัญ BMCA ใหม่ |

### ตัวอย่าง

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
chloros-cli time-sync cameras
chloros-cli time-sync restart
chloros-cli time-sync set-priority --priority1 1 --priority2 1
```

---

## `chloros-cli lattice`

การควบคุมกล้อง LATTICE ทุกคำสั่งย่อยจะถูกส่งผ่านแบ็กเอนด์Chloros; แบ็กเอนด์นี้เป็นเจ้าของกลุ่มกล้อง (cam pool) ดังนั้นการเรียกใช้คำสั่งCLI ในครั้งต่อไปจะใช้ handle ที่เปิดอยู่เดิมซ้ำ

### ตัวเลือกทั่วไป (ใช้ร่วมกันโดยคำสั่งย่อยส่วนใหญ่)

| แฟล็ก | คำอธิบาย |
| --- | --- |
| `-d, --device N` | ดัชนีกล้อง (ค่าเริ่มต้น: 0). |
| `-s, --serial SN` | หมายเลขซีเรียลเฉพาะ; แทนที่ `--device` |
| `--serials SN1,SN2,…` | หมายเลขซีเรียลที่คั่นด้วยเครื่องหมายจุลภาค สำหรับการทำงานกับกล้องหลายตัว | |
| `--all` | ทำงานกับกล้องทุกตัวที่ตรวจพบ |
| `--exposure US` | เวลาเปิดรับแสงในไมโครวินาที |
| `--gain DB` | ค่าเกนใน dB | |
| `--pixel-format FMT` | เช่น `BayerRG8`, `BayerRG12`. |
| `--width N` / `--height N` | ขนาดภาพ |
| `--preset {default,high_quality,high_speed,triggered}` | ใช้การตั้งค่าล่วงหน้า ทุกอย่างทำงานแบบอิสระ ยกเว้น `triggered` ซึ่งจะตั้งกล้องให้พร้อมรับสัญญาณขอบฮาร์ดแวร์บนสาย 2 — หากไม่มีสัญญาณใดส่งไปยังสายนั้น ระบบจะรออยู่ตลอดไปแทนที่จะบันทึกภาพ |
| `-o, --output DIR` | โฟลเดอร์ผลลัพธ์ (ค่าเริ่มต้น: `output`) |
| `--packet-size {auto,jumbo,standard,N}` | ขนาดแพ็กเก็ต GVSP `auto` ดำเนินการตรวจสอบ ICMP+GVSP; `jumbo` = 9000; `standard` = 1500. |

### กระบวนการเชื่อมต่อครั้งแรกของกล้อง LATTICE

```bash
# 1. Discover cameras on the network
chloros-cli lattice info

# 2. Single-cam smoke test: capture one frame.
#    By default this saves EVERY export type applicable to the cam
#    (raw, debayered, radiance, reflectance, preview). Pass e.g.
#    `--processing debayered` to save just one.
chloros-cli lattice capture -o output/

# 3. Connect a synchronized array (RECOMMENDED ENTRY POINT for arrays).
#    This is the same "smart-prep" flow the Chloros GUI uses:
#      - Network capability probe (ICMP DF ping + GVSP probe)
#      - Tier auto-pick (sim-emit / ftd-stagger / slip)
#      - Auto-shrink frame size to fit the wire
#      - PTP enabled by default
#      - Per-cam pixel format auto-pick
#      - AE seeding from the cam's saved state
#      - GPIO trigger config on Line2
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 4. Capture one synced frame group from the live array.
#    Defaults to --processing all (one file per export type per cam);
#    pass a single level to narrow it, e.g. --processing reflectance.
chloros-cli lattice array-capture --processing reflectance -o output/

# 5. Live-preview one cam in your browser
chloros-cli lattice viewer --serial 213800234

# 6. Tear down when done
chloros-cli lattice array-disconnect
```

### คู่มือคำสั่งย่อย

#### การค้นหาและข้อมูล

| คำสั่งย่อย | วัตถุประสงค์ |
| --- | --- |
| `lattice info` | แสดงรายชื่อกล้องที่เชื่อมต่อ (ผู้ผลิต, รุ่น, หมายเลขซีเรียล, IP, MAC). |
| `lattice probe [--pixel-format FMT] [--json] [--no-discover]` | วิเคราะห์ระบบโฮสต์เพื่อกำหนดค่ากล้องที่เหมาะสมที่สุด `--no-discover` จะข้ามขั้นตอนการค้นหากล้อง (เร็วขึ้น, วิเคราะห์เฉพาะ NIC เท่านั้น) |
| `lattice network [--fix] [--estimate] [--cameras N]` | ตรวจสอบ/แก้ไขการตั้งค่า NIC; ประมาณค่าแบนด์วิดท์/FPS |
| `lattice network-analysis --master SN --slaves SN1,SN2,… [--width N] [--height N] [--pixel-format FMT] [--binning N] [--force-tier TIER] [--backend-url URL] [--json]` | ความสามารถเครือข่ายของแบ็กเอนด์แบบสคีมาเสถียร + คำแนะนำเกี่ยวกับอาร์เรย์ (ส่งคืน `status` ∈ `ok` / `auto_shrunk` / `auto_capped_fps` / `needs_force_slip` / `error`). `auto_capped_fps` รักษาความละเอียดที่ขอไว้ แต่จำกัด fps เป้าหมาย — อ่านค่า `recommended.recommended_target_fps` และส่งมันเป็นเป้าหมายการเชื่อมต่อ; ถือว่าเป็นการสำเร็จ ไม่ใช่ข้อผิดพลาด |
| `lattice analyze-array [--models M1,M2,…] [--binning N] [--n-active N] [--width N] [--height N] [--pixel-format FMT] [--force-tier TIER] [--json]` | การวิเคราะห์แบบ What-if โดยไม่เปิดกล้อง **`--n-active` คือจำนวนกล้องทั้งหมดบนสาย ไม่ใช่เพียงในอาร์เรย์นี้**— ส่งค่านี้เมื่อกล้องแบบสแตนด์อโลนสตรีมพร้อมกัน, หรืองบประมาณสายถูกคำนวณจากความต้องการที่นับจำนวนกล้องไม่ครบ (ค่าเริ่มต้น: `len(--models)`) จะแสดงค่า `Wire budget:` รวมเสมอ (MB/s ที่ต้องการ เทียบกับขีดจำกัดที่ปลอดภัยจากการชนกัน) และ `Max cameras:` และตั้งค่าสถานะ `** OVER-SUBSCRIBED**` เมื่ออาร์เรย์ใช้สายเกินขีดจำกัด — ดู [โมเดล fps &amp; burst ของอาร์เรย์](#array-fps--burst-model). |
| `lattice gpu` | แสดงสถานะ GPU |
| `lattice firmware [--update] [--force] [-y\|--yes]` | ตรวจสอบหรืออัปเดตเฟิร์มแวร์กล้อง การเลือก `.fwa` ในเครื่องถูกตรึงไว้: ไฟล์ใน `firmware/<MODEL_PREFIX>/` ที่ตรงกับ `MIN_FIRMWARE_VERSION` ของเวอร์ชันจะถูกแฟลชเมื่อมีอยู่ (ใช้เวอร์ชันสูงสุดเป็นทางเลือกสำรองเท่านั้น), ดังนั้นภาพจากผู้ผลิตเวอร์ชันใหม่กว่าที่จัดเตรียมไว้บนดิสก์จะยังไม่ทำงานจนกว่าการล็อกนั้นจะถูกยกเลิก — เวอร์ชันใหม่กว่าจะถูกส่งไปยังอุปกรณ์อย่างตั้งใจผ่าน AWS manifest ที่ลงนามแล้ว ซึ่งวิธีนี้ถูกแนะนำเมื่อมีเวอร์ชันใหม่กว่า |
| `lattice presets [--apply NAME]` | แสดงรายการหรือใช้การตั้งค่าล่วงหน้าของกล้อง |
| `lattice status` | สถานะกล้องแบบเรียลไทม์ |

#### การจับภาพ

| คำสั่งย่อย | วัตถุประสงค์ |
| --- | --- |
| `lattice capture [--format tiff\|png\|jpg] [--jpeg-quality N] [--processing LEVEL] [--levels L1,L2,…] [--force-daq]` | ภาพเฟรมเดียว **บันทึกทุกประเภทการส่งออกตามค่าเริ่มต้น** (`--processing all`); ดู [ระดับการส่งออกการจับภาพ](#capture-export-levels-the-all-default). `--levels` บันทึกชุดย่อยที่กำหนดไว้อย่างชัดเจน (แทนที่ `--processing`); `--force-daq` เขียนค่าการอ่าน DAQ ที่กำหนดไว้เป็น sidecar `.daq` แม้ในการจับข้อมูลแบบ raw เท่านั้น `--jpeg-quality` = JPEG คุณภาพ 1–100 (ค่าเริ่มต้น 95). |
| `lattice continuous [--format tiff\|png\|jpg] [--jpeg-quality N] [--queue-depth N]` | ส่งข้อมูลไปยังดิสก์จนกว่าจะกด Ctrl+C. |
| `lattice viewer [--brightness N] [--ae-damping F] [--frame-rate FPS]` | การดูตัวอย่าง MJPEG แบบสดผ่านเบราว์เซอร์ `--ae-damping` ตั้งค่าการลดทอนการปรับแสงอัตโนมัติ (0.4–100). |

#### การปรับแต่งเซ็นเซอร์

| คำสั่งย่อย | วัตถุประสงค์ |
| --- | --- |
| `lattice configure [--get N1 N2…] [--set N=V N=V…] [--dump] [--json]` | อ่าน/เขียนโหนด GenICam ใดก็ได้. |
| `lattice exposure [--auto] [--auto-once] [--off] [--set US] [--brightness N] [--damping F] [--upper-limit US]` | การเปิดรับแสงและ AE |
| `lattice gain [--auto] [--off] [--set DB]` | การเพิ่มสัญญาณและปรับเพิ่มสัญญาณอัตโนมัติ |
| `lattice resolution [--set WxH] [--offset X,Y] [--binning N] [--binning-mode Sum\|Average]` | พื้นที่สนใจ (ROI) ของเซ็นเซอร์และการรวมพิกเซล |
| `lattice format [--set FMT] [--list]` | รูปแบบพิกเซล |
| `lattice trigger [--mode On\|Off] [--source SRC] [--delay-us US] [--activation EDGE] [--list-sources] [--software]` | ไตรเกอร์ฮาร์ดแวร์/ซอฟต์แวร์ | |
| `lattice white-balance [--auto] [--off] [--red R] [--blue B]` (ไม่มีแฟล็ก = WB แบบ one-shot) | การทำงาน WBRGB/กล้อง Bayer เท่านั้น; ไม่ทำงาน (ถูกข้าม) บน M3M แบบโมโน |
| `lattice color-profile [--set raw\|linear\|natural\|enhanced\|custom_temp] [--cct K] [--get]` | RGB ท่อสีการแสดงผล | `natural` (ค่าเริ่มต้น) เป็นกระบวนการปรับแต่งแบบสดที่ประหยัด; `enhanced` เพิ่มการกำจัดขอบสี + ความสดใส + CLAHE (ความคมชัดท้องถิ่น) เพื่อสร้างลุค hub-parity แบบเต็มรูปแบบ ด้วยค่าใช้จ่ายในการปรับแต่งต่อเฟรมประมาณ 2 เท่า จึงทำให้ **อัตราเฟรมแบบสด** ต่ำลง — ภาพที่บันทึกไว้จะได้รับการปรับแต่งแบบเต็มรูปแบบเสมอไม่ว่าจะใช้ค่าใดก็ตาม RGB /Bใช้ได้กับกล้องแบบหลายชั้นเท่านั้น; ถูกข้ามไปสำหรับ M3M แบบโมโน |
| `lattice color [--saturation N] [--contrast N] [--reset] [--get]` | ปรับความอิ่มตัว/ความคมชัดของภาพ (กล้องที่ใช้ฟิลเตอร์ RGB). ถูกข้ามไปสำหรับ M3M แบบโมโน |
| `lattice filter [--set NAME] [--list]` | ตั้งค่าโมเดลฟิลเตอร์ของกล้อง(`RGN-IMX265`, `OCN`, `NGB`, …). |
| `lattice power [--sleep]` | ตรวจสอบจุดพลังงาน/จุดความร้อนของโพรบ; เปิด/ปิดโหมดพักใช้พลังงานต่ำ |

#### การปรับเทียบและเซ็นเซอร์

| คำสั่งย่อย | วัตถุประสงค์ |
| --- | --- |
| `lattice calibrate [--filter NAME] [--attempts N] [--save PATH]` | ปรับเทียบจากเป้าหมายการสะท้อนแสง |
| `lattice dls [--connect] [--spectrum] [--irradiance] [--mac MAC] [--filter NAME] [--json]` | คำสั่งสำหรับเซ็นเซอร์แสงลงที่ติดตั้งในตัว |
| `lattice vignette --input DIR --output DIR [--lens-model KEY]` | ปรับแก้การเบลอขอบภาพ (vignette) ให้กับภาพที่มีอยู่ |

#### หลายกล้อง (เซสชันชั่วคราว)

| คำสั่งย่อย | วัตถุประสงค์ |
| --- | --- |
| `lattice multi-info` | แสดงรายชื่อกล้องทั้งหมดที่มีบทบาทการซิงค์ |
| `lattice multi-capture [--format FMT] [--jpeg-quality N] [--processing LEVEL]` | เฟรมที่ซิงค์หนึ่งเฟรมจากแต่ละกล้อง | บันทึก **ทุกประเภทการส่งออกตามค่าเริ่มต้น**เมื่อมีการเชื่อมต่ออาร์เรย์แบบถาวร; ตัวสำรองแบบชั่วคราวที่ไม่มีอาร์เรย์จะ**ผ่านการเดเบเยอร์เท่านั้น** (รัน `array-connect` ก่อนสำหรับส่วนที่เหลือ). |
| `lattice multi-stream [--fps F] [--count N] [--format FMT] [--jpeg-quality N]` | สตรีมเฟรมที่ซิงค์ (ชั่วคราว). |
| `lattice multi-test [--count N]` | ทดสอบเวลาซิงค์ GPIO. |
| `lattice multi-detect [--line LINE] [--json]` | ตรวจหาการต่อสาย GPIO แบบ master/slave โดยอัตโนมัติ |

#### การจัดแนว

| คำสั่งย่อย | วัตถุประสงค์ |
| --- | --- |
| `lattice align-calibrate [--method orb\|akaze\|phase\|checkerboard\|manual] [--model translation\|rigid\|affine\|homography] [--frames N] [--checkerboard RxC] [--points PATH] [--reference SN] [--save PATH] [--preview] [--vignette] [--prefilter none\|gradient\|clahe\|blur\|hist_match] [--rms-threshold-px N]` — พร้อมปุ่มปรับตัวตรวจจับ/ตัวจับคู่ `[--max-features N] [--ratio-threshold F] [--matcher bf\|flann] [--knn-k N]`, ปุ่มปรับ RANSAC `[--ransac-threshold-px F] [--ransac-iters N] [--ransac-confidence F]`, การรวมหลายเฟรม `[--averaging mean\|median\|inlier_weighted]`, ข้อจำกัดทางเรขาคณิต `[--lock-rotation] [--lock-scale] [--lock-axis x\|y]`, ข้อจำกัดทางพื้นที่ `[--roi X0,Y0,X1,Y1] [--mask PATH]`, และการแทนที่สำหรับแต่ละ slave `[--per-cam-override SN:KEY=VALUE]` (สามารถทำซ้ำได้) | คำนวณโปรไฟล์การจัดแนวจากกล้องที่ทำงานอยู่. `--prefilter` ตั้งค่าเริ่มต้นเป็น `gradient` (แผนที่ขอบ; สอดคล้องกับตัวจัดแนว GUI/array — ขอบยังคงอยู่ข้ามแถบสเปกตรัม). `--matcher flann` ให้ผลลัพธ์ที่ดีเมื่อมีคุณลักษณะมากกว่า ~5000; `--averaging median` มีความทนทานต่อการจับภาพที่ผิดพลาดหนึ่งครั้ง, `inlier_weighted` ให้ความสำคัญตามจำนวนการจับคู่; `--lock-scale` ทำการฉายไปยังการหมุนที่ใกล้ที่สุด (ไม่ปรับสเกล), `--lock-axis` ตั้งค่าองค์ประกอบความเลื่อนหนึ่งเป็นศูนย์; `--mask` ใช้ได้กับทุกกล้อง (ใช้ `--per-cam-override` สำหรับการตั้งค่าต่อกล้อง เช่น `--per-cam-override 214701292:method=phase`). `--rms-threshold-px` ปฏิเสธการบันทึกการปรับเทียบที่ค่า RMS ของการฉายซ้ำเกินค่ากำหนด |
| `lattice align-apply --profile PATH [--format tiff\|png] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-camera] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode constant\|replicate\|reflect\|wrap] [--border-value N]` | ถ่ายภาพเฟรมหลายแถบที่จัดแนวแล้วหนึ่งเฟรม `--bit-depth` ตั้งค่าเริ่มต้น ให้ตรงกับกล้อง; `--no-crop` เก็บเฟรมเต็ม (เติมด้วยสีดำ); `--interpolation` (ค่าเริ่มต้น `linear`) และ `--border-mode`/`--border-value` (ค่าเริ่มต้น `constant`/0) ควบคุมการบิดเบือนของ CPU — ส่วนทาง GPU จะใช้การบิดเบือนแบบ bilinear ไม่ว่าค่าจะตั้งเป็นอย่างไร |
| `lattice align-stream --profile PATH [--fps F] [--count N] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode MODE] [--border-value N]` | ส่งเฟรมหลายแถบที่จัดแนวแล้ว (ใช้ค่าการบิดเบือนเดียวกันเหมือน `align-apply`). |
| `lattice align-info --profile PATH [--json]` | แสดงรายละเอียดโปรไฟล์ |
| `lattice align-reorder --profile PATH [--order NAMES] [--enable SERIALS] [--disable SERIALS]` | เปลี่ยนลำดับชั้น |

#### ดัชนี / การคำนวณพืชพรรณ

```bash
# Offline: compute NDVI from an aligned multi-band TIFF
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --output ndvi.tif --colorize --gradient RdYlGn

# Live: discover array, calibrate alignment, capture, compute index, in one go
chloros-cli lattice index --live --profile align.json --preset NDVI \
  --save-multiband -o output/
```

ชุดธงเต็ม: `--input PATH | --live --profile PATH`, `--preset NAME` (NDVI / NDRE / EVI / SAVI / GNDVI /…), `--formula EXPR`, `--channel SYM=BAND` (สามารถทำซ้ำได้), `--capture-level raw|debayered|radiance|reflectance|unknown` (แทนที่ระดับการบันทึกที่บันทึกไว้ในแหล่งที่มา TIFF; ค่าเริ่มต้น: อ่านจากข้อมูลเมตาดาต้าของ TIFF), `--output PATH`, `--output-format all|raw|tif|colorized|lut|png`, `--gradient NAME|JSON`, `--vmin/--vmax/--percentile LO,HI`, `--bg-mode clip|transparent|indexColor|backgroundColor`, `--colorize`, `--list-presets`, `--list-gradients`. สำหรับ `--live` ปุ่มปรับการบิดเบือนการจัดแนวก็ใช้ได้เช่นกัน: `--save-multiband`, `--gpu/--no-gpu`, `--no-crop`, `--bit-depth 8|12|16`, `--vignette`, `--interpolation nearest|linear|cubic|lanczos`, `--border-mode constant|replicate|reflect|wrap`, `--border-value N`.

> **`--channel` สัญลักษณ์เหล่านี้มีความไวต่อตัวอักษรใหญ่-เล็ก** ด้านสัญลักษณ์ต้องตรงกับชื่อช่องของพรีเซ็ตอย่างแม่นยำ (การตั้งค่าล่วงหน้าใช้ตัวอักษรเล็ก เช่น NDVI = `red`, `nir` — ตรวจสอบ `--list-presets`), และด้าน band ต้องตรงกับชื่อ band ใน stack ที่จัดเรียง (หรือเป็นดัชนีแบนด์ที่เริ่มจาก 0 ในโหมดออฟไลน์) `--channel red=Red_660 --channel nir=NIR_850` ทำงานได้; `--channel RED=660` ล้มเหลวพร้อมข้อผิดพลาด `channel_map missing entries`.

#### การเชื่อมต่อแบบคงที่ (Smart-Prep, กระบวนการที่เทียบเท่า GUI)

คำสั่งเหล่านี้จะรักษาการเชื่อมต่อกล้องไว้ในกลุ่มกล้องด้านหลัง (backend pool) ระหว่างการเรียกใช้คำสั่งCLI

| คำสั่งย่อย | วัตถุประสงค์ |
| --- | --- |
| `lattice cam-connect [--serial SN]` | เพิ่มกล้องหนึ่งตัวเข้าสู่กลุ่ม (กล้องเดียว, ไม่ใช่อาร์เรย์) |
| `lattice cam-disconnect [--serial SN] [--all]` | ปล่อย |
| `lattice cam-list` | แสดงรายการกล้องในพูล |
| **`lattice array-connect`**|**เชื่อมต่ออาร์เรย์ที่ซิงโครไนซ์แบบถาวร (จุดเริ่มต้นที่ THE แนะนำ)** ดำเนินการตามขั้นตอน smart-prep ผ่าน GUI อย่างครบถ้วน |
| `lattice array-disconnect [--array-id ID] [--all]` | ปล่อยอาร์เรย์ |
| `lattice array-list` | แสดงรายชื่ออาร์เรย์ที่เชื่อมต่อ |
| `lattice array-status [--array-id ID]` | Live fps, PTP, ข้อผิดพลาดล่าสุด |
| `lattice array-capture [--processing LEVEL\|all] [--levels L1,L2,…] [--aligned\|--no-aligned] [--index\|--no-index] [--force-daq] [--smart] [--fastest] [--compression deflate\|none] [--continuous\|--interval S] [--count N] [--duration S]` | การจับภาพที่ซิงโครไนซ์หนึ่งครั้งจากอาร์เรย์แบบเรียลไทม์ — แบบเดี่ยว / แบบต่อเนื่อง / แบบช่วงเวลา / แบบเร็วที่สุด **ค่าเริ่มต้นคือ `all`** (ไฟล์หนึ่งต่อประเภทการส่งออกที่เหมาะสมต่อกล้องหนึ่งตัว). กล้องที่ถูกข้าม (เช่น RGB ที่ถูกยกเว้นจาก radiance/reflectance) จะถูกรายงานด้วย `Skipped: SN:<serial> (<reason>)`; ค่าอ่าน DAQ ที่ใช้สำหรับ reflectance จะถูกบันทึกไว้พร้อมกันและรายงานด้วย `DAQ: <path>` ดู [โหมดการจับภาพ, เครื่องบันทึก และกระบวนการประมวลผลใหม่แบบออฟไลน์](#capture-modes-recorders--offline-reprocess). |
| `lattice array-record [--fps F] [--duration S] [--gif] [--gif-only]` | บันทึกภาพรวมดัชนีแบบสดเป็นวิดีโอ/GIF (ระดับการเฝ้าติดตาม; ต้องเปิดสตรีมรวมไว้). |
| `lattice array-burst [--duration S] [--max-frames N] [--build] [--products …]` | การถ่ายภาพต่อเนื่องแบบ raw-Bayer ที่อัตราเฟรมสูง (ระดับการวิเคราะห์; ประมวลผลใหม่แบบออฟไลน์). |
| `lattice array-build-video --burst-dir DIR [--products …] [--fps F] [--save-tiffs] [--gif]` | ประมวลผลใหม่ชุดภาพ raw ที่บันทึกไว้เป็นวิดีโอที่ผ่านการปรับเทียบ |

##### `array-connect` ตัวเลือก

| ตัวเลือก | ค่าเริ่มต้น | คำอธิบาย |
| --- | --- | --- |
| `--serials SN1,SN2,…` | ค้นหาอัตโนมัติกล้อง LATTICE ทั้งหมด (ต้องมี ≥2) | กล้องซีเรียลตัวแรกจะเป็น MASTER เมื่อไม่ระบุ ตัวค้นหาจะกรองเฉพาะรุ่น LATTICE (`TRI032*`) และเชื่อมต่อทั้งหมด |
| `--line {Line0,Line2,Line3}` | `Line2` | สายซิงค์ GPIO. |
| `--target-fps F` | อัตโนมัติ | อัตราการยิงของ Master |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | อัตโนมัติ | กำหนดค่าทับตัวเลือกระดับ |
| `--wire-ceiling-mbps MB_PER_S` | ตรวจจับอัตโนมัติ | **งบประมาณสายสัญญาณต่อเนื่องของโฮสต์ (MB/s) — ค่าที่การจัดสรรพื้นที่จัดเก็บข้อมูลทั้งหมดขึ้นอยู่กับ** ลดค่านี้ลงเมื่ออาร์เรย์รายงานเฟรม GVSP ที่เสียหาย: ค่าอัตโนมัติถูกคำนวณจากความเร็วลิงก์ที่ NIC ประกาศ ซึ่งอาจสูงเกินจริงสำหรับอะแดปเตอร์ USB เลน PCIe ที่บาง และโครงสร้างเครือข่ายที่ใช้ร่วมกันซึ่งมีภาระงานสูง ค่านี้ถูกบันทึกไว้ในบล็อกการจับภาพอาร์เรย์ของโครงการ ดังนั้นการเปิดใหม่ / CLI / SDK การเชื่อมต่อใหม่จะคืนค่าเดิม ดู [Array health](#array-health--which-subsystem-is-losing-frames). |
| `--binning {1,2,4}` | auto | การจัดกลุ่มฮาร์ดแวร์ |
| `--no-recommend` | off | ข้ามขั้นตอนการวิเคราะห์เครือข่าย |
| `--no-ptp` | off | ปิด PTP (ซึ่งทำให้ **ไม่สามารถ** เปรียบเทียบเวลาประทับตราข้ามกล้องได้) |

### Smart-AE / Smart-Capture

อาร์เรย์ LATTICE จะทำงาน AE อย่างต่อเนื่องในพื้นหลังทันทีที่เชื่อมต่อ แต่ฉากที่เพิ่งกำหนดใหม่จะใช้เวลาสักครู่เพื่อให้ค่า AE บรรจบกัน `array-capture --smart` เป็น **แพ็กเกจที่สะดวก**: มันจะรอให้ค่า AE บรรจบกันในทุกกล้องใน ในอาร์เรย์ ก่อนที่จะเริ่มการถ่ายภาพ ใช้ฟังก์ชันนี้เมื่อเปลี่ยนฉากระหว่างการถ่ายภาพ

```bash
# Connect once, then take settled captures whenever you re-point the rig
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4
chloros-cli lattice array-capture --smart --processing reflectance -o pose_a/
# (move the rig)
chloros-cli lattice array-capture --smart --processing reflectance -o pose_b/
```

นโยบายการคงที่ (settle policy) ตั้งค่าเริ่มต้นแบบระมัดระวัง: เวลาหมดอายุ 5 วินาที, 1.5 วินาทีสำหรับช่วงเวลาความเสถียร, ความทนทานต่อการกระจายการเปิดรับแสง ±5 % ปรับแต่งผ่านตัวกำหนดค่าอัตโนมัติ (SDK) (`ArrayHandle.capture_smart(settle_timeout_s=…, stability_window_s=…, exposure_tolerance_pct=…)`) หากต้องการพฤติกรรมที่แตกต่างจากระบบอัตโนมัติ

### ระดับการส่งออกภาพ (ค่าเริ่มต้น `all`)

ตั้งแต่เวอร์ชันนี้ `lattice capture`, `lattice multi-capture`, และ `lattice array-capture` **ตั้งค่าเริ่มต้นเป็น `--processing all`** — ไฟล์ที่บันทึกไว้หนึ่งไฟล์ต่อประเภทการส่งออก ซึ่งใช้กับกล้องแต่ละตัว ตามพฤติกรรม &quot;Capture All&quot; ใน GUI ระดับต่าง ๆ มีดังนี้:

| ระดับ | ผลลัพธ์ | ใช้กับ |
| --- | --- | --- |
| `raw` | Bayer 1 ช่อง (กล้องโมโนโครม: แถบเดียว) จากเซ็นเซอร์โดยตรง | ทุกกล้อง |
| `debayered` | การถอดโมเสก BGR 3 ช่อง (กล้องโมโนโครม: 1 ช่องสัญญาณสีเทา). | ทุกกล้อง |
| `radiance` | float32 W/m²/sr/nm ผ่านห่วงโซ่รังสีวัดเต็มรูปแบบ | Multispectral (M3C/M3M) เท่านั้น — **ข้ามไปสำหรับกล้องที่มีตัวกรองRGB** |
| `reflectance` | uint16 ρ (`32768` = 1.0), พร้อมสำหรับ Pix4D | เฉพาะแบบมัลติสเปกตรัม และ **เฉพาะเมื่อ DAQ ถูกผูกไว้ + กล้องถูกปรับเทียบ**; หากไม่เป็นเช่นนั้นจะถูกข้าม |
| `preview` / `display` | โซ่การพรีวิว GUI แบบเต็ม (CCM + WB + gamma ตามโปรไฟล์ของกล้อง). `lattice capture` ตั้งชื่อเป็น `preview`; `array-capture`/`multi-capture` ใช้ `display`. | ทุกกล้อง. |

ส่งระดับเดียวเพื่อบันทึกเฉพาะระดับนั้น (`--processing debayered`) เมื่อคุณขอ `all` ระดับที่ไม่เกี่ยวข้องกับแคมที่กำหนดจะถูกข้ามไป (และรายงาน) โดยไม่เกิดข้อผิดพลาด — กล้องที่ไม่ได้เชื่อมต่อหรือไม่ได้ปรับเทียบยังคงได้รับ `raw` / `debayered` / `preview`

สำหรับเฟรมการสะท้อนแสงใดๆ, ค่าการอ่านลงของ DAQ ที่ใช้จริงจะถูกเขียนลงใน **`.daq`** sidecar ข้างๆ ภาพ (เพื่อให้สามารถประมวลผลการจับภาพใหม่ได้ในภายหลัง) และรายงานในบรรทัด `DAQ:`

### โครงสร้างของโฟลเดอร์การจับภาพ มีลักษณะอย่างไร

แต่ละประเภทการส่งออกจะถูกจัดเก็บใน **โฟลเดอร์ย่อยของตัวเอง** ภายใต้ `-o` ดังนั้นการจับภาพหลายระดับจะไม่มีการผสมประเภท:

```
output/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when --index is on
├── composite/     array foreground/background live-view composite, when produced
└── *.daq          the downwelling reading matched to the capture
```

`<ts>` คือเวลาบันทึกภาพ และ `<serial>` คือหมายเลขซีเรียลของกล้อง ดังนั้นกลุ่มที่ซิงค์กันจะใช้
เวลาบันทึกภาพเดียวกันสำหรับกล้องทุกตัว **โปรดสังเกตความไม่สมมาตร**:** ระดับ `display` ถูกเก็บไว้ในโฟลเดอร์
ที่มีชื่อ `preview/` ในขณะที่ไฟล์เองยังคงมี `_display` ในชื่อ — ชื่อโฟลเดอร์และส่วนท้ายชื่อไฟล์จะแตกต่างกัน
เฉพาะระดับนั้นเท่านั้น ระดับที่ไม่รู้จักจะกลับใช้โฟลเดอร์ที่มีชื่อตามระดับนั้นเอง และหากไม่สามารถสร้างโฟลเดอร์ย่อย
ได้ ไฟล์จะถูกเขียนไปยังรากของโฟลเดอร์ผลลัพธ์ แทนที่จะสูญหาย

**การประมวลผลใหม่โฟลเดอร์ captures:**ชี้ `chloros-cli process` ที่**ราก captures**
(`output/`). `process` โดยปกติจะนำเข้าเพียงโฟลเดอร์ที่คุณตั้งชื่อเท่านั้น, แต่เมื่อโฟลเดอร์นั้นไม่มี
ภาพใดเลยและมีโฟลเดอร์ย่อยอยู่ มันจะขยายลงไปโดยอัตโนมัติ — ดังนั้น โฟลเดอร์ย่อยระดับรากและ
โฟลเดอร์ราก `.daq` จะถูกนำเข้ามาทั้งหมดในครั้งเดียว ทุกระดับของ capture จะถูกนำเข้าเป็นภาพเดียว
โดยระดับอื่น ๆ จะปรากฏเป็นโหมด แทนที่จะเป็นภาพหนึ่งภาพต่อระดับ

การตั้งชื่อ **โฟลเดอร์ลูกของระดับ** โดยตรง (เช่น `output/raw/`) ก็ทำงานได้เช่นกัน การทำเช่นนี้จะทิ้งโฟลเดอร์ราก
`.daq` ไว้ ดังนั้นให้คัดลอกหรือชี้ให้ DAQ อ่านข้อมูลนั้นเข้ามาพร้อมกัน เมื่อคุณสร้างผลิตภัณฑ์รังสีวัด
ใหม่จาก `raw/` — มิฉะนั้น การจับคู่เวลาจะไม่มีข้อมูลอ้างอิงเพื่อแก้ไข

**การประมวลผลจะเริ่มจาก `raw` เสมอ** ในแต่ละการจับภาพ เฟรมดิบคือแหล่งข้อมูลของ pipeline;
`debayered`, `radiance`, `reflectance` และ `preview` เป็นโหมดที่แสดงผลได้ แต่ไม่เคยถูกส่ง
กลับผ่านท่อส่งข้อมูล. การประมวลผลใหม่ผลิตภัณฑ์ที่ได้มาจะนำไปใช้เอฟเฟกต์ vignette, CCM และ
การคำนวณ radiance ที่ได้ถูกเบคไว้แล้วในพิกเซลของมัน ดังนั้นChloros จึงปฏิเสธการประมวลผลแทนที่จะ
ทำการประมวลผลซ้ำ สองผลที่ควรทราบ:

- `index/` และ `composite/`**ไม่เคย** ถูกประมวลผลเลย พวกมันเป็นผลลัพธ์ ไม่ใช่การจับภาพ —
  การเรนเดอร์ LUT แบบNDVIไม่มีการตีความ radiance ที่มีความหมาย
- โฟลเดอร์การจับภาพที่ส่งออก **โดยไม่มี** `raw` (เช่น `array-capture --processing reflectance`) ไม่มี
  ไม่มีแหล่งข้อมูลใน pipeline ที่ถูกต้อง การจับภาพเหล่านั้นสามารถนำเข้าและแสดงผลได้ตามปกติ แต่ `process` จะข้าม
  ภาพเหล่านั้นไปและแจ้งว่า:

  ```
  [IMPORT-LEVEL] Skipping 4 already-processed file(s) with no raw source: capture_…_reflectance.tif
  [IMPORT-LEVEL] Processing starts from raw. Re-capture with --processing raw, or force an entry
                 point with --input-level.
  ```

  หากคุณจำเป็นต้องส่งผลิตภัณฑ์ที่สร้างขึ้นผ่านระบบจริง — เช่น เซสชัน hub ที่ถูกจับภาพด้วย
  `demosaic` หรือโฟลเดอร์รุ่นเก่า — `--input-level {raw,debayered,processed}` จะบังคับจุดเข้า
  และแทนที่คำสั่งข้ามไปนั้น ธงนี้คือทางออกฉุกเฉินที่ตั้งใจไว้; `auto` (ค่าเริ่มต้น)
  จะไม่ประมวลผลการจับภาพที่ไม่มีข้อมูลดิบ

### การจับภาพที่ถูกข้ามในอาร์เรย์ที่มีฟิลเตอร์ผสม

เมื่อคุณผสมกล้องแบบRGBและกล้องมัลติสเปกตรัมในอาร์เรย์เดียว `array-capture --processing radiance` (หรือ `reflectance`) จะบันทึกเฟรมมัลติสเปกตรัมและ **ข้าม** กล้องแบบRGB— ค่า radiance ต่อ Bayer ไม่มีความหมายสำหรับเซ็นเซอร์แบบบรอดแบนด์ CLI จะพิมพ์แต่ละไฟล์ที่บันทึกไว้ (พร้อมระดับการส่งออก), แต่ละ `.daq` ที่เขียนลง, และแต่ละการข้ามอย่างชัดเจน ดังนั้นจำนวนไฟล์จึงไม่น่าประหลาดใจ:

```
  Saved: output/sync_…_SN213800234.tif [reflectance] (SN:213800234, fid:1)
  Saved: output/sync_…_SN214000533.tif [reflectance] (SN:214000533, fid:1)
  Saved: output/sync_…_SN214701288.tif [reflectance] (SN:214701288, fid:1)
  DAQ:   output/sync_…_daq-e-54b5e0.daq
  Skipped: SN:214701292 (reflectance-not-applicable-to-rgb-cam filter=RGB)

  3 synchronized frames captured. (1 skipped)
```

โทเค็นเหตุผลการข้าม (Skip-reason tokens) มีรูปแบบ `<level>-not-applicable-to-rgb-cam` การสะท้อนแสง (Reflectance) สามารถข้ามได้ด้วย `reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)` และด้วย `dls-uncalibrated-band-<nm>` เมื่อแถบความยาวคลื่นอยู่ส่วนใหญ่ภายนอกช่วงที่ปรับเทียบทางรังสีของเซ็นเซอร์แสง DAQ (~374–974 nm) — ในบรรดา SKU ที่จัดส่ง มีเพียง F988 เท่านั้น ซึ่งเส้นทางที่รองรับคือกระบวนการทำงานของแผงสะท้อนแสง

ใช้ `--processing debayered` (หรือ `display`) เพื่อรวมกล้องทุกตัวโดยไม่คำนึงถึงประเภทฟิลเตอร์ หรือใช้ `all` ตามค่าเริ่มต้น เพื่อรับระดับที่เหมาะสมสำหรับกล้องทุกตัวในครั้งเดียว

---

## โหมดการจับภาพ, เครื่องบันทึก และกระบวนการประมวลผลแบบออฟไลน์

ทั้งหมดนี้ทำงานบน **อาร์เรย์ที่คงที่** (รัน `array-connect` ก่อน) และสะท้อนการทำงานของแผงจับภาพใน GUI

### โหมด `array-capture`

`array-capture` เป็นคำสั่งเดียวที่มีสี่โหมดชัตเตอร์ พร้อมชุดตัวเลือกการส่งออก:

| โหมด | ตัวเลือก | พฤติกรรม |
| --- | --- | --- |
| **Single** *(ค่าเริ่มต้น)* | (ไม่มี) | กลุ่มการจับภาพที่ซิงค์หนึ่งกลุ่ม, แล้วออก |
| **ต่อเนื่อง** | `--continuous` | ดำเนินการต่อเนื่องจนกว่าจะถึง `Ctrl+C`, `--count N` หรือ `--duration S` |
| **ตามช่วงเวลา** | `--interval S` | หนึ่งรอบทุก `S` วินาที (วัดจากจุดเริ่มต้นของแต่ละรอบ), ขอบเขตเดียวกัน |
| **เร็วที่สุด** | `--fastest` | เฉพาะข้อมูลดิบ + ค่าอ่าน DAQ ที่กำหนด + ดัชนีรวมแบบผสม; ข้ามการคำนวณความส่องสว่าง/การสะท้อน/การแสดงผล เพื่อให้เฟรมถูกประมวลผลอย่างรวดเร็ว ซึ่งหมายถึง `--processing raw --force-daq`. ประมวลผลใหม่ `.daq` ที่บันทึกไว้เป็นผลิตภัณฑ์ที่ปรับเทียบในภายหลัง |

ตัวเลือกการส่งออก (สามารถใช้ร่วมกับโหมดใดก็ได้; ทั้งหมดใช้จุดปลายทาง GUI/SDK ร่วมกัน):

| ตัวเลือก | ผล |
| --- | --- |
| `--processing LEVEL` | ระดับการส่งออกเดียว หรือ `all` (ค่าเริ่มต้น) |
| `--levels L1,L2,…` | ชุดย่อยที่ระบุชัดเจนของประเภทการส่งออก (เช่น `raw,radiance,reflectance`); **แทนที่ `--processing`**. |
| `--aligned` / `--no-aligned` | ปรับการส่งออกที่ไม่ใช่แบบดิบของสมาชิกทุกตัวให้สอดคล้องกับ [โปรไฟล์การจัดแนว](#alignment) ของอาร์เรย์ (ที่ลงทะเบียนร่วมกัน). ข้อมูลดิบจะไม่ถูกปรับให้สอดคล้อง แต่จะเก็บการแปลงไว้ในเมตาดาต้า หากอาร์เรย์ไม่มีโปรไฟล์ จะกลับสู่สถานะไม่สอดคล้อง (พร้อมคำเตือน) |
| `--index` / `--no-index` | บันทึก / ข้ามการซ้อนทับดัชนีพืชพรรณต่อกล้อง หากมีการกำหนดค่าไว้ ค่าเริ่มต้น: แสดงผล |
| `--force-daq` | บันทึกค่าอ่าน DAQ/DLS ที่กำหนดไว้เป็นไฟล์ sidecar `.daq` แม้ระดับที่เลือกไว้ไม่ต้องการ (เช่น การจับภาพแบบ raw เท่านั้น), เพื่อให้สามารถประมวลผลเฟรมใหม่เป็นค่าสะท้อนแสง/ดัชนีแบบออฟไลน์ได้ |
| `--smart` | รอให้ AE เสถียรในทุกกล้องก่อนที่จะทริกเกอร์ (ดู [Smart-AE / Smart-Capture](#smart-ae--smart-capture)). |
| `--compression {deflate,none}` | TIFF การบีบอัดพิกเซล. `deflate` (ค่าเริ่มต้น) = zlib L1 แบบไม่สูญเสียข้อมูล + ตัวทำนายแนวนอน, ~4.1 MB ต่อเฟรมความละเอียดเต็ม; `none` = ไม่บีบอัด, เขียนเร็วขึ้น ~5× ที่ ~6.3 MB ต่อเฟรม — ใช้เพื่ออัตราการส่งข้อมูลต่อเนื่องสูงสุดเมื่อพื้นที่ดิสก์เอื้ออำนวย ทั้งสองแบบไม่สูญเสียข้อมูลและอ่านได้เหมือนกันเมื่อนำเข้า |

> **TIFF แบบเขียนครั้งเดียว + โมเดลอัตราความเร็วคงที่**ภาพที่ถ่ายจะถูกเขียนใน**หนึ่ง**ครั้งผ่านไฟล์ tifffile ที่รวมพิกเซล + XMP + IFD0 Make/Model (วัดบน Mono12 ความละเอียดเต็ม: 36 ms แบบบีบอัด / 6.5 ms ไม่บีบอัด, เทียบกับ ~148 ms สำหรับวิธีเก่าที่เขียนก่อนแล้วใช้ ExifTool เขียนใหม่); งาน ExifTool ที่เหลือเพียงอย่างเดียว (การปรับแต่ง EXIF sub-IFD) ทำงานบนกระบวนการทำงานพื้นหลังแบบไม่พร้อมกัน (async background worker) และเฟรมจะเสร็จสมบูรณ์และพร้อมสำหรับการนำเข้า แม้กระบวนการนี้จะไม่ถูกเรียกใช้เลยก็ตาม โปรดทราบว่าการบีบอัด DEFLATE จะยึด GIL ของPython ดังนั้นการเขียนข้อมูลที่ถูกบีบอัด**จึงไม่**สามารถทำงานแบบขนานได้ระหว่างเธรดผู้เขียนของแต่ละกล้อง — การบันทึกภาพความละเอียดเต็มจาก 8 กล้องอย่างต่อเนื่องที่อัตราความเร็วของเซ็นเซอร์ (~10.4 fps) จำเป็นต้องใช้ `--compression none`**และ** ดิสก์ระดับ NVMe (~500 MB/s ของการเขียนอย่างต่อเนื่อง) ตัวปรับเดียวกันนี้ถูกเปิดเผยเป็น `compression` บน `POST /api/camera/array/capture`

```bash
# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 \
  --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# Co-registered multi-band export (drop the index overlay)
chloros-cli lattice array-capture --processing reflectance --aligned --no-index -o out/
```

### `array-record` — วิดีโอ/GIF แบบรวม-วิดีโอ/GIF แบบดัชนีรวม (ระดับการเฝ้าติดตาม)

บันทึกทุกสิ่งที่ **มุมมองดัชนีรวมแบบสด** แสดงไว้ไปยัง `.avi` (และ `.gif` ตามตัวเลือก) เนื่องจากมันดึงข้อมูลจากภาพรวมแบบสด สตรีมแบบรวมนี้ต้องเปิดอยู่ (เช่น มัลติเพล็กซ์กำลังถูกแสดงตัวอย่างใน GUI) เพื่อให้เฟรมถูกบันทึกได้ มันจะตรวจสอบความคืบหน้าทุก 2 วินาที และหยุดลงที่ `--duration`, `Ctrl+C` หรือเมื่อเครื่องบันทึกสิ้นสุดการทำงานด้วยตนเอง

```bash
# 30-second combined-index clip at 10 fps, plus a GIF
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/
```

| สัญลักษณ์ | ค่าเริ่มต้น | คำอธิบาย |
| --- | --- | --- |
| `--array-id ID` | only array | มصفوفเป้าหมาย (ข้ามไปหากมีเพียงหนึ่งชุดที่เชื่อมต่อ) |
| `-o, --output DIR` | `output` | โฟลเดอร์ออก (backend-local). |
| `--fps F` | `10` | อัตราเฟรมการบันทึก |
| `--duration S` | จนกว่าจะกด Ctrl+C | หยุดอัตโนมัติหลังจาก `S` วินาที |
| `--gif` | ปิด | เขียนไฟล์ GIF แบบเคลื่อนไหวด้วย |
| `--gif-only` | ปิด | เขียนเฉพาะ GIF (ไม่มี `.avi`). |

### `array-burst` — การถ่ายภาพต่อเนื่องแบบ raw-Bayer ที่อัตราเฟรมสูง (ระดับการวิเคราะห์)

อ่านบัฟเฟอร์กลุ่มที่ซิงค์ของวงจรการจับภาพโดยตรง — **ไม่จำเป็นต้องใช้โซ่การปรับเทียบ, exiftool หรือโหมดดูสด** — ดังนั้นจึงทำงานด้วยความเร็วการจับภาพสูงสุดของกล้อง เขียนเฟรม raw + manifest สำหรับแต่ละเฟรม + `.daq` หนึ่งไฟล์ต่อการอ่าน DLS ที่แตกต่างกันภายใต้ `<output>/bursts/<base>/` สามารถประมวลผลใหม่แบบออฟไลน์ (คำสั่งถัดไป) หรือส่ง `--build` เพื่อดำเนินการทันทีเมื่อหยุด

```bash
# 5-second raw burst, then build the combined index video in one shot
chloros-cli lattice array-burst --duration 5 --build \
  --products combined:index --fps 10 -o capture/
```

| แฟล็ก | ค่าเริ่มต้น | คำอธิบาย |
| --- | --- | --- |
| `--array-id ID` | only array | มصفوفเป้าหมาย |
| `-o, --output DIR` | `output` | โฟลเดอร์ผลลัพธ์ (ข้อมูลที่ส่งมาแบบบัฟต์จะถูกจัดเก็บใน `<DIR>/bursts/<base>/`). |
| `--duration S` | จนกว่าจะกด Ctrl+C | อัตโนมัติ-หยุดอัตโนมัติหลังจาก `S` วินาที |
| `--max-frames N` | ไม่จำกัด | หยุดอัตโนมัติหลังจาก `N` เฟรมดิบ |
| `--build` | ปิด | หลังจากหยุดแล้ว ให้ประมวลผลชุดข้อมูลแบบบัร์สต์ใหม่ทันที (เหมือน `array-build-video`). |
| `--products …` | `combined:index` | ด้วย `--build`: วิดีโอใดที่จะสร้าง (ดูด้านล่าง). |
| `--fps F` | `10` | เมื่อใช้ `--build`: fps ของวิดีโอที่ส่งออก |
| `--save-tiffs` | ปิด | เมื่อใช้ `--build`: ยังบันทึกไฟล์ TIFF ที่ปรับเทียบตามแต่ละเฟรมด้วย |
| `--gif` | ปิด | เมื่อใช้ `--build`: จะเขียนไฟล์ GIF แบบเคลื่อนไหวด้วย |

### `array-build-video` — ประมวลผลใหม่แบบออฟไลน์สำหรับชุดภาพที่บันทึกไว้

Time- จับคู่แต่ละเฟรมดิบกับค่าการอ่าน `.daq` ที่บันทึกไว้ซึ่งใกล้เคียงที่สุด และส่งผ่าน **ห่วงโซ่ radiance / reflectance / index เดียวกันกับ pipeline การนำเข้า**, เพื่อสร้างวิดีโอหนึ่งหรือมากกว่าหนึ่งคลิป

`--products` เป็นรายการที่คั่นด้วยเครื่องหมายจุลภาคของ `kind:level` รายการ ซึ่ง `kind` ∈ `per_cam` | `combined` และ `level` ∈ `radiance` | `reflectance` | `index`. `level` แบบเปล่า (ไม่มี `kind:`) จะตั้งค่าเริ่มต้นเป็น `per_cam` ค่าเริ่มต้นคือ `combined:index`.

```bash
# Per-cam reflectance video for every member + one combined NDVI video
chloros-cli lattice array-build-video \
  --burst-dir "capture/bursts/2026-06-24_141500" \
  --products per_cam:reflectance,combined:index \
  --fps 10 --save-tiffs
```

| ตัวเลือก | ค่าเริ่มต้น | คำอธิบาย |
| --- | --- | --- |
| `--burst-dir DIR` | (จำเป็น) | เส้นทางไปยังโฟลเดอร์ burst (`…/bursts/<base>/`) |
| `--products …` | `combined:index` | รายการ `kind:level` ตามที่กล่าวไว้ข้างต้น |
| `--fps F` | `10` | จำนวนเฟรมต่อวินาที (fps) ของวิดีโอที่ส่งออก |
| `--save-tiffs` | ปิด | บันทึกไฟล์ TIFF ที่ปรับเทียบแล้วสำหรับแต่ละเฟรมพร้อมกับวิดีโอ |
| `--gif` | ปิด | บันทึกไฟล์ GIF แบบเคลื่อนไหว |

> **เลือกเครื่องบันทึกที่เหมาะสม** `array-record` เป็น *ระดับการเฝ้าติดตาม* — มันบันทึกภาพคอมโพสิตสดตามที่แสดงบนหน้าจอ และจำเป็นต้องเปิดสตรีมไว้ `array-burst` → `array-build-video` เป็น *ระดับการวิเคราะห์* — มันบันทึกข้อมูลเซ็นเซอร์ดิบด้วยความเร็วเต็ม และสร้างวิดีโอที่ปรับเทียบแล้วสำหรับ radiance/reflectance/index ในภายหลัง โดยไม่จำเป็นต้องมีภาพสด

### Mono (M3M) กล้องแบบแถบเดียว

ซีรีส์ **M3M**เป็นรุ่นโมโนโครมของ**M3C**แบบ Bayer: มีตัวกรองแทรกสอดแถบแคบหนึ่งตัวต่อกล้อง (เช่น `M3M-<lens>-F<wavelength>`, `M3M-L87-F685`), ทำให้เซ็นเซอร์ส่ง**แถบสีเทาเดียว** โดยไม่มีโมเสก Bayer ไม่มีขั้นตอนการเดโมเสก ไม่มีสัญญาณรบกวนระหว่างช่องสัญญาณที่ต้องแยก และไม่จำเป็นต้องตั้งค่าสมดุลสีขาว — กระบวนการแสดงสีแบบRGBทั้งหมดจึงไม่ถูกนำมาใช้

ความหมายของสิ่งนี้บน CLI:

- **`lattice white-balance`, `lattice color-profile`, `lattice color`**จะตรวจพบกล้องโมโนโครมและ**ข้ามไปพร้อมข้อความหนึ่งบรรทัด** แทนที่จะส่งการตั้งค่าที่ไม่มีความหมายไป ระบบยังคงทำงานปกติเมื่อใช้กับกล้อง RGB /Bayer M3C ในเซสชันเดียวกัน
- **`lattice calibrate` / `process --reflectance` / `array-capture --processing radiance`** ยังคงทำงานได้ — ความส่องสว่างและความสะท้อนเป็นแผนที่รังสี *ต่อแถบ* และถูกกำหนดไว้อย่างชัดเจนสำหรับแถบเดียว เฟรมโมโนมีเมทริกซ์การตอบสนองของเซ็นเซอร์ **แบบเอกลักษณ์** (ไม่มีการแยกผสม 3×3) ดังนั้นระนาบจึงผ่านกระบวนการคัลลิเบรชันโดยไม่ถูกเปลี่ยนแปลง
- **กล้องโมโนตัวเดียวไม่สามารถสร้างดัชนีพืชได้.**NDVI / NDRE /etc. ต้องการอย่างน้อยสองแถบ (เช่น Red + NIR) เพื่อสร้างดัชนีจากอุปกรณ์โมโน ให้ชี้**หลาย** กล้อง M3M ไปยังความยาวคลื่นที่ต่างกัน จัดเรียงพวกมันเป็นสแต็กหลายแถบ และสร้างดัชนี *ที่*:

```bash
# Red (660) + NIR (850) mono pair -> aligned 2-band stack -> NDVI
chloros-cli lattice array-connect --serials SN_RED,SN_NIR
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Red_660 --channel nir=NIR_850 \
  --save-multiband -o output/
```

`--channel` สัญลักษณ์ต้องตรงกับชื่อช่องของ preset **อย่างแม่นยำ** (แยกตัวอักษรใหญ่-เล็ก; NDVI เป็นตัวพิมพ์เล็ก `red`,`nir` — ดู `--list-presets`), และชื่อด้านแถบจะกำหนดชื่อแถบในชุดภาพที่จัดเรียงแล้ว (โหมดออฟไลน์ยังรับได้ทั้ง 0-based band indices, e.g. `--channel red=0 --channel nir=1`).

ตัวระบุ (discriminator) ในทั้งสแต็กคือโทเคน `M3M` ในสตริงแบบจำลอง (มันไม่ปรากฏในสตริง `M3C`), ซึ่งแสดงบน GUI/SDK เป็น `is_mono`

---

## การตั้งค่าและปรับแต่ง NIC ของโฮสต์ (อาร์เรย์ LATTICE)

กล้อง LATTICE ส่งสัญญาณ GVSP ผ่านอะแดปเตอร์ Ethernet ของโฮสต์ ดังนั้น สำหรับอาร์เรย์ที่มีหลายกล้อง **ไดรเวอร์**และ**ขนาดวงรับสัญญาณ** ของอะแดปเตอร์มีความสำคัญไม่แพ้ความเร็วการเชื่อมต่อ การตั้งค่าที่ผิดจะแสดงผลเป็น `FRAMES WILL DROP` / `Reduce ROI to enable` ในแผง Array Settings (และใน `lattice network-analysis` / `analyze_array_network()` ของ SDK) แม้กล้องเองจะทำงานปกติ

### อะแดปเตอร์ USB 10GbE — Realtek RTL8157 (&quot;Realtek USB 10GbE Family Controller&quot;)

| รายการ | ค่าที่จำเป็น | เหตุผลที่มันสำคัญ |
| --- | --- | --- |
| **เวอร์ชันไดรเวอร์**|**≥ v10.67 (ม.ค. 2026)**, INF `rtump64x64sta.inf` | ไดรเวอร์รุ่นเก่า**2016**(v10.65, `rtump64x64.inf`) จัดการการปิดเครื่องอย่างไม่ถูกต้องและเกิดข้อผิดพลาด (bugcheck) กับ**`DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`)**เมื่อปิดเครื่อง/รีสตาร์ท/เข้าสู่โหมดสลีป กระบวนการเปลี่ยนสถานะจะค้าง (~5 นาที หมดเวลา) ผู้ใช้ต้องปิดเครื่องแบบบังคับ และการปิดเครื่องที่ไม่สมบูรณ์ซ้ำๆ**ทำให้ WMI repository เสียหาย**(PowerShell/เครื่องมือเริ่มทำงานผิดพลาดด้วย `Invalid class`) และ**ทำให้สแต็ก USB ติดขัด** ในการบูตครั้งต่อไป (NIC ไม่สามารถเปิดใช้งานได้; ไดรฟ์ USB หยุดการระบุอุปกรณ์) อัปเดตจาก realtek.com (หรือผู้ผลิตดองเกิล) ก่อนที่จะพึ่งพาการรีสตาร์ทอย่างถูกต้อง |
| **บัฟเฟอร์รับ**— คำสำคัญ `ReceiveBufferLen` |**256**(ค่าสูงสุดของไดรเวอร์) | วงแหวนรับสัญญาณ (RX ring) ของ NIC ค่าเริ่มต้นของไดรเวอร์ที่**32**ทิ้งพื้นที่วงแหวนที่ใช้งานได้เพียง ~0.26 MB — ซึ่งเล็กเกินไปสำหรับการส่งข้อมูลแบบบัฟเฟอร์จากกล้องหลายตัว — ทำให้แผงควบคุมอาร์เรย์รายงานข้อผิดพลาด `Sim-emit burst … exceeds NIC RX ring usable capacity 0.26 MB` และบล็อกการเชื่อมต่อ เมื่อตั้งค่าเป็น**256**วงแหวนมีขนาดใหญ่ (**~13.5 MB วัดได้บนโฮสต์ 10GbE ในห้องทดลอง**) ซึ่งให้พื้นที่ว่างจริงในท่อส่งข้อมูลรับ (RX pipeline) สำหรับการส่งข้อมูลแบบบัฟต์ GVSP จากหลายกล้อง (การกำหนดค่าใด ๆ จะ *เชื่อมต่อ* ได้จริงหรือไม่ ถูกตัดสินโดยการตรวจสอบสองอย่าง — การตรวจสอบการรับเข้า **drain-aware**และการตรวจสอบ**aggregate over-subscription** — ไม่ใช่การเปรียบเทียบแบบดิบระหว่าง burst กับ ring; ดู [โมเดล fps &amp; burst ของ Array](#array-fps--burst-model).) |
| **Receive URBs**— คำสำคัญ `PendingReceives` |**64** (สูงสุด) | บล็อกคำขอ USB ที่กำลังส่ง; เพิ่มขึ้นพร้อมกับ Receive Buffers เพื่อดูดซับการส่งข้อมูลแบบ burst |
| **Jumbo Frame** — คำสำคัญ `*JumboPacket` | **9014** | จำเป็นสำหรับแพ็กเก็ต GVSP ขนาด 9000 ไบต์ (จำนวนแพ็กเก็ตต่อเฟรมน้อยกว่า 6 เท่าเมื่อเทียบกับ 1500) |

> ⚠️ **การอัปเดตไดรเวอร์ NIC จะรีเซ็ตคุณสมบัติขั้นสูงเหล่านี้กลับเป็นค่าเริ่มต้น**หลังจากอัปเดตหรือเปลี่ยนไดรเวอร์อะแดปเตอร์ ให้**ตั้งค่าใหม่** `ReceiveBufferLen=256` และ `PendingReceives=64` มิฉะนั้น แผงอาร์เรย์จะปิดกั้นการเชื่อมต่ออีกครั้ง แม้ว่าจะ &quot;ไม่มีการเปลี่ยนแปลงใดๆ ในฮาร์ดแวร์.&quot; นี่คือสาเหตุอันดับ 1 ที่ทำให้ระบบซึ่งทำงานได้ปกติมาก่อน จู่ๆ ก็ไม่สามารถเชื่อมต่อได้

ใช้คำสั่งจาก PowerShell ที่ **มีสิทธิ์ผู้ดูแลระบบ** (แทนที่ด้วยชื่ออะแดปเตอร์ของคุณ เช่น `"Ethernet 5"`):

```powershell
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen -RegistryValue 256
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword PendingReceives  -RegistryValue 64
Get-NetAdapterAdvancedProperty  -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen,PendingReceives   # verify
```

> **`lattice network --fix` ใช้สำหรับอะแดปเตอร์ USB 10GbE** ตอนนี้มันจะตรวจจับประเภทอะแดปเตอร์และปรับค่า: `*ReceiveBuffers`→2048 สำหรับ NIC PCIe (Intel I219, เป็นต้น), หรือ `ReceiveBufferLen`→256 + `PendingReceives`→64 สำหรับ ตัวควบคุม Realtek **USB** 10GbE (ซึ่งไม่เปิดเผยค่า `*ReceiveBuffers`) ค่าเป้าหมายจะถูกจำกัดไว้ที่ค่าสูงสุดที่แต่ละไดรเวอร์รายงาน (`NumericParameterMaxValue`) ที่รายงานโดยไดรเวอร์แต่ละตัว ดังนั้นจึงไม่เขียนค่าที่อยู่นอกช่วงค่าที่กำหนดไว้ ให้รันจากเทอร์มินัล **ที่มีสิทธิ์สูง**; เช่นเดียวกับการปรับแต่งที่อิงจากรีจิสทรี การเปลี่ยนแปลงจะมีผลหลังจากรีสตาร์ทอะแดปเตอร์หรือรีบูตเครื่อง คำสั่ง `Set-NetAdapterAdvancedProperty` แบบแมนนวลข้างต้นยังคงเป็น เป็นทางเลือกที่ดี — คำสั่งเหล่านี้มีผลทันที (ผูกใหม่กับอะแดปเตอร์) โดยไม่ต้องรีสตาร์ท

### พื้นฐานเครือข่าย (ลิงก์ LATTICE ทั้งหมด)

- **การกำหนดที่อยู่:** link-local `169.254.0.0/16` (GigE Vision LLA). เครื่องโฮสต์ใช้ที่อยู่แบบคงที่ `169.254.x.x/16`; กล้องและ DAQ-E จะกำหนดที่อยู่เองในช่วงเดียวกัน ไม่จำเป็นต้องใช้ DHCP/เกตเวย์
- **ขนาดแพ็กเก็ต:**แนะนำให้ใช้จัมโบ้ (9000) แต่ให้ระบบตรวจจับอัตโนมัติค้นหาขนาดที่เหมาะสม — ระบบจะวัดใหม่ทุกครั้งที่เชื่อมต่อ และได้ตรวจสอบผ่านขีดจำกัด ICMP 1500 ไบต์ของกล้องแล้วผ่าน GVSP probe ดังนั้นจึงใช้ jumbo เมื่อสายสามารถรองรับได้จริง ใช้ `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` เฉพาะเมื่อคุณรู้ดีกว่าการตรวจวัด และ ควรตั้งค่าตามคำสั่งแต่ละครั้งมากกว่าการตั้งค่าถาวร: การตั้งค่าแบบ pin จะข้ามขั้นตอนการตรวจวัด ดังนั้นหากเส้นทางไม่สามารถรองรับ 9000 ได้จริง**ทุก** ครั้งที่จับข้อมูลจะหมดเวลาด้วย `SC_ERR_TIMEOUT -1011` (ดู [Environment Variables](#environment-variables)).
- **วงแหวน RX ขยายตามค่า `ReceiveBufferLen`:**ที่ค่าเริ่มต้น `32` วงแหวนที่ใช้งานได้มีขนาด ~0.26 MB (เล็กเกินไปสำหรับ burst แบบหลายกล้อง); ที่ค่าสูงสุด `256` มันมีขนาดใหญ่ (~13.5 MB วัดบนโฮสต์ 10GbE ในห้องทดลอง), ซึ่งให้พื้นที่ว่างที่เพียงพอ การที่การตั้งค่าจะเชื่อมต่อได้หรือไม่นั้น ขึ้นอยู่กับ**การตรวจสอบการรับเข้าที่คำนึงถึงการระบาย** **และ** **การตรวจสอบการสมัครเกินรวม** ด้านล่าง — ไม่ใช่การเปรียบเทียบแบบดิบระหว่าง burst กับ ring

### Array fps &amp; แบบ burst

วิธีอ่านแผงตั้งค่า Array (และ `lattice analyze-array` / `analyze_array_network` ของ SDK):

- **Burst จะถูกรวมกันต่อกล้องแต่ละตัวตามรูปแบบพิกเซลจริงของกล้องนั้น**Mono**M3M**ส่ง**Mono12 (2 B/px)**;**M3C**Bayer ส่ง 8- หรือ 12-bit (TRI032S ส่ง BayerRG12 โดยอัตโนมัติ แม้จะขอ BayerRG8). ดังนั้น เฟรมความละเอียดเต็มจากกล้อง 4 ตัวจะมีขนาด**~12.6 MB หากทั้งหมดเป็น 8 บิต แต่ ~25 MB หากมีกล้อง Mono 12 บิต 3 ตัว**. ระบบการคาดการณ์จะกำหนดรูปแบบของแต่ละกล้องจากรุ่นของมัน (แคชข้อมูลระบุตัวตน) ดังนั้นข้อมูลที่ส่งมาแบบบัร์สต์จะตรงกับข้อมูลที่สายส่งจริง — ไม่ใช่การสมมติแบบ BayerRG8 ขนาดเดียว
- **อะแดปเตอร์อีเทอร์เน็ต USB ถูกจำกัดความเร็วสูงสุดที่ 200 MB/s ไม่ว่าข้อมูลบนป้ายจะระบุไว้อย่างไร** ตารางประสิทธิภาพที่แปลงอัตราการเชื่อมต่อเป็นค่าความเร็วคงที่นั้นอ้างอิงจาก PCIe; การ์ดเครือข่าย USB ประกาศอัตราความเร็ว *Ethernet* ของมัน แต่ถูกจำกัดโดยบัส USB และไดรเวอร์ของมัน ดองเกิล USB 10GbE เคยให้ผลลัพธ์ ~1063 MB/s &quot;ต่อเนื่อง&quot; — ตัวเลขที่ไม่เคยถูกตรวจสอบ — และระบบควบคุมความเร็วที่เกิดขึ้นทำให้เฟรมเสียหาย 6–18 % ของเฟรม ในขณะที่ยังคงรายงานค่า fps เป้าหมายที่ปกติ การ์ดเครือข่ายที่ต่อผ่าน USB ปัจจุบันถูกจำกัดไว้ที่ **200 MB/s** อย่างเด็ดขาด (ข้อจำกัดนี้มาจากบัส ดังนั้นจึงไม่ปรับตามข้อมูลบนป้ายชื่อ; การ์ด USB 1 GbE ให้ความเร็ว ~80 MB/และไม่ได้รับผลกระทบ). `wire_ceiling_source` ในบันทึกความสามารถระบุไว้อย่างชัดเจน และ `nic_is_usb` ทำเครื่องหมายไว้ สามารถแทนที่ทั้งสองค่าด้วย `--wire-ceiling-mbps`.
- **ค่าแอดมิแทนซ์คำนึงถึงการระบายข้อมูล ไม่ใช่การเปรียบเทียบระหว่าง burst ทั้งหมดกับ ring** Burst ที่เกิดขึ้นพร้อมกันต้องพอดีกับ *transient backlog* = `max(0, Σ per-cam arrival − host drain) × emit_window` เท่านั้น ไม่ใช่ burst ทั้งหมด ในโครงสร้าง host เร็ว / cam ช้า (a **PCIe**10G host + 4× 1 GbE cams: arrival ≈ 320 MB/s, drain ≈ 1063 MB/s) host ระบายข้อมูลได้เร็วกว่าที่กล้องจะส่งข้อมูลเข้ามา, backlog ≈ 0 ดังนั้นการส่งสัญญาณแบบจำลองความละเอียดเต็ม**จะยอมรับ**แม้ว่าการส่งข้อมูลแบบ burst 25 MB จะเกินวงแหวน 13.5 MB หากวางกล้องทั้งสี่ตัวเดียวกันไว้หลังอะแดปเตอร์**USB**10GbE ความเร็วการระบายข้อมูลจะเป็น 200 MB/s ไม่ใช่ 1063 — การรับข้อมูลเข้าเร็วกว่าการส่งข้อมูลออก และการสูญเสียจะปรากฏเป็นเฟรมที่เสียหาย แทนที่จะเป็นอัตราเฟรมที่ต่ำลง บนโฮสต์ 1 GbE ค่า DLThr ขั้นต่ำ 31.25 MB/s ของกล้องทำให้การรับข้อมูลเข้าเร็วกว่าการส่งข้อมูลออก → ระบบจะ**** (สำหรับ *ประเภท* บล็อกนี้ ให้ลด ROI หรือใช้ binning ≥ 2) การรับข้อมูลเป็นหนึ่งใน **สอง** — อีกตัวคือการตรวจสอบการสมัครเกินระดับรวมด้านล่าง
- **fps ที่คาดการณ์เป็นขีดจำกัดสูงสุดแบบอนุรักษ์นิยมสำหรับการดึงข้อมูลแบบอนุกรม**วงจรดึงข้อมูลของโฮสต์ปัจจุบันดึงบัฟเฟอร์ของแต่ละกล้อง**แบบอนุกรม**(~หนึ่งหน้าต่างการส่งข้อมูลต่อกล้องแต่ละตัว), ดังนั้นรอบการทำงานจึงถูกจำกัดโดย `max(readout+emit, N × emit)` โดยความเร็วการส่งข้อมูลต่อกล้องถูกจำกัดตาม**ลิงก์การเข้าถึง**ของกล้อง (1 GbE ≈ 80 MB/s) ไม่ใช่ความเร็วอัพลิงค์ของโฮสต์ สำหรับอาร์เรย์ 4 กล้อง ความละเอียดเต็ม 12 บิต นั่นคือ**~2.8 fps**ซึ่งสอดคล้องกับค่าที่วัดได้ ~2.7–3.0 fps ที่ถูกออกแบบให้**ไม่ขึ้นอยู่กับระยะเวลาการเปิดรับแสง**, ดังนั้นในฉากที่มีแสงน้อย ค่าจริงอาจลดลงเล็กน้อยต่ำกว่าขีดจำกัดสูงสุดเมื่อเวลาการเปิดรับแสงเพิ่มขึ้น การดึงข้อมูลแบบอนุกรมคือตัวจำกัด fps ที่แท้จริง; การทำให้เป็นแบบขนานจะเพิ่มขีดจำกัดสูงสุดให้เข้าใกล้กับอัตราการส่งข้อมูลต่อกล้องเดียว
- **การสมัครใช้เกินรวมเป็นอุปสรรคที่ขัดขวางการเชื่อมต่ออย่างเด็ดขาด**การจัดสรรแบนด์วิดท์ต่อกล้องมีขีดจำกัดขั้นต่ำที่**8 MB/s**(`ARRAY_PER_CAM_FLOOR_BPS`), ดังนั้นเมื่อระดับขั้นต่ำถูกจำกัดแล้ว ความต้องการรวม (`per_cam × N`) อาจเกิน**ขีดจำกัดสูงสุดของสายที่ปลอดภัยจากการชนกัน**(`sustained × sim_emit_factor`) ได้แล้ว ขีดจำกัดความละเอียดเต็มในทางปฏิบัติบน 1 GbE:**6 กล้องที่ MTU 1500, 9 กล้องเมื่อใช้ jumbo**ขีดจำกัดนี้เป็นคุณสมบัติของสายสัญญาณและระดับขั้นต่ำเท่านั้น — มัน**ไม่ขึ้นอยู่กับขนาดเฟรม**, ดังนั้น**การรวมเฟรม (binning) และ ROI ที่เล็กลงจึงไม่ช่วย** (เพราะมันลดจำนวนไบต์ต่อ *เฟรม* ไม่ใช่จำนวนไบต์ต่อ *วินาที* ที่ถูกกำหนดโดย GevSCPD); วิธีแก้ไขเพียงอย่างเดียวคือลดจำนวนกล้อง, ใช้เฟรมจัมโบ้แบบ end-to-end, หรือใช้ NIC ที่เร็วขึ้น อาการที่ปรากฏคือการสูญเสียแพ็กเก็ต GVSP, ไม่ใช่การลด fps อย่างค่อยเป็นค่อยไป ดังนั้น `analyze-array` จะตั้งค่าตัวเลข fps ที่สามารถบรรลุได้เป็นศูนย์ และแสดงผล `**OVER-SUBSCRIBED**` และ `array-connect` ที่มีความละเอียดถูกตรึง **จะปฏิเสธการเชื่อมต่อ** (ในสถานการณ์อื่น ๆ การลดเฟรมจะลดจำนวนเฟรมลง แต่ไม่ช่วยแก้ไขปัญหานี้ได้เช่นกัน) `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` จะลดระดับการปฏิเสธนี้ลงเป็นคำเตือนที่ชัดเจนสำหรับการทดสอบประสิทธิภาพ — ดู [ตัวแปรสภาพแวดล้อม](#environment-variables).

### สภาพของอาร์เรย์ — ระบบย่อยใดกำลังสูญเสียเฟรม

`GET /api/camera/array/<array_id>/capability` ของอาร์เรย์ที่เชื่อมต่ออยู่จะแสดง
บล็อก `health` ที่กำลังทำงานอยู่ ซึ่งถูกประเมินใหม่ในหน้าต่าง **10 วินาที** ที่หมุนเวียน มันแบ่งการสูญเสียเฟรม
ออกเป็นสองสาเหตุที่ต้องการการแก้ไขที่ตรงกันข้าม แทนที่จะรายงานเป็น &quot;inสมบูรณ์&quot;
ที่ไม่ระบุสาเหตุใด:

| สนาม | ความหมาย | ระบบย่อยใด |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (ต่อพอร์ตอนุกรม) | เฟรม **มาถึงแต่มีโครงสร้างผิดปกติ**— การสูญเสียแพ็กเก็ต GVSP. |**เครือข่าย**: งบประมาณสาย, การควบคุมความเร็ว, วงแหวนรับ NIC, MTU |
| `never_arrived_rate_pct` (ต่อพอร์ตอนุกรม) | เฟรม **ไม่มาถึงเลย**— กล้องไม่ทำงาน หรือไม่มีข้อมูลส่งออกมา |**ทริกเกอร์ / ซิงค์**: สาย M8, `--line`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | อัตราการส่งข้อมูลที่แย่ที่สุดของกล้องสำหรับ แต่ละตัว | — |
| `per_cam_rate_pct` | อัตราการไม่สมบูรณ์รวมต่อกล้อง (ทั้งสองสาเหตุรวมกัน) | — |
| `stable_for_seconds` | ระยะเวลาที่กล้องแต่ละตัวอยู่ต่ำกว่า 0.01 % | — |

เมื่อเกิน 5% ระบบหลังจะบันทึกบรรทัด `[array-health <id>] WARN` ที่ระบุชื่อการแบ่ง — เมื่อ
เกิดการละเมิดครั้งแรก เมื่อมีการเปลี่ยนแปลงระดับความรุนแรง ทุกนาทีในขณะที่ปัญหายังคงอยู่ และอีกครั้งเมื่อ
มันกลับสู่ปกติ ส่วนที่เสียหายจะพิมพ์ `[gvsp-corrupt <SN>]` เมื่อเกิดการละเมิดครั้งแรกต่อกล้องและ
สาเหตุ แล้วตามด้วยการสรุปผลทุก 60 วินาที การประเมินทุกครั้งยังคงถูกบันทึกในไฟล์บันทึกของระบบหลัง |
ตัวนับจะเพิ่มขึ้นในทุกบัฟเฟอร์ ไม่ว่าจะมีข้อความอะไรถูกพิมพ์ออกมา |

บันทึกเดียวกันนี้รายงานจำนวนที่การจัดสรรทั้งหมดขึ้นอยู่กับ:

| Field | ความหมาย |
| --- | --- |
| `wire_ceiling_mbps` | งบประมาณการส่งข้อมูลต่อเนื่องของโฮสต์ที่ใช้งานอยู่, MB/s. |
| `wire_ceiling_source` | ที่มาของตัวเลขนั้น ในรูปแบบคำ — เช่น `USB-capped 200 MB/s (was theoretical 1062; PnPDeviceID=USB\VID_0BDA&PID_815A)` หรือ `user override 120 MB/s (auto said 200)`. |
| `wire_ceiling_is_user_set` | `true` เมื่อ `--wire-ceiling-mbps` (หรือช่อง **Wire Budget** ใน GUI) กำหนดค่านี้ |
| `nic_is_usb` | `true` สำหรับอะแดปเตอร์อีเทอร์เน็ต USB — ดูขีดจำกัด 200 MB/s ข้างต้น |

**การอ่านค่า:** ค่า `gvsp_corrupt_rate_pct` ที่ไม่เท่ากับศูนย์ พร้อมกับค่า `never_arrived_rate_pct` ที่ 0
หมายความว่า การทริกเกอร์และการซิงค์สายเคเบิลสมบูรณ์แบบ และการสูญเสีย 100% เกิดขึ้นบนเส้นทางเครือข่าย
— ค่า `--wire-ceiling-mbps` และเชื่อมต่อใหม่ รูปแบบที่ตรงกันข้ามชี้ไปที่
สายซิงค์หรือสายทริกเกอร์แทน

> **`--target-fps` ไม่ใช่ตัวควบคุมสำหรับเฟรมที่เสียหาย** การปรับจังหวะ GevSCPD ถูกเขียน
> เพียงครั้งเดียวเมื่อเชื่อมต่อ ดังนั้นการลดอัตราการทริกเกอร์จะเปลี่ยนรอบการทำงาน (duty cycle) แต่ไม่เปลี่ยน
> อัตราการส่งข้อมูลแบบพร้อมกัน (simultaneous-emit burst rate) การลดความต้องการลง 5× ที่วัดได้ไม่ก่อให้เกิดการปรับปรุง;
> การลดขีดจำกัดความเร็วสายจาก 240 เป็น 200 MB/s ทำให้ระบบเดียวกันนี้ลดอัตราการเฟรมเสียหายจาก 10.4 %
> ลงเหลือ 0.00 %

> **การลดขนาดอัตโนมัติระหว่างการส่งข้อมูลไม่สามารถใช้งานได้บนเฟิร์มแวร์ TRI032S.** อาร์เรย์ที่กำลังทำงาน
> ไม่สามารถแก้ไขปัญหานี้ได้ด้วยตัวเอง; ต้องตัดการเชื่อมต่อและเชื่อมต่อใหม่ เพื่อให้ตัวเลือกระยะเวลาการเชื่อมต่อสามารถ
> วางแผนใหม่ตามขีดจำกัดใหม่

### อาการ → วิธีแก้ไข

| อาการ (การตั้งค่าอาร์เรย์ / การเชื่อมต่อ / `analyze_array_network`) | สาเหตุ | วิธีแก้ไข |
| --- | --- | --- |
| `FRAMES WILL DROP … exceeds NIC RX ring usable capacity 0.26 MB`, `Reduce ROI to enable` | `ReceiveBufferLen` ถูกตั้งค่าใหม่เป็น 32 (โดยทั่วไปหลังการอัปเดตไดรเวอร์) | ตั้งค่า `ReceiveBufferLen`→256, `PendingReceives`→64; เปิดแผงควบคุมใหม่ (รีสตาร์ทระบบหลังบ้านหากมีการเก็บค่าขนาดวงแหวนเก่าไว้ในแคช) |
| การรีสตาร์ท/ปิดเครื่องค้าง; ต่อมาเกิดข้อผิดพลาด WMI ของ `Invalid class`, NIC ไม่สามารถเปิดใช้งานได้, USB drives missing | ไดรเวอร์ Realtek USB 10GbE รุ่นเก่าปี 2016 → BSOD `0x9F` → ปิดเครื่องแบบบังคับ | อัปเดตไดรเวอร์อะแดปเตอร์เป็นเวอร์ชัน ≥ v10.67 (2026) แล้วตั้งค่าการรับ-|
| การเชื่อมต่อสำเร็จแต่คืนค่าความละเอียด sub-native | Smart-prep ลดขนาดเฟรมอัตโนมัติให้เหมาะสมกับสาย | อัปเกรดลิงก์ / ยอมรับการลดขนาด / `--force-tier slip-emit-and-capture` |
| Array รายงานค่า fps เป้าหมายที่ปกติ แต่ส่งได้เพียงส่วนน้อยของค่านั้น; `health.gvsp_corrupt_rate_pct` non-เป็นศูนย์, `never_arrived_rate_pct` 0 | งบประมาณสายที่โฮสต์คาดการณ์ไว้สูงเกินกว่าที่มันสามารถรักษาได้จริง (มักเกิดขึ้นกับอะแดปเตอร์ USB Ethernet, เลน PCIe ที่แคบ หรือโครงสร้างเครือข่ายที่ใช้ร่วมกัน) | เชื่อมต่อใหม่ด้วยค่า `--wire-ceiling-mbps` ที่ต่ำกว่า และตรวจสอบบล็อกสถานะอีกครั้ง **ไม่ใช่** `--target-fps` — การปรับจังหวะ GevSCPD ถูกตั้งค่าคงที่เมื่อเชื่อมต่อ |
| กล้องไม่ปรากฏในกลุ่มที่เผยแพร่; `health.never_arrived_rate_pct` ไม่เป็นศูนย์, `gvsp_corrupt_rate_pct` 0 | เส้นทางทริกเกอร์/ซิงค์ — กล้องไม่ทำงาน ไม่ใช่ปัญหาเครือข่าย | ตรวจสอบ สายซิงค์ M8 และ `--line`; ตรวจสอบให้แน่ใจว่าทุกสมาชิกอยู่ในสถานะเปิดใช้งาน (`TriggerMode=On`) |
| `**OVER-SUBSCRIBED**` / `Wire budget` เกินค่าใน `analyze-array` หรือถูกปฏิเสธการเชื่อมต่อเนื่องจากความละเอียดที่ถูกกำหนด (`array over-subscribes the wire`) | ความต้องการรวมต่อกล้อง (ขั้นต่ำ 8 MB/s × N กล้อง) เกินขีดจำกัดสูงสุดของสายที่ปลอดภัยจากการชน — 6 กล้องความละเอียดเต็มบน 1 GbE @1500 MTU, 9 กล้องเมื่อใช้ jumbo | ลดจำนวนกล้อง, ใช้ jumboเฟรมแบบบิตต่อบิตจากต้นทางถึงปลายทาง หรือ NIC ที่เร็วขึ้น **ROI/binning จะไม่ช่วย** (ขีดจำกัดนี้ไม่ขึ้นอยู่กับขนาดเฟรม). `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` มีประสิทธิภาพดีกว่าบนโต๊ะทดสอบ (ยอมรับการสูญเสียแพ็กเก็ต) |

---

## `chloros-cli daq`

คำสั่งสำหรับเซ็นเซอร์สเปกตรัม มีสองประเภท:
- **`pool-*`**— ลูกค้าแบบบาง (HTTP) ที่ควบคุมเซ็นเซอร์ผ่านกลุ่ม (pool) ที่คงที่ของระบบหลัง (backend)**นี่คือเส้นทางที่ได้รับการสนับสนุน และเป็นเส้นทางเดียวที่มีอยู่ใน CLI ที่จัดส่ง** ระบบหลัง (backend) เป็นเจ้าของกระบวนการส่งข้อมูล ดังนั้น GUI, สคริปต์ CLI และ SDK จึงใช้ handle เดียวที่ทำงานอยู่ร่วมกัน แทนที่จะแข่งขันกันเพื่อใช้พอร์ตซีเรียล
- **ส่วนอื่นๆ ทั้งหมด**(`test`, `record`, `live`, `stream`, `connect`, `info`, `net`, `ota`, `sample-rate`, `calibrate`, `serve`, `ws`, `udp`, `mqtt`, `reflectance`, `login`, `logout`, `status`) — การเข้าถึงฮาร์ดแวร์โดยตรง ซึ่งได้บันทึกไว้ด้านล่างเพื่อความครบถ้วน รายการเหล่านี้ต้องการแพ็กเกจ `daq` Python ซึ่ง**ไม่รวมอยู่ในอาร์ติแฟกต์ที่จัดส่งใดๆ**: CLI ที่ถูกคอมไพล์แล้วไม่รวมแพ็กเกจนี้ (`scripts/Build-CLI.ps1` ตั้งค่า `--nofollow-import-to=daq` และทรานสปอร์ต `pyserial` / `bleak` / `zeroconf` ไม่รวมมันด้วย), และแพ็กเกจ PyPI SDK ก็ไม่รวมมันเช่นกัน มันทำงานได้เฉพาะจากแหล่งโค้ดต้นเท่านั้น, ดังนั้นควรพิจารณาพวกมันเป็นเส้นทางพัฒนาภายในของ MAPIR มากกว่าที่จะเป็นสิ่งที่ควรใช้
- **`discover` / `list`** อยู่ระหว่างสองกรณี: พวกมันเป็นคำสั่งฮาร์ดแวร์โดยตรงจากแหล่งโค้ดต้นแบบ, แต่ในเวอร์ชันที่ปล่อยออกสู่ตลาด พวกมันจะเปลี่ยนไปใช้ `pool-discover` และส่วนหลัง (backend) จะดำเนินการสแกน ดังนั้นการสแกนจึงทำงานได้ทุกที่ — ซึ่งสำคัญมาก เพราะนี่เป็นวิธีเดียวที่จะทราบ BLE MAC ของ DAQ-M

> **`chloros-cli daq --help`** (และ `-h` / `help`) แสดงรายการคำสั่งย่อยของ `pool-*` — คำแนะนำถูกส่งไปยัง ไปยัง client pool เพื่อให้สะท้อนคำสั่งที่ทำงานจริง หากคุณเรียกใช้คำสั่งย่อยที่เชื่อมต่อกับฮาร์ดแวร์โดยตรงบนเวอร์ชันที่จัดส่งมา ระบบจะปิดตัวลงพร้อมข้อผิดพลาดที่ระบุชื่อแพ็กเกจที่ขาดหายไปอย่างชัดเจน และชี้ให้คุณกลับไปยัง `pool-*`; ไม่มีอะไรที่ล้มเหลวอย่างเงียบๆ (`discover` / `list` เป็นข้อยกเว้น — คำสั่งเหล่านี้จะเปลี่ยนเส้นทางไปยัง `pool-discover` และทำงานได้ตามปกติ)
>
> **ทุกสิ่งที่ลูกค้าต้องการสามารถเข้าถึงได้ผ่าน `pool-*`** — เชื่อมต่อ, ส่งข้อมูลแบบสตรีม, บันทึกไฟล์ `.daq` ที่ได้รับการปรับเทียบ, และเปลี่ยนโปรไฟล์ตัวเก็บประจุ DAQ ยังสามารถควบคุมได้จาก Python ด้วย `chloros_sdk.connect_daq_sensor()` ซึ่งใช้เส้นทางรวมเดียวกัน

### กระบวนการเชื่อมต่อครั้งแรกของเซ็นเซอร์ DAQ

```bash
# 1. Smart-detect any DAQ on this machine (Ethernet → BLE → USB precedence)
chloros-cli daq connect

# 2. Detailed scan: every transport, showing the address to connect with.
#    This is how you find a DAQ-M's BLE MAC — unlike a DAQ-E hostname or a
#    DAQ-U COM port, a MAC isn't printed on the device or listed by the OS.
chloros-cli daq discover                      # or: daq pool-discover
chloros-cli daq discover --only ble           # BLE only
chloros-cli daq discover --json               # machine-readable

# 3. Open a persistent pool session (handle stays alive across CLI calls)
chloros-cli daq pool-connect           # smart-detect
chloros-cli daq pool-connect --port COM3                       # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF           # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local        # DAQ-E by hostname

# 4. List what's in the pool, including the sensor_id you'll use next
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 5. Read the latest spectrum frame
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

# 6. Record a calibrated .daq file for 60s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 7. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

### เอกสารอ้างอิง `pool-*`

| คำสั่งย่อย | วัตถุประสงค์ |
| --- | --- |
| `daq pool-connect` (smart-detect) | เปิดเซ็นเซอร์ในพูลแบ็กเอนด์ |
| `daq pool-connect --port PORT` | DAQ-U บนพอร์ตซีเรียลเฉพาะ |
| `daq pool-connect --ble` | DAQ-M ผ่าน BLE, MAC ที่ถูกสแกนอัตโนมัติ |
| `daq pool-connect --mac MAC` | DAQ-M ผ่าน BLE ที่ MAC ที่ทราบ (ต้องใช้ `--ble`) |
| `daq pool-connect --eth-host HOST` | DAQ-E ผ่าน Ethernet ที่โฮสต์ที่ทราบ |
| `daq pool-connect --eth` | DAQ-E ผ่าน Ethernet, โฮสต์ถูกค้นพบอัตโนมัติ (mDNS + ARP fallback; ทำงานได้แม้จาก ARP cache ที่ว่างเปล่าบน Windows และ Linux). |
| `daq pool-connect --integration-time MS --frame-avg N --no-ae` | Tไม่มีหน้าต่างการรวม / สถานะ AE. |
| `daq pool-connect --no-stream` | เชื่อมต่อแล้วแต่ยังไม่เริ่มสตรีม (ต่อด้วย `pool-stream --start`). |
| `daq pool-connect --cap-id {none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}` | โปรไฟล์การปรับแก้ Cap. ค่าเริ่มต้นที่ฝั่งหลังคือ `sunshine_cosine`. |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | สแกนทุกการขนส่งเพื่อค้นหาเซ็นเซอร์ที่สามารถเชื่อมต่อได้ โดยไม่ทำการเชื่อมต่อ **นี่คือวิธีหา BLE MAC ของ DAQ-M** `daq discover` / `daq list` จะถูกกำหนดเส้นทางมาที่นี่โดยอัตโนมัติในเวอร์ชันที่จัดส่งแล้ว เซ็นเซอร์ที่เปิดอยู่ในกลุ่มแล้วจะไม่ถูกแสดง — DAQ-M ที่เชื่อมต่ออยู่จะหยุดการโฆษณา — ดังนั้นให้ใช้ `pool-list` สำหรับกรณีดังกล่าว |
| `daq pool-list` | แสดงเซ็นเซอร์ทุกตัวในพูลของระบบหลัง |
| `daq pool-disconnect --sensor-id ID [--all]` | ปล่อย |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | เฟรมสเปกตรัม N ล่าสุด |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | ดำเนินการสตรีมต่อ / หยุดชั่วคราว |
| `daq pool-record --sensor-id ID [--duration SEC] [--output DIR] [--device-name NAME] [--stop]` | เริ่ม / หยุดการบันทึก .daq |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | เปลี่ยนโปรไฟล์การปรับค่าความจุ (cap-correction) ระหว่างการทำงาน |

### คำสั่งย่อยที่ควบคุมฮาร์ดแวร์โดยตรง (มีเฉพาะในโค้ดต้นฉบับ — ไม่มีในเวอร์ชันที่จัดส่ง)

> ระบุไว้เพื่อความครบถ้วน คำสั่งเหล่านี้ต้องการแพ็กเกจ `daq` Python พร้อมทั้ง `pyserial` / `bleak` / `zeroconf` ซึ่งไม่มีในเวอร์ชันที่คอมไพล์แล้ว CLI หรือ PyPI SDK — สามารถทำงานได้เฉพาะจากแหล่งโค้ดที่เช็คเอาต์จาก MAPIR **หากคุณกำลังใช้เวอร์ชันที่ปล่อยออกมาแล้ว Chloros ให้ใช้คำสั่ง `pool-*` ข้างต้นแทน**; คำสั่งเหล่านี้ครอบคลุมการเชื่อมต่อ, การสตรีม, การบันทึก และการเลือกแคปเจอร์

```bash
chloros-cli daq test --port COM3                           # Verify connection
chloros-cli daq connect --eth                              # Smart-detect over ETH
chloros-cli daq info --eth-host daq-e-xxx.local            # Device summary as JSON
chloros-cli daq discover --only usb,ble --timeout 5        # Scan local interfaces
chloros-cli daq list                                       # Alias of discover
# ^ discover/list are the exception in this section: in a shipped build they
#   fall back to `pool-discover` (the backend does the scan), so they work
#   without a source checkout. The only difference is that the fallback needs
#   the Chloros backend running, as all pool-* commands do.

# Streaming JSON Lines to stdout (pipeable)
chloros-cli daq stream --port COM3 --format jsonl --photometrics

# Record to .daq for 60 seconds
chloros-cli daq record --port COM3 --duration 60 -o ~/Documents/spectra/

# Live spectrum visualization in a window
chloros-cli daq live --port COM3 --record

# Dual-sensor reflectance (ambient + object) → JSON Lines
chloros-cli daq reflectance \
  --ambient-eth-host daq-e-field.local \
  --object-eth-host daq-e-canopy.local \
  --record -o ~/Documents/reflectance/

# Convenience: pick integration_time + frame_avg for a target rate
chloros-cli daq sample-rate --port COM3 --target-hz 5

# Calibration profile management
chloros-cli daq calibrate --port COM3 --list
chloros-cli daq calibrate --port COM3 --set field_calibration_2026_05

# DAQ-E network config (mDNS auto-discovers the host)
chloros-cli daq net --eth-host daq-e-xxx.local set-ip --mode static --ip 192.168.2.20
chloros-cli daq net --eth-host daq-e-xxx.local set-name "sky-sensor"
chloros-cli daq net --eth-host daq-e-xxx.local set-ptp --enabled true --domain 0
chloros-cli daq net --eth-host daq-e-xxx.local set-auto-stream true          # auto-stream on boot
chloros-cli daq net --eth-host daq-e-xxx.local set-require-signature         # require factory-signed cal (fw v1.6.0+; refused while the held cal is unsigned)
chloros-cli daq net --eth-host daq-e-xxx.local set-time                      # push host clock (refused when PTP SLAVE)
chloros-cli daq net --eth-host daq-e-xxx.local set-auth-token --current "" --new "s3cret"   # control-channel auth ("" new = disable)
chloros-cli daq net --eth-host daq-e-xxx.local set-ota-password "newpass"    # change OTA password (min 4 chars)
chloros-cli daq net --eth-host daq-e-xxx.local factory-reset                 # clear all NVS settings and reboot
chloros-cli daq net --eth-host daq-e-xxx.local reboot

# OTA firmware update
chloros-cli daq ota --eth-host daq-e-xxx.local \
  --firmware daq_e_1.21.bin --password mapir-daq-e

# Bridge spectra to other protocols
chloros-cli daq serve --port COM3 --tcp-port 9000           # TCP JSON-lines
chloros-cli daq ws    --port COM3 --ws-port 9001            # WebSocket
chloros-cli daq udp   --port COM3 --udp-port 9002           # UDP broadcast
chloros-cli daq mqtt  --port COM3 --broker mqtt.example.com --topic daq/spectrum
```

---

## `chloros-cli project`

เปิด เชื่อมต่อ และควบคุมโครงการ Chloros ที่บันทึกไว้ (โฟลเดอร์ที่มี `cameras.json` + `sensors.json` + `project.json`) ทุกอย่างจะถูกส่งผ่านระบบหลังบ้าน (backend) ดังนั้น GUI และ CLI จะแสดงสถานะฮาร์ดแวร์ที่เหมือนกัน.

### อ้างอิงคำสั่งย่อย

| คำสั่งย่อย | วัตถุประสงค์ |
| --- | --- |
| `project open PATH` | พิมพ์รายการอุปกรณ์ของโครงการ (กล้อง, อาร์เรย์, เซนเซอร์) |
| `project devices PATH [--reconnect]` | แสดงรายการหรือดำเนินการค้นหาอุปกรณ์ใหม่ |
| `project connect PATH [--cameras-only] [--sensors-only]` | เชื่อมต่อกล้อง / อาร์เรย์ / เซนเซอร์ที่บันทึกไว้ทั้งหมด |
| `project capture PATH NAME [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P]` | ถ่ายภาพครั้งเดียวจากกล้องหรืออาร์เรย์ที่ระบุชื่อ | |
| `project burst PATH NAME [-n N] [-i S] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P]` | ถ่ายภาพต่อเนื่อง N เฟรมจากกล้องหรืออาร์เรย์ที่ระบุชื่อ (`-n/--count` ค่าเริ่มต้น 5; `-i/--interval` เวลาเป็นวินาทีระหว่างเฟรม ค่าเริ่มต้น 0). การถ่ายภาพต่อเนื่องของอาร์เรย์จะ-dup กลุ่มที่ซิงค์ซ้ำ (ระบบเฝ้าระวังความล้าสมัย) เพื่อป้องกันไม่ให้อาร์เรย์ที่ทำงานแบบวงจรบางส่วนส่งคืน N สำเนาของเฟรมเดียว; แสดงผลลัพธ์ต่อรอบการทำงาน |
| `project stream PATH NAME [-n N] [--fps F] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--poll-interval S]` | ส่งข้อมูลแบบสตรีมไปยังดิสก์ผ่านงานแบ็กเอนด์ `--poll-interval` = วินาทีระหว่างการตรวจสอบ `/stats` (ค่าเริ่มต้น 2.0). |
| `project sensor read PATH NAME [--json]` | เฟรมสเปกตรัมล่าสุด |
| `project sensor log PATH NAME --seconds SEC [-o DIR] [--device-name NAME]` | บันทึก .daq |
| `project run PATH RECIPE.yaml` | ดำเนินการตามสูตรการจับภาพ YAML/JSON `--dry-run` ตรวจสอบความถูกต้องโดยไม่ดำเนินการ |
| `project align calibrate PATH NAME [--method M] [--model M] [--frames N] [--reference SN] [--max-features N] [--ratio-threshold F] [--ransac-threshold-px F] [--min-matches N] [--max-reproj-err-px F] [--checkerboard RxC] [--name PROFILE]` | คำนวณการปรับแนวสำหรับอาร์เรย์ — ดู [ตารางธงด้านล่าง](#project-align-calibrate-options). |
| `project align status PATH NAME [--json]` | แสดงโปรไฟล์การจัดแนวปัจจุบัน |
| `project align clear PATH NAME` | ลบโปรไฟล์ที่เก็บไว้ในแคช | |
| `project align tweak PATH NAME --serial SN --dx N --dy N --rotation-deg N --scale N` | ปรับเปลี่ยนการแปลงของสเลฟหนึ่งตัว |
| `project align export PATH NAME --to FILE` | บันทึกโปรไฟล์ลงในJSON |
| `project align import PATH NAME --from FILE [--no-validate]` | โหลดโปรไฟล์ที่บันทึกไว้ |

#### `project align calibrate` ตัวเลือก

| ตัวเลือก | ค่าเริ่มต้น | คำอธิบาย |
| --- | --- | --- |
| `--method {feature_orb, feature_akaze, phase_correlation, checkerboard, manual}` | `feature_orb` | วิธีจัดแนว **การเขียนคำเหล่านี้แตกต่างจาก `lattice align-calibrate`** ซึ่งใช้รูปแบบสั้น `orb` / `akaze` / `phase`; สองคำสั่งนี้ไม่สามารถใช้แทนกันได้สำหรับแฟล็กนี้ |
| `--model {translation, rigid, affine, homography}` | `affine` | แปลงโมเดลให้พอดี |
| `--frames N` | `1` | เฟรมที่ซิงค์แล้วจะปรับให้ตรงกับค่าเฉลี่ยให้ตรงกับค่าเฉลี่ย |
| `--reference SN` | กล้องหลัก | หมายเลขอ้างอิงของกล้อง; สมาชิกอื่น ๆ ทุกตัวจะถูกบิดเบือนให้สอดคล้องกับมัน |
| `--max-features N` | `5000` | การทดสอบอัตราส่วน ORB|
| `--ratio-threshold F` | `0.75` | การทดสอบอัตราส่วน Lowe |
| `--ransac-threshold-px F` | `3.0` | ค่าเกณฑ์จุดข้อมูลภายในของ RANSAC |
| `--min-matches N` | `15` | **เกณฑ์คุณภาพ** — ปฏิเสธผลลัพธ์การแก้ปัญหาหากจำนวนจุดข้อมูลภายในที่ตรงกันน้อยกว่าค่านี้ |
| `--max-reproj-err-px F` | `4.0` | ****เกณฑ์คุณภาพ** — ปฏิเสธการแก้ปัญหาหากค่า RMS ของข้อผิดพลาดในการฉายซ้ำเกินค่านี้ |
| `--checkerboard RxC` | — | รูปทรงของบอร์ดสำหรับ `--method checkerboard`, เช่น `9x6` |
| `--name PROFILE` | ว่าง | ชื่อโปรไฟล์ที่ฝังอยู่ในไฟล์ JSON ที่บันทึกไว้ **ไม่ใช่ชื่ออาร์เรย์** — นั่นคือ `NAME` ที่กำหนดตำแหน่ง |

ประตูคุณภาพทั้งสองนี้เป็นเหตุผลที่ทำให้การปรับเทียบสามารถแก้ปัญหาได้สำเร็จ แต่ยังคง
ปฏิเสธ การบันทึก: โปรไฟล์ที่ล้มเหลวในหนึ่งในสองเกณฑ์ดังกล่าวจะลงทะเบียนผิดอย่างเงียบๆ สำหรับทุก
การจับภาพในภายหลังทั้งหมด ดังนั้นจึงถูกปฏิเสธแทนที่จะถูกเก็บไว้

### ตัวอย่าง

```bash
# Open a project and see what it knows about
chloros-cli project open "/home/user/Chloros Projects/Field_A"

# Connect everything saved in the project
chloros-cli project connect "/home/user/Chloros Projects/Field_A"

# Capture from a named camera (defined in cameras.json)
chloros-cli project capture "/home/user/Chloros Projects/Field_A" FrontLeft \
  -o output/ --format tiff

# Capture from a named array
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  -o output/ --format tiff

# Capture with overrides
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  --exposure 5000

# Read a spectrum
chloros-cli project sensor read "/home/user/Chloros Projects/Field_A" Sky --json

# Record a DAQ log
chloros-cli project sensor log "/home/user/Chloros Projects/Field_A" Sky \
  --seconds 120 -o ~/Documents/spectra/

# Align an array (live)
chloros-cli project align calibrate "/home/user/Chloros Projects/Field_A" main_rig
chloros-cli project align status "/home/user/Chloros Projects/Field_A" main_rig

# Run a recipe
chloros-cli project run "/home/user/Chloros Projects/Field_A" recipe.yaml
```

### DSL ของสูตร

`project run RECIPE.yaml` รับไฟล์ YAML หรือ JSON ที่อธิบายลำดับการดำเนินการ:

```yaml
# recipe.yaml
overrides:
  cameras:
    FrontLeft:
      exposure_us: 5000
      target_brightness: 80

stop_on_error: true
actions:
  - apply:
      name: FrontLeft
      settings:
        exposure_auto: "Off"
        gain: 6.0
        gain_auto: "Off"
  - wait: 2s
  - capture:
      name: FrontLeft
      output: pose_a/
      format: tiff
  - stream:
      name: main_rig
      count: 60
      fps: 5
      output: stream/
  - burst:
      name: main_rig
      count: 10
      interval: 0.5
      output: burst_a/
      format: tiff
  - sensor:
      name: Sky
      action: read
```

การดำเนินการที่รองรับ: `apply`, `wait`, `capture`, `stream`, `burst`, `sensor`. การดำเนินการ `burst` ต้องใช้ `name` (จำเป็น), `count` (ค่าเริ่มต้น 5), `interval` (วินาที, ค่าเริ่มต้น 0), `output`, `format`, และ `settings` (รูปแบบการตั้งค่าต่อกล้องเดียวกันกับ `apply`); การถ่ายภาพต่อเนื่องแบบอาร์เรย์ใช้ตัวเฝ้าระวังกลุ่มที่เพิ่งซิงค์ใหม่เดียวกันกับ `project burst`

เรียกใช้:

```bash
chloros-cli project run "/path/to/project" recipe.yaml

# Dry-run to validate without firing hardware
chloros-cli project run "/path/to/project" recipe.yaml --dry-run
```

---

## ตัวแปรสภาพแวดล้อม

| ตัวแปร | ผล |
| --- | --- |
| `CHLOROS_BACKEND_URL` | กำหนดค่าทับตัวแปร backendURL (ค่าเริ่มต้น `http://127.0.0.1:5000`) — **ถูกใช้เฉพาะกับกลุ่มคำสั่ง `lattice`, `project` และ `daq pool-*` เท่านั้น.** คำสั่งหลัก (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) จะล็อก `http://127.0.0.1:<port>` และเพิกเฉยตัวแปรนี้ (ค่า IPv4 แบบตัวอักษรจะหลีกเลี่ยงการกำหนดค่าเริ่มต้นของ Windows `localhost`→`::1` ~2 ต่อคำขอ), ดังนั้นจึงมุ่งเป้าไปที่เครื่องท้องถิ่นเสมอ |
| `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED` | `1` ลดระดับการปฏิเสธการเชื่อมต่อเนื่องจากมีการสมัครเกินขีดจำกัดของอาร์เรย์ (ความต้องการรวมต่อกล้อง &gt; ขีดจำกัดสายที่ปลอดภัยจากการชนกันกับ `pin_resolution`) เป็นคำเตือนดังและดำเนินการต่อ โดยยอมรับการสูญเสียแพ็กเก็ต GVSP ใช้สำหรับการทดสอบเท่านั้น — ดู [โมเดล fps &amp; burst ของอาร์เรย์](#array-fps--burst-model). |
| `CHLOROS_CLI_MODE` | ตั้งโดยCLIเอง; แจ้งให้ระบบหลังเปิดใช้งานการประมวลผลแบบขนาน |
| `CHLOROS_GVSP_PROBE_FALLBACK` | `0` ข้ามการตรวจสอบการเปลี่ยนไปใช้ GVSP สำรอง (เฉพาะผลลัพธ์ ICMP เท่านั้น). **การตั้งค่านี้จะปิดการใช้งานแพ็กเก็ตจัมโบ้ ไม่ใช่แค่ลดระดับการบันทึกในล็อก** — กล้องจะตอบกลับ ping DF ได้สูงสุดเพียง 1500 เท่านั้นบนทุกเส้นทาง ดังนั้นการตรวจสอบนี้จึงเป็นวิธีเดียวที่สามารถตรวจจับแพ็กเก็ตจัมโบ้ได้ ช่วยประหยัดเวลาประมาณ 1 วินาทีต่อกล้องต่อการเชื่อมต่อ; ใช้ทรัพยากรประมาณ 1.45× ความเร็วสูงสุดของสาย หากเครือข่าย *สามารถ* ส่งแพ็กเก็ตจัมโบได้ ระบบแจ้งเตือนเมื่อตั้งค่านี้ (SDK) |
| `CHLOROS_GVSP_PACKET_SIZE_FORCE` | กำหนดขนาดแพ็กเก็ต GVSP เป็น N ไบต์; ข้ามการตรวจสอบทั้งหมด ให้ตั้งค่าตามคำสั่ง (`CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 chloros-cli …`) แทนที่จะตั้งค่าแบบถาวร: การตั้งค่าขนาดคงที่ทำให้ระบบหยุดปรับตัวให้เข้ากับเครือข่ายที่อยู่ด้านหน้า และหากตั้งค่า 9000 บนเส้นทางที่ไม่สามารถรองรับแพ็กเก็ตจัมโบ้ได้ จะทำให้ **ทุก** การจับข้อมูลจะหมดเวลาเสมอเมื่อใช้ `SC_ERR_TIMEOUT -1011`. |
| `TMPDIR` (Linux) | กำหนดทับไดเรกทอรีการสกัดไฟล์เดียวของ Nuitka. CLI จะใช้ `/mnt/ssd/tmp` โดยอัตโนมัติหากมีอยู่ |

---

## รหัสออก

| รหัส | ความหมาย |
| --- | --- |
| `0` | สำเร็จ |
| `1` | ความผิดพลาดทั่วไป (ข้อผิดพลาดของคำสั่งย่อยส่วนใหญ่) |
| `2` | ข้อผิดพลาดของอาร์กิวเมนต์ |
| `130` | ถูกขัดจังหวะด้วย Ctrl+C |

---

## คำแนะนำในการแก้ไขปัญหา

- **&quot;Login required&quot;** → 実行 `chloros-cli login EMAIL PASSWORD` หนึ่งครั้งบนเครื่องนี้
- **&quot;backend unreachable&quot;** → เปิดแอปพลิเคชันเดสก์ท็อป Chloros หรือเรียกใช้ไฟล์ไบนารีของ backend โดยตรง (`chloros-backend`) หรือตรวจสอบ `CHLOROS_BACKEND_URL` หากทำงานจากระยะไกล
- **คำสั่ง `lattice` ล้มเหลวด้วยข้อความ &quot;LATTICE camera drivers not found&quot;** → ยังไม่ติดตั้ง runtime Arena SDK; CLI มาพร้อมกับ `win32api` ที่รวมอยู่ใน Windows แต่ C runtime เป็นส่วนหนึ่งของตัวติดตั้ง GUI
- **Array connect / Array Settings แสดง &quot;FRAMES WILL DROP&quot; หรือ &quot;Reduce ROI to enable&quot;** → วงรับสัญญาณของ NIC ฝั่งโฮสต์มีขนาดเล็กเกินไป (มักถูกตั้งค่าใหม่เป็น 32 หลังจากอัปเดตไดรเวอร์ NIC) ดู [การตั้งค่าและปรับแต่ง NIC ของโฮสต์](#host-nic-setup--tuning-lattice-arrays) — ตั้งค่า `ReceiveBufferLen=256`, `PendingReceives=64`.
- **เครื่องค้างเมื่อรีสตาร์ท/ปิดเครื่อง แล้ว WMI `Invalid class` / NIC ไม่t enable / USB drives missing** → ไดรเวอร์อะแดปเตอร์ USB 10GbE ที่ล้าสมัย ทำให้เกิดข้อผิดพลาด `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). อัปเดตไดรเวอร์อะแดปเตอร์ — ดู [การตั้งค่าและปรับแต่ง NIC ของโฮสต์](#host-nic-setup--tuning-lattice-arrays).
- **คำเตือนการสวอปของ Jetson** → เพิ่มพื้นที่ swap ที่ใช้ไฟล์เป็นฐาน; คำสั่งCLIจะแสดงคำสั่ง `fallocate` / `swapon` ที่ถูกต้อง
- **คำสั่ง DAQ direct ขาด** → ที่คาดไว้: แพ็กเกจ `chloros-cli` ที่มาพร้อมเครื่องได้ยกเว้น `daq` ออกจากแพ็กเกจโดยเจตนา, ดังนั้นจึงมีเพียง `pool-*` เท่านั้น (PyPI SDK ก็ไม่รวมแพ็กเกจนี้เช่นกัน) ให้ใช้ `pool-*` ซึ่งควบคุมเซ็นเซอร์เดียวกันผ่านระบบหลังบ้าน หรือ `chloros_sdk.connect_daq_sensor()` จาก Python.

---

## ดูเพิ่มเติม

- [Python SDK Reference](sdk-reference.md) — คำสั่งโปรแกรมที่เทียบเท่าทุกคำสั่งใน CLI
- [คู่มือเซ็นเซอร์ DAQ](../daq/README.md) — การต่อสายและปรับเทียบเฉพาะเซ็นเซอร์
- เอกสารออนไลน์: `https://mapir.gitbook.io/chloros/cli`
