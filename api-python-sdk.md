# API : Python SDK

{% hint style="info" %}
**กำลังหา API แบบครบถ้วนอยู่ใช่ไหม?** หน้านี้คือคู่มือปฏิบัติจริง ทุกคลาส เมธอด ลายเซ็นที่ถูกต้อง และตัวอย่างที่สามารถคัดลอกและวางได้ อยู่ใน [SDK Reference](reference/sdk-reference.md) ซึ่งได้รับการปรับให้เหมาะสมสำหรับผู้ช่วย AI**กำลังทำงานกับผู้ช่วย AI อยู่หรือไม่?** วางURL นี้ลงในแชทเพื่อให้ผู้ช่วย AI มีChloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API Chloros API ทุกหน้าของคู่มือนี้มีให้ในรูปแบบ markdown ดิบที่ slug ตัวพิมพ์เล็ก + `.md` และคู่มือทั้งหมดถูกจัดทำดัชนีไว้ที่ `https://mapir.gitbook.io/chloros/llms.txt`
{% endhint %}

**Chloros Python SDK** (`chloros-sdk` บน PyPI) เป็นตัวขับเคลื่อนทุกฟังก์ชันที่แอปเดสก์ท็อปสามารถทำได้จาก Python: การประมวลผลภาพแบบแบทช์, การควบคุมกล้อง LATTICE และอาร์เรย์แบบเรียลไทม์, เซสชันเซ็นเซอร์แสง DAQ, และการทำงานอัตโนมัติของโครงการที่บันทึกไว้ นี่เป็นชั้นบางๆ ที่อยู่เหนือแบ็กเอนด์ท้องถิ่นเดียวกันที่ GUI และ CLI ใช้ (HTTP บน `127.0.0.1:5000`) ดังนั้นพฤติกรรมจึงเหมือนกันในทั้งสามอินเทอร์เฟซ

## การติดตั้ง

SDK มาพร้อมกับแพ็กเกจเดสก์ท็อป Chloros ซึ่งยังจัดเตรียมแบ็กเอนด์การประมวลผลและรันไทม์ฮาร์ดแวร์ด้วย และเวอร์ชันจะตรงกันเสมอ

