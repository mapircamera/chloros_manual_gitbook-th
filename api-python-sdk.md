# API : Python SDK

{% hint style="info" %}
**กำลังหาเอกสารอ้างอิง API ที่ครบถ้วนอยู่ใช่ไหม?** หน้านี้คือคู่มือปฏิบัติจริง ทุกคลาส เมธอด ลายเซ็นที่ถูกต้อง และตัวอย่างที่สามารถคัดลอกและวางได้ ล้วนอยู่ใน [SDK Reference](reference/sdk-reference.md) ซึ่งได้รับการปรับให้เหมาะสมสำหรับผู้ช่วย AI**กำลังใช้ผู้ช่วย AI อยู่ใช่ไหม?** วางลิงก์นี้ URL ลงในแชท เพื่อให้ผู้ช่วย AI มีข้อมูล Chloros 1.2.0 API ที่ครบถ้วนและล่าสุด:

`https://mapir.gitbook.io/chloros/reference/sdk-reference.md`

ทุกหน้าของคู่มือนี้มีให้ในรูปแบบ markdown ดิบที่ slug ตัวพิมพ์เล็ก + `.md` และคู่มือทั้งหมดถูกจัดทำดัชนีที่ `https://mapir.gitbook.io/chloros/llms.txt`
{% endhint %}

**Chloros Python SDK** (`chloros-sdk` บน PyPI) เป็นตัวขับเคลื่อนทุกฟังก์ชันที่แอปเดสก์ท็อปสามารถทำได้จาก Python: การประมวลผลภาพแบบแบทช์, การควบคุมกล้อง LATTICE และอาร์เรย์แบบเรียลไทม์, เซสชันเซ็นเซอร์แสง DAQ, และการทำงานอัตโนมัติของโครงการที่บันทึกไว้ นี่เป็นชั้นบางๆ ที่อยู่เหนือแบ็กเอนด์ท้องถิ่นเดียวกันที่ GUI และ CLI ใช้ (HTTP บน `127.0.0.1:5000`) ดังนั้นพฤติกรรมจึงเหมือนกันในทั้งสามแพลตฟอร์ม

## การติดตั้ง

การติดตั้งมีสองขั้นตอน: ติดตั้งแพ็กเกจเดสก์ท็อป Chloros ก่อน (ซึ่งให้ระบบหลังบ้านสำหรับการประมวลผลและรันไทม์ฮาร์ดแวร์) แล้วติดตั้งแพ็กเกจ Python ต่อ