**ขั้นตอน 1 — ติดตั้ง Chloros** Windows:  실행ตัวติดตั้งเดสก์ท็อป (เส้นทางเริ่มต้น `C:\Program Files\Chloros\`) จากหน้า [ดาวน์โหลด](download.md) Linux: ติดตั้งแพ็กเกจ `.deb` พร้อมกับ `sudo apt install ./chloros_1.3.0_<arch>.deb` ([การติดตั้งบน Linux](linux/linux-installation.md)).**ขั้นตอน 2 — ตรวจสอบ SDK** (Python 3.7+). โดยทั่วไปไม่จำเป็นต้องติดตั้งอะไรเพิ่มเติม:

* **Windows:** โปรแกรมติดตั้งจะติดตั้งไฟล์ wheel ของ SDK ที่มาพร้อมแพ็กเกจลงในระบบ Python ของคุณ (หากยังไม่มี Python ติดตั้งอยู่ โปรแกรมจะข้ามขั้นตอนนี้ไป)
* **Linux:** `.deb` จะติดตั้ง SDK สำหรับระบบ `python3` เข้าสู่ `/usr/lib/python3/dist-packages`. แพ็กเกจนี้แนะนำให้ติดตั้ง `python3-numpy` และ `python3-requests` ซึ่งเป็นข้อกำหนดของระบบ: `apt install` จะติดตั้งแพ็กเกจเหล่านี้ให้ แต่ `dpkg -i` จะไม่ติดตั้ง
* **A virtualenv** ไม่สามารถมองเห็นแพ็กเกจของระบบได้; ให้ติดตั้งไฟล์ wheel ที่มาพร้อมแพ็กเกจเข้าไปใน virtualenv: `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl` บน Linux หรือ `chloros_sdk-*.whl` ในโฟลเดอร์ `sdk` ของโฟลเดอร์ติดตั้ง Windows (`C:\Program Files\Chloros\sdk\` ตามค่าเริ่มต้น)

```bash
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"   # Windows: py -c "…"
```

บนเครื่องที่ไม่มีแพ็กเกจ Chloros ซึ่ง SDK ของมันควบคุมแบ็กเอนด์บนเครื่องอื่น (ดู [สิ่งที่การติดตั้งด้วย pip เท่านั้นจะได้รับ](#what-a-pip-only-install-gets)), ให้ติดตั้งจาก PyPI: `pip install chloros-sdk`. PyPI จะได้รับการอัปเดตเมื่อมีการปล่อยเวอร์ชันใหม่ ดังนั้นจึงสอดคล้องกับเวอร์ชันเสถียรล่าสุด

**ขั้นตอน 3 — ลงชื่อเข้าใช้ครั้งเดียวต่อเครื่อง:**

```bash
chloros-cli login user@example.com 'YourPassword'
```

ข้อมูลการเข้าสู่ระบบจะถูกเก็บไว้ในแคชของ `~/.chloros/` (ทั้งสองแพลตฟอร์ม) บน Windows คุณสามารถเข้าสู่ระบบได้ผ่านแท็บ User <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> ของแอปเดสก์ท็อปได้เช่นกัน SDK ต้องการแผน Chloros+ แบบชำระเงิน — ดู [ข้อกำหนดใบอนุญาต](#license-requirement) ด้านล่าง

| ข้อกำหนด | รายละเอียด |
| --- | --- |
| **ติดตั้ง Chloros** | Windows: โปรแกรมติดตั้งบนเดสก์ท็อป; Linux: แพ็กเกจ `.deb` (ให้ไฟล์ไบนารีแบ็กเอนด์และ SDK) |
| **Python** | 3.7 หรือสูงกว่า (พัฒนา/ทดสอบบน 3.10) |
| **ระบบปฏิบัติการ** | Windows 10/11 64-bit, Ubuntu 22.04 LTS หรือเวอร์ชันใหม่กว่า, หรือ NVIDIA Jetson (JetPack 6) |
| **ใบอนุญาต** | บัญชี Chloros+ ที่ใช้งานอยู่, ระดับบริการแบบชำระเงินใดก็ได้ (Copper หรือสูงกว่า) |

## ชนะใน 60 วินาที

เพียงเรียกใช้คำสั่งเดียว ก็สามารถสร้างโครงการ นำเข้าโฟลเดอร์ ตั้งค่าการประมวลผล และรันท่อประมวลผล — โดยจะเริ่มต้นระบบหลังบ้านอัตโนมัติหากยังไม่ได้ทำงานอยู่:

```python
import chloros_sdk

results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "GNDVI", "OSAVI"],
)
```

(บนLinux ให้ใช้เส้นทางแบบLinux: `/home/user/drone_images/flight001`. SDK ทำงานเหมือนกันบนทั้งสองแพลตฟอร์ม)

กำลังประมวลผลโฟลเดอร์ที่บันทึกจาก LATTICE? ใช้ wrapper ที่รองรับ LATTICE — มันจะตั้งค่าเริ่มต้นที่ถูกต้อง (ไม่ตรวจจับเป้าหมายของแผง, debayer มาตรฐาน):

```python
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)
```

## `ChlorosLocal` — การควบคุม pipeline อย่างเต็มรูปแบบ

สำหรับงานที่ซับซ้อนกว่าคำสั่งหนึ่งบรรทัด ให้ใช้ `ChlorosLocal` มันใช้แบ็กเอนด์ที่กำลังทำงานอยู่ (ของแอปเดสก์ท็อป) หรือจะเริ่มแบ็กเอนด์ที่ติดตั้งไว้เมื่อใช้ครั้งแรก (`auto_start_backend=True`; บนWindows จากโฟลเดอร์ `backend` ในโฟลเดอร์ติดตั้งChloros, `C:\Program Files\Chloros\backend\` ตามค่าเริ่มต้น), สร้างและกำหนดค่าโครงการ, ติดตามความคืบหน้า, และส่งคืนสรุปหลังการรัน

```python
ChlorosLocal(
    api_url="http://127.0.0.1:5000",   # backend URL (also: backend_url=)
    auto_start_backend=True,            # spawn backend if not running
    backend_exe=None,                   # override backend binary path
    timeout=30,                         # request timeout seconds
    backend_startup_timeout=60,         # backend boot timeout
    processing_timeout=14400,           # hard cap on process() (4 h)
    processing_stuck_timeout=1800,      # no-progress threshold (30 min)
)
```

{% hint style="info" %}
ให้ใช้ `http://127.0.0.1:5000` ตามค่าเริ่มต้นแทนที่จะเปลี่ยนเป็น `localhost` — บนWindows `localhost` จะถูกแปลงเป็น `::1` ก่อน และใช้เวลาราว 2 วินาทีต่อคำขอเมื่อใช้แบ็กเอนด์ที่รองรับ IPv4 เท่านั้น
{% endhint %}

ใช้มันเป็นผู้จัดการบริบท (context manager) การออกจากบล็อกจะยุติการใช้งานแบ็กเอนด์ของอินสแตนซ์นี้ ไม่ใช่ตัวแบ็กเอนด์เอง: มันจะยังคงทำงานต่อไปสำหรับสคริปต์ถัดไป และจะหยุดทำงานเองหลังจากไม่ถูกใช้งานเป็นเวลา 30 นาที `shutdown_backend()` จะหยุดการทำงานเมื่ออินสแตนซ์นี้เริ่มต้นขึ้น

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

`configure()` รับคำสำคัญต่อไปนี้: `debayer`, `vignette_correction`, `reflectance_calibration`, `indices`, `export_format`, `ppk`, `daq_log_path`, `input_level`, `radiometric_output`, `array_alignment`, `array_alignment_crop`, `array_alignment_interpolation`, และ `custom_settings` ค่าหลัก:

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (8-bit)"            # 8-bit TIFF; what all-8-bit images get for a deeper format
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"                  # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
```

ปุ่มปรับเฉพาะ LATTICE (`input_level`, `radiometric_output`, และกลุ่ม `array_alignment*`) ได้รับการบันทึกไว้พร้อมตารางค่าเต็มใน [SDK Reference](reference/sdk-reference.md#supported-values)

### การติดตามความคืบหน้า

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### การอ่านสรุปหลังการรัน — และการตรวจจับการรันที่ว่างเปล่า

เมื่อเสร็จสิ้น `process()` จะแนบสรุปการประมวลผลของแบ็กเอนด์เป็น `result["summary"]` แต่ละรายการใน `summary["hints"]` เป็นประโยคเต็มอธิบายสิ่งที่น่าสังเกต — เช่น ทำไมการรันจึงไม่สร้างผลลัพธ์ใดๆ — และทุกคำชี้แนะจะถูกส่งกลับอีกครั้งในรูปแบบPython `UserWarning` ดังนั้นการรันที่ว่างเปล่าจึงสามารถวินิจฉัยตัวเองได้ แม้คุณจะไม่ตรวจสอบ dict เลยก็ตาม:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

{% hint style="warning" %}
**`process()` ไม่ก่อให้เกิดข้อผิดพลาดเมื่อการรันไม่สร้างภาพใดๆ** นี่คือจุดเดียวที่SDK และCLI มีความแตกต่างโดยเจตนา: `chloros-cli process` ถือว่า "มีการขอผลิตภัณฑ์ แต่ไม่มีการเขียนผลิตภัณฑ์ใด" เป็นความล้มเหลวและออกค่าไม่เท่ากับศูนย์ ในขณะที่SDK จะคืนค่าตามปกติและรายงานสภาพดังกล่าวผ่าน `summary` / hints. หาก pipeline ของคุณควรหยุดเมื่อการรันไม่มีผลลัพธ์ ให้ตรวจสอบด้วยตัวเอง — ตรวจสอบ `summary` (หรือนับจำนวนไฟล์ในโฟลเดอร์โครงการ) แทนที่จะพึ่งพาข้อยกเว้น
{% endhint %}

## Smart Connect — ฮาร์ดแวร์แบบเรียลไทม์

มีตัวช่วยสามตัวที่เปิดเซสชันแบบถาวรในพูลฮาร์ดแวร์ของแบ็กเอนด์ — ซึ่งเป็นพูลเดียวกันที่CLI สคริปต์อื่น ๆ และแท็บกล้องของ GUI ใช้ ดังนั้นสคริปต์SDK จึงสามารถทำงานร่วมกับพวกมันได้โดยไม่เกิดการแย่งชิงพอร์ตอนุกรมหรือแบนด์วิดท์เครือข่าย ทั้งสามตัวจะเริ่มต้นแบ็กเอนด์ท้องถิ่นโดยอัตโนมัติหากยังไม่มีตัวใดกำลังทำงานอยู่ แผง DAQ ของ GUI ไม่เป็นส่วนหนึ่งของกลุ่มนี้: ปิดเซ็นเซอร์แสงใน GUI ก่อนที่จะเปิดมันจากสคริปต์

### กล้อง LATTICE เดี่ยว — `connect_camera`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)   # microseconds, dB
    cam.capture("output/")
```