**ขั้นตอน 1 — ติดตั้ง Chloros** Windows: ติดตั้งโปรแกรมติดตั้งเดสก์ท็อป (เส้นทางเริ่มต้น `C:\Program Files\MAPIR\Chloros\`) จากหน้า [Download](download.md) Linux: ติดตั้งแพ็กเกจ `.deb` ([Linux Installation](linux/linux-installation.md)).**ขั้นตอนที่ 2 — ติดตั้ง SDK** (Python 3.7+):

```bash
pip install chloros-sdk
```

คุณอาจไม่จำเป็นต้องใช้ pip เลย: ทุกตัวติดตั้งมาพร้อมกับ wheel SDK ที่ตรงกัน ตัวติดตั้ง Windows จะติดตั้งมันลงในระบบของคุณโดยอัตโนมัติที่ Python; ส่วนตัวติดตั้ง Linux `.deb` จะวางมันไว้ที่ `/usr/lib/chloros/sdk/` และแสดงคำสั่ง `pip install --user` ที่ถูกต้อง PyPI จะได้รับการอัปเดตเมื่อมีการปล่อยเวอร์ชันใหม่ ดังนั้น `pip install chloros-sdk` จึงตรงกับเวอร์ชันเสถียรล่าสุด

**ขั้นตอนที่ 3 — ลงชื่อเข้าใช้ครั้งเดียวต่อเครื่อง:**

```bash
chloros-cli login user@example.com 'YourPassword'
```

ข้อมูลการเข้าสู่ระบบจะถูกเก็บไว้ในแคชที่ `~/.chloros/` (ทั้งสองแพลตฟอร์ม) บน Windows คุณสามารถเข้าสู่ระบบได้ผ่านแท็บ &quot;User<img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line">&quot; ของแอปเดสก์ท็อปได้เช่นกัน ส่วน SDK จำเป็นต้องมีแผน Chloros+ ที่ต้องชำระเงิน — ดู [ข้อกำหนดใบอนุญาต](#license-requirement) ด้านล่าง

| ข้อกำหนด | รายละเอียด |
| --- | --- |
| **ติดตั้ง Chloros** | Windows: ตัวติดตั้งสำหรับเดสก์ท็อป; Linux: แพ็กเกจ `.deb` (ให้ไฟล์ไบนารีของระบบหลังบ้าน) |
| **Python** | 3.7 หรือสูงกว่า (พัฒนา/ทดสอบบนเวอร์ชัน 3.10) |
| **ระบบปฏิบัติการ** | Windows 10/11 64-bit, Ubuntu 22.04 LTS หรือเวอร์ชันใหม่กว่า, หรือ NVIDIA Jetson (JetPack 6) |
| **ใบอนุญาต** | บัญชี Chloros+ ที่ใช้งานอยู่, ระดับบริการแบบชำระเงินใดก็ได้ (Copper หรือสูงกว่า) |

## ความสำเร็จใน 60 วินาที

เพียงเรียกใช้คำสั่งเดียว ก็สามารถสร้างโครงการ นำเข้าโฟลเดอร์ ตั้งค่าการประมวลผล และรัน pipeline — พร้อมทั้งเริ่มต้นระบบหลังบ้านอัตโนมัติหากยังไม่ได้ทำงานอยู่:

```python
import chloros_sdk

results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "NDRE", "GNDVI"],
)
```

(บน Linux ให้ใช้เส้นทาง Linux: `/home/user/drone_images/flight001`. SDK ทำงานเหมือนกันบนทั้งสองแพลตฟอร์ม.)

กำลังประมวลผลโฟลเดอร์การจับภาพ LATTICE? ใช้ wrapper ที่รองรับ LATTICE — มันจะตั้งค่าเริ่มต้นที่ถูกต้อง (ไม่ตรวจจับเป้าหมายของแผง, debayer มาตรฐาน):

```python
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)
```

## `ChlorosLocal` — การควบคุม pipeline อย่างเต็มรูปแบบ

สำหรับงานที่ซับซ้อนกว่าคำสั่งเดียว ให้ใช้ `ChlorosLocal` ซึ่งจะสร้าง backend (`auto_start_backend=True`) เมื่อใช้ครั้งแรก สร้างและกำหนดค่าโครงการ ติดตามความคืบหน้า และส่งรายงานสรุปหลังการประมวลผล

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
ควรใช้ `http://127.0.0.1:5000` ตามค่าเริ่มต้น แทนที่จะใช้ `localhost` — บน Windows, `localhost` จะถูกแปลงเป็น `::1` ก่อน และใช้เวลาราว 2 วินาทีต่อคำขอเมื่อใช้แบ็กเอนด์ที่รองรับเฉพาะ IPv4
{% endhint %}

ใช้มันเป็นผู้จัดการบริบท (context manager) เพื่อรับประกันการทำความสะอาด:

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "NDRE", "GNDVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

`configure()` รับคำสำคัญต่อไปนี้: `debayer`, `vignette_correction`, `reflectance_calibration`, `indices`, `export_format`, `ppk`, `daq_log_path`, `input_level`, `radiometric_output`, `array_alignment`, `array_alignment_crop`, `array_alignment_interpolation`, และ `custom_settings` ค่าหลัก:

```python
# export_format
"TIFF (16-bit)"           # default, recommended
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

เมื่อเสร็จสิ้น `process()` จะแนบสรุปการประมวลผลของ backend เป็น `result["summary"]` แต่ละรายการใน `summary["hints"]` เป็นประโยคเต็มเพื่ออธิบายสิ่งที่น่าสังเกต — เช่น ทำไมการรันจึงไม่สร้างผลลัพธ์ใดๆ — และทุกคำชี้แนะจะถูกส่งออกไปใหม่ในรูปแบบของ Python `UserWarning` ดังนั้นการรันที่ไม่มีผลลัพธ์จึงสามารถวินิจฉัยตัวเองได้ แม้คุณจะไม่ตรวจสอบ dict เลยก็ตาม:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

{% hint style="warning" %}
**`process()` ไม่ถูกเรียกใช้เมื่อการรันไม่สร้างภาพใดๆ** นี่คือจุดเดียวที่ SDK และ CLI มีความแตกต่างอย่างจงใจ: `chloros-cli process` ถือว่า &quot;มีการขอผลิตภัณฑ์ แต่ไม่มีการเขียนผลิตภัณฑ์ใด&quot; เป็นความล้มเหลวและออกค่าที่ไม่เป็นศูนย์ ในขณะที่ SDK กลับค่าปกติและรายงานสภาพดังกล่าวผ่าน `summary` / hints. หาก pipeline ของคุณควรหยุดเมื่อการรันไม่มีผลลัพธ์ ให้ตรวจสอบด้วยตัวเอง — ตรวจสอบ `summary` (หรือนับจำนวนไฟล์ในโฟลเดอร์โครงการ) แทนที่จะพึ่งพาข้อยกเว้น
{% endhint %}

## Smart Connect — ฮาร์ดแวร์แบบเรียลไทม์

มีตัวช่วยสามตัวที่เปิดเซสชันแบบถาวรในพูลฮาร์ดแวร์ของแบ็กเอนด์ — ซึ่งเป็นพูลเดียวกันที่ GUI ใช้ ดังนั้นสคริปต์SDKจึงสามารถทำงานร่วมกับแอปเดสก์ท็อปได้โดยไม่เกิดการแย่งชิงพอร์ตอนุกรมหรือแบนด์วิดท์เครือข่าย ทั้งสามตัวจะเริ่มต้นแบ็กเอนด์ท้องถิ่นโดยอัตโนมัติหากยังไม่มีตัวใดกำลังทำงานอยู่

### กล้อง LATTICE เดี่ยว — `connect_camera`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)   # microseconds, dB
    cam.capture("output/")
```

### ชุดกล้องที่ซิงโครไนซ์ — `connect_array`

`connect_array` เป็นจุดเริ่มต้นที่แนะนำสำหรับระบบกล้องหลายตัว มันทำงานตามขั้นตอน smart-prep เดียวกันกับ GUI: การวิเคราะห์เครือข่าย, การเลือกชั้นซิงค์อัตโนมัติ, การซิงค์เวลา PTP, การเลือกรูปแบบพิกเซลต่อกล้อง, การตั้งค่าเริ่มต้น AE และการเตรียมทริกเกอร์ GPIO **อุปกรณ์ซีเรียลตัวแรกคือมาสเตอร์** (เป็นอุปกรณ์ที่ส่งสัญญาณทริกเกอร์ฮาร์ดแวร์); ส่วนที่เหลือเป็นสเลฟ

```python
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")
```