{% hint style="info" %}
**เส้นทางสัมพัทธ์จะชี้ไปยังตำแหน่งที่สคริปต์ของคุณทำงาน (เปลี่ยนแปลงในเวอร์ชัน 1.3.0)** เมื่อใช้ backend ท้องถิ่น `output_dir` แบบสัมพัทธ์ (รวมถึง `"output"` ตามค่าเริ่มต้น) — และเส้นทางอื่น ๆ ที่ backend เปิด (`burst_dir`, `profile_path`, `save_path`, `mask_path`) — จะถูกกำหนดตำแหน่งตามไดเรกทอรีทำงานของสคริปต์ของคุณ ดังนั้น `cam.capture("output/")` จะเขียนข้อมูลลงใน `<current folder>/output/` ในเวอร์ชัน 1.2.0 เบ็กเอนด์ที่ติดตั้งไว้จะทำการแปลงเส้นทางเหล่านี้ในโฟลเดอร์ติดตั้งของตัวเอง และเกิดข้อผิดพลาด "Access is denied" เมื่อใช้กับเบ็กเอนด์ระยะไกล เส้นทางจะถูกส่งไปตามที่พิมพ์ไว้
{% endhint %}

### มصفوفةที่ซิงโครไนซ์ — `connect_array`

`connect_array` เป็นจุดเข้าที่แนะนำสำหรับระบบกล้องหลายตัว มันทำงานตามขั้นตอน smart-prep เดียวกันกับ GUI: การวิเคราะห์เครือข่าย, การเลือกชั้นการซิงค์อัตโนมัติ, การซิงค์เวลา PTP, การเลือกรูปแบบพิกเซลต่อกล้อง, การตั้งค่าเริ่มต้น AE, และการเตรียมทริกเกอร์ GPIO **อุปกรณ์ซีเรียลตัวแรกคือมาสเตอร์** (เป็นอุปกรณ์ที่ส่งสัญญาณทริกเกอร์ฮาร์ดแวร์); ส่วนที่เหลือเป็นสเลฟ

```python
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")
```

เพิ่ม `smart=True` ลงในกระบวนการจับภาพแบบอาร์เรย์ใดก็ตาม เพื่อรอให้การปรับแสงอัตโนมัติ (auto-exposure) เสถียรในทุกกล้องก่อนที่จะทริกเกอร์

คุณสมบัติใหม่ในเวอร์ชัน 1.3.0: `ArraySession` ยังส่งเฟรมที่ซิงโครไนซ์ให้คุณโดยตรง โดยไม่ต้องเขียนไฟล์:

* `arr.grab()` ส่งคืนเฟรมหนึ่งเฟรมต่อกล้องหนึ่งตัว ทั้งหมดจากทริกเกอร์เดียว ในรูปแบบอาร์เรย์ numpy (`{serial: array}`); `.group_id` เป็นสัญญาณทริกเกอร์ที่ใช้ร่วมกัน และ `.errors` จะระบุชื่อกล้องใดที่ถูกทิ้งไว้
* `arr.frame_stream(fps=5.0)` ให้ชุดข้อมูลดังกล่าวหนึ่งชุดต่อ tick เพื่อการประมวลผลแบบเรียลไทม์ใน pipeline ของคุณเอง
* `arr.continuous("output/")` ทำงานจับภาพต่อเนื่องของระบบหลัง (โหมดต่อเนื่องของ GUI) และส่งคืน `ContinuousHandle` พร้อมกับ `status()`, `wait()` และ `stop()` — ใช้แทนวงจรการจับภาพเมื่อคุณต้องการบันทึกทุกการทริกเกอร์ด้วยความเร็วสูง

สำหรับโหมดการบันทึก (Single / Continuous / Interval / Fastest), เครื่องบันทึก, burst-to-video และการจัดแนวอาร์เรย์ โปรดดู [SDK Reference](reference/sdk-reference.md#synchronized-array--arraysession-smart-prep).

### เซ็นเซอร์แสง DAQ — `connect_daq_sensor`

หากไม่ระบุอาร์กิวเมนต์ `connect_daq_sensor()` จะตรวจจับโปรโตคอลการส่งข้อมูลโดยอัตโนมัติ (ลำดับความสำคัญ: Ethernet → BLE → USB):

```python
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

แต่ละเฟรมจะนำข้อมูล `spectrum` 135 จุด (W/m²/nm เมื่อได้รับการปรับเทียบ), ธง `is_saturated` และ CIE `x`, `y`, `z`. เพื่อกำหนดเซ็นเซอร์หรือช่องทางส่งข้อมูลเฉพาะ ให้ส่งคำชี้แจงอย่างชัดเจนหนึ่งข้อ:

```python
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")        # implies BLE
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")     # implies Ethernet
```

โปรดทราบว่าโปรไฟล์การปรับแก้ cap (`cap_id`) **ไม่ใช่** ตัวปรับในSDK — ให้เลือกผ่าน `chloros-cli daq pool-connect --cap-id …` / `pool-set-cap` แทน

### โครงการที่บันทึกไว้ — `open_project`

โครงการChlorosที่บันทึกไว้จะเก็บรักษาอุปกรณ์ที่เชื่อมต่อไว้ (`cameras.json` + `sensors.json` พร้อมกับ `project.json`), และ `chloros_sdk.open_project(path)` สามารถเชื่อมต่อกล้อง แผง และเซ็นเซอร์แสงได้พร้อมกัน และควบคุมการจับภาพตามชื่ออุปกรณ์ เซ็นเซอร์แสงจะเชื่อมต่อใหม่ผ่านระบบหลังบ้านChloros ที่กำลังทำงานอยู่ พร้อมกับการตั้งค่าและขีดจำกัดที่บันทึกไว้ ดู [Project Automation](reference/sdk-reference.md#project-automation--chlorosproject) ในเอกสารอ้างอิง

## สิ่งที่ได้รับจากการติดตั้งแบบ pip เท่านั้น

ตรวจสอบธงความพร้อมใช้งานระดับโมดูลก่อนใช้พื้นผิวฮาร์ดแวร์:

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)    # True iff lattice_sdk imported cleanly
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)  # True iff ChlorosProject deps available
```

บนโฮสต์ที่มี **เพียง** `pip install chloros-sdk` และไม่มีแพ็กเกจเดสก์ท็อปChloros:

* `ChlorosLocal`, `process_folder` และ `process_lattice_capture` **ไม่** ทำงาน — พวกมันต้องการไฟล์ไบนารีแบ็กเอนด์ที่มาพร้อมกับตัวติดตั้งเดสก์ท็อป
* โปรแกรมช่วย smart-connect (`connect_camera`, `connect_array`, `connect_daq_sensor`) เป็นไคลเอนต์HTTP แบบบริสุทธิ์ ดังนั้นจึงทำงานได้กับแบ็กเอนด์บนเครื่องอื่น — แต่ backend ที่มาพร้อมตัวติดตั้งจะ bind เฉพาะ loopback เท่านั้น ดังนั้นคุณต้อง forward พอร์ตด้วยตัวเอง (เช่น `ssh -N -L 5000:127.0.0.1:5000 user@chloros-host`) และส่ง `backend_url="http://127.0.0.1:5000"` พร้อมกับ `auto_start_backend=False` ดู [Remote-Backend Mode](reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel).
* คลาส LATTICE ที่เชื่อมต่อกับฮาร์ดแวร์โดยตรง (`LatticeCamera`, `CameraPool`, …) สามารถนำเข้าได้ แต่จำเป็นต้องมี runtime Arena SDK จากแพ็กเกจเดสก์ท็อป — หากไม่มี `CAMERA_AVAILABLE` จะกลายเป็น `False`.
* เซ็นเซอร์ DAQ ถูกควบคุมผ่านแบ็กเอนด์ (แบบทูนเนล) เท่านั้น — `connect_daq_sensor()` หรือ `proj.sensors` ของโครงการ; แบ็กเอนด์จะเปิดเซ็นเซอร์บนเครื่องของตัวเอง

## ผู้ช่วย AI — `chloros-mcp`

SDK นี้รวมถึง `chloros-mcp` ซึ่งเป็นเซิร์ฟเวอร์ MCP ที่ช่วยให้ Claude Desktop, Claude Code, Cursor และไคลเอนต์ MCP อื่นๆ สามารถควบคุม Chloros ได้: ประมวลผลภาพ และทำงานกับกล้อง LATTICE, อาร์เรย์ และเซ็นเซอร์แสง DAQ เครื่องมือของมันครอบคลุมฟังก์ชันที่SDK นี้ทำ มันติดตั้งมาพร้อมกับSDK ดังนั้นการตั้งค่าสำหรับ Claude Code จึงใช้เพียงหนึ่งบรรทัด:

```bash
claude mcp add chloros -- chloros-mcp
```

ไคลเอนต์อื่น ๆ: [การใช้ Chloros กับผู้ช่วย AI](ai-assistants.md). ตัวเลือกและเครื่องมือทุกชนิด: [MCP Server (AI Assistants)](reference/sdk-reference.md#mcp-server-ai-assistants).

## ข้อกำหนดด้านใบอนุญาต

การเข้าถึง SDK จำเป็นต้องมีบัญชี Chloros+ ที่ใช้งานอยู่ ในระดับบริการแบบชำระเงินใดก็ตาม — **Copper หรือสูงกว่า**(Copper / Bronze / Silver / Gold); ระดับ Iron ฟรีไม่มีสิทธิ์เข้าถึง SDK/CLI การตรวจสอบสิทธิ์ดำเนินการ**ด้านเซิร์ฟเวอร์**: ทุกคำขอ SDK ต้องมีทั้งเซสชันที่ใช้งานอยู่และแผนบริการแบบชำระเงิน มิฉะนั้น ระบบหลังบ้านจะส่งคืนรหัสข้อผิดพลาด `403` / `PLAN_UPGRADE_REQUIRED` (ถูกส่งต่อเป็น `ChlorosLicenseError` โดย `ChlorosLocal` และเป็น `ChlorosConnectError` โดยตัวช่วย `connect_*`) ผู้เรียกที่ถูกออกจากระบบจะได้รับ `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) — การเรียกใช้ `chloros-cli login` อีกครั้งจะแก้ไขกรณีแรกได้ แต่ไม่แก้ไขกรณีที่สอง

การใช้งานแบบออฟไลน์ทำงานได้ภายในช่วงระยะเวลาผ่อนผันของแผน: ระดับการเข้าถึงจะถูกอ่านจากแคชการตรวจสอบความถูกต้องของเซิร์ฟเวอร์ (5 นาที) หรือแคชใบอนุญาตที่ลงนามและผูกกับเครื่อง (30 วันสำหรับแผนรายเดือน; จนถึงวันหมดอายุการสมัครสมาชิกสำหรับแผนรายปี) เมื่อช่วงผ่อนผันสิ้นสุดลง แผนจะเปลี่ยนเป็นเวอร์ชันฟรี และการเข้าถึงSDK จะหยุดลงจนกว่าเครื่องจะเชื่อมต่อกับเซิร์ฟเวอร์ได้หนึ่งครั้ง `chloros-cli status` ยังคงเข้าถึงได้ในระดับฟรี ดังนั้นเหตุผลจึงปรากฏอยู่เสมอ ดู [Chloros+ Login](chloros+-login.md).

## ข้อยกเว้น

จับคลาสพื้นฐานเพื่อจัดการกับ "ทุกปัญหาที่ Chloros อาจเกิดขึ้น":

```python
import chloros_sdk

try:
    chloros_sdk.process_folder("/path/to/folder")
except chloros_sdk.ChlorosAuthenticationError:
    print("Run `chloros-cli login` first.")
except chloros_sdk.ChlorosLicenseError:
    print("Chloros+ subscription required.")
except chloros_sdk.ChlorosError as e:
    print(f"Chloros error: {e}")
```

ทุกข้อยกเว้นใน pipeline (`ChlorosBackendError`, `ChlorosConnectionError`, `ChlorosLicenseError`, `ChlorosAuthenticationError`, `ChlorosConfigurationError`, `ChlorosProcessingError`) ล้วนมีต้นกำเนิดจาก `ChlorosError` ข้อควรระวังหนึ่ง: `ChlorosConnectError` — ถูกเรียกใช้เฉพาะโดย `connect_camera` / `connect_array` / `connect_daq_sensor` — มีต้นกำเนิดจาก `Exception` แบบธรรมดา **ไม่ใช่** จาก `ChlorosError` ดังนั้น `except ChlorosError` จึงไม่สามารถจับข้อผิดพลาดนี้ได้ โครงสร้างลำดับชั้นทั้งหมดมีอยู่ใน [SDK Reference](reference/sdk-reference.md#exceptions).

## ดูเพิ่มเติม

* [SDK Reference](reference/sdk-reference.md) — พื้นผิว API ทั้งหมด ที่ได้รับการปรับให้เหมาะสมสำหรับผู้ช่วย AI
* [คู่มือ CLI](reference/cli-reference.md) — ทุกคำสั่งย่อยของ CLI สะท้อนการเรียกใช้ SDK
* [การใช้Chloros กับผู้ช่วย AI](ai-assistants.md) — การตั้งค่า `chloros-mcp` และตัวอย่างคำสั่งพรอมต์
* [ดาวน์โหลด](download.md) — โปรแกรมติดตั้งสำหรับWindows และLinux