เพิ่ม `smart=True` เข้าสู่การจับภาพแบบอาร์เรย์ใดก็ตาม เพื่อรอให้การปรับแสงอัตโนมัติ (AE) เสถียรในทุกกล้องก่อนที่จะทริกเกอร์ สำหรับโหมดการจับภาพ (Single / Continuous / Interval / Fastest), เครื่องบันทึก, burst-to-video และการจัดแนวอาร์เรย์ ดู [SDK Reference](reference/sdk-reference.md#synchronized-array--arraysession-smart-prep).

### เซ็นเซอร์แสง DAQ — `connect_daq_sensor`

หากไม่ระบุอาร์กิวเมนต์ `connect_daq_sensor()` จะตรวจจับโปรโตคอลการส่งข้อมูลโดยอัตโนมัติ (ลำดับความสำคัญ: Ethernet → BLE → USB):

```python
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

แต่ละเฟรมจะนำข้อมูล `spectrum` 135 จุด (W/m²/nm เมื่อได้รับการปรับเทียบ), ธง `is_saturated` และ CIE `x`, `y`, `z`. เพื่อกำหนดเซ็นเซอร์หรือช่องทางส่งข้อมูลเฉพาะ — ซึ่งเป็นตัวเลือกที่เชื่อถือได้บนเครื่องโฮสต์ที่มีอินเทอร์เฟซเครือข่ายหลายตัว ซึ่งการค้นพบอัตโนมัติของ Ethernet อาจไม่พบ DAQ-E ที่ทำงานปกติในครั้งแรก — ให้ส่งคำชี้แจงที่ชัดเจนหนึ่งข้อ:

```python
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")        # implies BLE
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")     # implies Ethernet
```

โปรดทราบว่าโปรไฟล์การปรับค่าขีดจำกัด (`cap_id`) **ไม่ใช่** ปุ่มปรับค่าขีดจำกัดแบบอัตโนมัติ (SDK) — ให้เลือกผ่าน `chloros-cli daq pool-connect --cap-id …` / `pool-set-cap` แทน

### โครงการที่บันทึกไว้ — `open_project`

โครงการที่บันทึกไว้ (Chloros) จะเก็บรักษาอุปกรณ์ที่เชื่อมต่อไว้ (`cameras.json` + `sensors.json` พร้อมกับ `project.json`), และ `chloros_sdk.open_project(path)` สามารถเชื่อมต่อทุกอย่างกลับเข้าพร้อมกันและควบคุมการจับภาพตามชื่ออุปกรณ์ ดู [Project Automation](reference/sdk-reference.md#project-automation--chlorosproject) ในเอกสารอ้างอิง

## ผลลัพธ์จากการติดตั้งด้วย pip เท่านั้น

ตรวจสอบธงความพร้อมใช้งานระดับโมดูลก่อนใช้พื้นผิวฮาร์ดแวร์:

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)    # True iff lattice_sdk imported cleanly
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)       # True iff daq_sdk imported cleanly
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)  # True iff ChlorosProject deps available
```

บนโฮสต์ที่มี **เพียง** `pip install chloros-sdk` และไม่มีแพ็กเกจเดสก์ท็อปChloros:

* `ChlorosLocal`, `process_folder` และ `process_lattice_capture` **ไม่** ทำงาน — พวกมันต้องการไฟล์ไบนารีแบ็กเอนด์ที่มาพร้อมกับตัวติดตั้งเดสก์ท็อป
* โปรแกรมช่วย smart-connect (`connect_camera`, `connect_array`, `connect_daq_sensor`) เป็นไคลเอนต์แบบบริสุทธิ์ของ HTTP ดังนั้นจึงทำงานได้กับแบ็กเอนด์บนเครื่องอื่น — แต่ backend ที่มาพร้อมกับโปรแกรมจะผูกกับ loopback เท่านั้น ดังนั้นคุณต้องส่งต่อพอร์ตด้วยตัวเอง (เช่น `ssh -N -L 5000:127.0.0.1:5000 user@chloros-host`) และส่ง `backend_url="http://127.0.0.1:5000"` พร้อมกับ `auto_start_backend=False` ดู [โหมดเซิร์ฟเวอร์หลังระยะไกล](reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel).
* คลาส LATTICE ที่เชื่อมต่อกับฮาร์ดแวร์โดยตรง (`LatticeCamera`, `CameraPool`, …) สามารถนำเข้าได้ แต่จำเป็นต้องมี runtime Arena SDK จากแพ็กเกจเดสก์ท็อป — หากไม่มี `CAMERA_AVAILABLE` จะกลายเป็น `False`.
* `daq_sdk` (คลาส DAQ แบบตรง) มาพร้อมกับติดตั้งบนเดสก์ท็อป ไม่ใช่แพ็กเกจ PyPI, ดังนั้น `DAQ_AVAILABLE` จะกลายเป็น `False` บนโฮสต์ที่ใช้ pip เท่านั้น — ให้ควบคุมเซ็นเซอร์ DAQ ผ่าน `connect_daq_sensor()` โดยเชื่อมต่อกับแบ็กเอนด์ (ผ่าน tunnel) แทน

## ข้อกำหนดด้านใบอนุญาต

การเข้าถึง SDK จำเป็นต้องมีบัญชี Chloros+ ที่ยังใช้งานได้บนแพ็กเกจแบบชำระเงินใดก็ตาม — **Copper หรือสูงกว่า**(Copper / Bronze / Silver / Gold); แพ็กเกจ Iron ฟรีไม่มีสิทธิ์เข้าถึง SDK / CLI การบังคับใช้นี้**ดำเนินการด้านเซิร์ฟเวอร์**: ทุกคำขอ SDK ต้องมีทั้งเซสชันที่ใช้งานอยู่และแพ็กเกจแบบชำระเงิน มิฉะนั้นระบบหลังบ้านจะส่งคืนรหัสข้อผิดพลาด `403` / `PLAN_UPGRADE_REQUIRED` (ถูกสร้างขึ้นเป็น `ChlorosLicenseError` โดย `ChlorosLocal` และเป็น `ChlorosConnectError` โดยตัวช่วย `connect_*`) ผู้เรียกที่ถูกออกจากระบบจะได้รับ `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) — การเรียกใช้ `chloros-cli login` อีกครั้งจะแก้ไขกรณีแรกได้ แต่ไม่แก้ไขกรณีที่สอง

การใช้งานแบบออฟไลน์ทำงานได้ภายในช่วงเวลาผ่อนผันของแผน: ระดับการเข้าถึงจะถูกอ่านจากแคชการตรวจสอบความถูกต้องของเซิร์ฟเวอร์ (5 นาที) หรือแคชใบอนุญาตที่ลงนามและผูกกับเครื่อง (30 วันสำหรับแผนรายเดือน; จนถึงวันหมดอายุการสมัครสมาชิกสำหรับแผนรายปี) เมื่อช่วงผ่อนผันหมดอายุ แผนจะเปลี่ยนเป็นเวอร์ชันฟรี และSDK จะหยุดทำงานจนกว่าเครื่องจะเชื่อมต่อกับเซิร์ฟเวอร์ได้หนึ่งครั้ง `chloros-cli status` ยังคงเข้าถึงได้ในระดับฟรี ดังนั้นเหตุผลจึงปรากฏอยู่เสมอ ดู [Chloros+ Login](chloros+-login.md).

## ข้อยกเว้น

จับคลาสพื้นฐานเพื่อจัดการกับ &quot;ปัญหาใดๆ ที่เกิดขึ้นใน Chloros&quot;:

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

ทุกข้อยกเว้นใน pipeline (`ChlorosBackendError`, `ChlorosConnectionError`, `ChlorosLicenseError`, `ChlorosAuthenticationError`, `ChlorosConfigurationError`, `ChlorosProcessingError`) ล้วนมีต้นกำเนิดจาก `ChlorosError`. ข้อควรระวัง: `ChlorosConnectError` — ถูกเรียกใช้เฉพาะโดย `connect_camera` / `connect_array` / `connect_daq_sensor` — มีต้นกำเนิดจาก `Exception` แบบธรรมดา **ไม่ใช่** จาก `ChlorosError` ดังนั้น `except ChlorosError` จึงไม่สามารถตรวจจับได้ ลำดับชั้นทั้งหมดอยู่ใน [SDK Reference](reference/sdk-reference.md#exceptions).

## ดูเพิ่มเติม

* [SDK Reference](reference/sdk-reference.md) — พื้นผิว API ที่สมบูรณ์ ซึ่งได้รับการปรับให้เหมาะสมสำหรับผู้ช่วย AI
* [CLI Reference](reference/cli-reference.md) — ทุกคำสั่งย่อยของ CLI สะท้อนการเรียกใช้ SDK
* [Download](download.md) — โปรแกรมติดตั้งสำหรับ Windows และ Linux.
