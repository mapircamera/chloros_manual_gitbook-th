# Chloros Python SDK ข้อมูลอ้างอิง

**เวอร์ชัน:**

1.2.0**สร้างขึ้น:**2026-07-29 19:19 ·**ปรับปรุง:** 2026-08-30**แพ็กเกจ:** `chloros-sdk` (PyPI)**กลุ่มเป้าหมาย:** ปรับให้เหมาะสมสำหรับการใช้งานกับ LLM; อ่านได้โดยมนุษย์**ขอบเขต:** ทุกคลาส ฟังก์ชัน และตัวช่วยสาธารณะที่ `import chloros_sdk` เปิดให้ใช้งาน พร้อมตัวอย่างที่สามารถคัดลอกและวางได้ ซึ่งครอบคลุมการประมวลผลภาพ การควบคุมกล้องเดี่ยว มصفوفاتที่ซิงโครไนซ์ เซ็นเซอร์ DAQ และการอัตโนมัติของโครงการ

หากคุณต้องการดูส่วนสำคัญเท่านั้น ให้ข้ามไปที่:
- [การติดตั้งและเริ่มต้นอย่างรวดเร็ว](#installation)
- [Smart-Connect สำหรับ LATTICE Arrays](#smart-connect-for-lattice-cameras)
- [เซสชันเซ็นเซอร์ DAQ](#daq-sensor-sessions)
- [การอัตโนมัติของโครงการ](#project-automation--chlorosproject)
- [Smart-AE / Smart-Capture](#smart-ae--smart-capture)

---

## สถาปัตยกรรมใน 60 วินาที

SDK เป็นชั้นบางๆ ของ Python ที่อยู่เหนือ backend ของ Chloros (เซิร์ฟเวอร์ Flask เดียวกันที่ GUI บนเดสก์ท็อปและ CLI ใช้) สำหรับการอัตโนมัติ คุณต้องนำเข้า `chloros_sdk` และเรียกเมธอดระดับสูง; ในเบื้องหลัง การเรียกทุกครั้งจะกลายเป็นคำขอ HTTP ไปยัง backend ท้องถิ่นที่พอร์ต 5000 — `http://127.0.0.1:5000/api/...` (ไม่ได้ใช้ `localhost` โดยเจตนา เนื่องจากชื่อนี้จะถูกแปลงเป็น `::1` ก่อนบน Windows และใช้เวลาราว 2 วินาทีต่อคำขอเมื่อส่งไปยังระบบหลังบ้านที่รองรับเฉพาะ IPv4 เท่านั้น) ระบบหลังบ้าน เป็นเจ้าของกลุ่มอุปกรณ์ — กล้อง, เซนเซอร์ DAQ, โปรไฟล์การจัดแนว, บัฟเฟอร์เฟรม — ดังนั้นสคริปต์ SDK จึงสามารถทำงานร่วมกับ GUI ได้โดยไม่จำเป็นต้องแข่งขันกันเพื่อใช้พอร์ตซีเรียลหรือแบนด์วิดท์ NIC

มีสามส่วนหลักที่คุณจะใช้:

1. **`ChlorosLocal` + ฟังก์ชันฟรี** (`process_folder`, `process_lattice_capture`) — ท่อประมวลผลภาพ (Image-processing pipeline) ดำเนินการกับโฟลเดอร์ทั้งชุดผ่านการปรับเทียบ / debayer / ส่งออกดัชนี (index export) จากการเรียกใช้ Python ครั้งเดียว
2. **Smart-connect handles** (`connect_camera`, `connect_array`, `connect_daq_sensor`) — เปิดเซสชันแบ็กเอนด์แบบคงที่สำหรับฮาร์ดแวร์แบบเรียลไทม์ ใช้ขั้นตอน &quot;smart-prep&quot; เดียวกันกับ GUI: การตรวจสอบเครือข่าย, การเลือกชั้นอัตโนมัติ, PTP, การตั้งค่าเริ่มต้น AE, การตั้งค่าทริกเกอร์ GPIO
3. **`ChlorosProject` / `open_project`** — โหลดโครงการที่บันทึกไว้ (โฟลเดอร์ที่มี `cameras.json` + `sensors.json` + `project.json`), เชื่อมต่อทุกสิ่งพร้อมกัน และบันทึกข้อมูลการจับภาพด้วย handle ที่มีชื่อ

Surfaces 1 และ 2 **จะเริ่มต้น backend ท้องถิ่นโดยอัตโนมัติ** หากยังไม่มี backend ที่กำลังรับฟังอยู่ (เป็นไฟล์ไบนารีเดียวกันที่รวมอยู่ในชุดกับ GUI/CLI ) — ดังนั้นสคริปต์เปล่าจึงทำงานได้จากเชลล์ใหม่โดยไม่ต้องเริ่มต้นแบ็กเอนด์ก่อน ให้ส่งผ่าน `auto_start_backend=False` เพื่อปิดการใช้งาน (เช่น เมื่อชี้ไปยังแบ็กเอนด์ระยะไกล ซึ่งไม่เคยถูกสร้างขึ้น) ดู [การเริ่มต้นระบบหลังแบบอัตโนมัติ](#backend-auto-start). Surface 3 มีพฤติกรรมที่ต่างออกไป: `open_project()` ไม่รับพารามิเตอร์ `auto_start_backend`, และ `connect_all()` ไม่เคยสร้าง backend — มันจะตรวจสอบ `http://127.0.0.1:5000` ครั้งเดียว และหากไม่มีอะไรตอบกลับ ก็จะกลับสู่การควบคุมอุปกรณ์โดยตรง (backend-free) `lattice_sdk` device control. มีเพียง `proj.process()` และ `stream(..., overlays=True)` เท่านั้นที่สร้าง `ChlorosLocal()` (ซึ่งจะเริ่มต้นอัตโนมัติ)

ทั้งสามตัวนี้ล้วนต้องผ่านการตรวจสอบสิทธิ์: 実行 `chloros-cli login` บนเครื่องหนึ่งครั้ง หรือลงชื่อเข้าใช้ผ่าน GUI บนเดสก์ท็อป การเรียกใช้ SDK โดยไม่มีเซสชันที่ถูกต้องจะก่อให้เกิดข้อผิดพลาด `ChlorosAuthenticationError`

ข้อกำหนด:
- Python 3.7+ (ตามที่ระบุในแพ็กเกจ; พัฒนา/ทดสอบบนเวอร์ชัน 3.10)
- Chloros Desktop ติดตั้งไว้บนเครื่อง (ไฟล์ไบนารีของ backend อยู่ในตัวติดตั้ง)
- บัญชี Chloros+ ที่ใช้งานอยู่. ระดับ SDK / CLI ต้องเป็น **Copper**หรือสูงกว่า (Copper / Bronze / Silver / Gold); ระดับ**Iron**ฟรีไม่มีสิทธิ์เข้าถึง SDK / CLI การบังคับใช้นี้ดำเนินการ**ทางฝั่งเซิร์ฟเวอร์**: ทุกคำขอที่มีแฟล็ก SDK / CLI ต้องมีทั้งเซสชันที่ใช้งานอยู่และแผนบริการแบบชำระเงิน มิฉะนั้นระบบหลังบ้านจะส่งคืน `403` พร้อมกับ `error_code: PLAN_UPGRADE_REQUIRED` (แสดงเป็น `ChlorosLicenseError` โดย `ChlorosLocal` และแสดงเป็น `ChlorosConnectError` โดย `connect_*` helpers) ผู้เรียกที่ออกจากระบบจะได้รับ `401` / `AUTH_REQUIRED` (`ChlorosAuthenticationError`) แทน — ทั้งสองนี้แตกต่างกันเพราะการเรียกใช้ `chloros-cli login` อีกครั้งจะแก้ไขปัญหาที่แรกได้ แต่ไม่สามารถแก้ไขปัญหาที่สองได้
- การใช้งานแบบออฟไลน์ได้รับการสนับสนุนภายในช่วงระยะเวลาผ่อนผันของแพ็กเกจ: ระดับแพ็กเกจจะถูกอ่านจากแคชการตรวจสอบความถูกต้องของเซิร์ฟเวอร์ (5 นาที) หรือแคชใบอนุญาตที่ลงนามและผูกกับเครื่อง (30 วันสำหรับแผนรายเดือน, จนถึงวันหมดอายุการสมัครสมาชิกสำหรับแผนรายปี) เมื่อช่วงผ่อนผันนี้หมดอายุ แผนจะเปลี่ยนเป็นแผนฟรี และการเข้าถึง SDK / CLI จะหยุดลงจนกว่าเครื่องจะสามารถเชื่อมต่อกับเซิร์ฟเวอร์ได้อีกครั้ง `chloros-cli status` (`GET /api/license-status`) ยังคงเข้าถึงได้ในระดับฟรี ดังนั้นจึงสามารถเห็นเหตุผลได้ — นี่เป็นเส้นทางเดียว SDK / CLI ที่ได้รับการยกเว้นจากข้อจำกัดระดับบริการ
- Windows 10/11 64-bit, **Ubuntu 22.04 LTS หรือเวอร์ชันใหม่กว่า**, หรือ Jetson (JetPack 6). Ubuntu 20.04**ไม่**ได้รับการสนับสนุน: ความพึ่งพาของ `.deb` มาจากสิ่งที่ระบบหลังบ้าน (backend) เชื่อมโยงด้วย รวมถึง `libc6 (>= 2.34)` และ focal มาพร้อมกับ glibc 2.31.

---

## การติดตั้ง

Python SDK เป็นชั้นบางๆ Python ที่อยู่เหนือ backend Chloros สำหรับทุกงานนอกเหนือจาก workflow ที่ใช้ DAQ เพียงไม่กี่แบบ คุณจำเป็นต้อง **ติดตั้งแพ็กเกจเดสก์ท็อป Chloros ท้องถิ่น** (ตัวติดตั้ง Windows หรือ Linux `.deb`) — ซึ่งจะเป็นตัวที่จัดหาไฟล์ไบนารีของ backend, สภาพแวดล้อมการทำงาน Arena SDK สำหรับกล้อง LATTICE และชุดข้อมูลการปรับเทียบ

ไฟล์ดาวน์โหลดล่าสุด: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

### ขั้นตอนที่ 1 — ติดตั้งแพ็กเกจแพลตฟอร์ม Chloros

#### Windows (.exe)

1. ดาวน์โหลด `Chloros-Setup-x.y.z.exe` จากหน้าดาวน์โหลด
2. เปิดโปรแกรมติดตั้งและทำตามขั้นตอนของตัวช่วยติดตั้ง เส้นทางติดตั้งตามค่าเริ่มต้นคือ `C:\Program Files\MAPIR\Chloros\`
3. เปิด Chloros อย่างน้อยหนึ่งครั้ง และลงชื่อเข้าใช้ด้วยบัญชี Chloros+ ของคุณ

#### Linux amd64 (.deb)

```bash
sudo dpkg -i chloros-amd64.deb
sudo apt-get install -f         # only if dpkg reports missing dependencies
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

#### Linux arm64 — Jetson (JetPack 6)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
sudo apt-get install -f
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

### ขั้นตอนที่ 2 — ติดตั้ง Python SDK

**ตัวติดตั้ง Chloros มาพร้อมกับ wheel SDK ที่ตรงกัน** ตัวติดตั้ง Windows และ .deb Linux ทุกตัวจะวาง `chloros_sdk-X.Y.Z-py3-none-any.whl` ลงบนดิสก์ ซึ่งตรงกับเวอร์ชัน GUI / CLI / backend อย่างแม่นยำ คุณไม่จำเป็นต้องติดตาม PyPI เพื่อให้ระบบยังคงสอดคล้องกัน

#### Windows

โปรแกรมติดตั้งจะรัน `pip install` โดยอัตโนมัติกับไฟล์ wheel ที่มาพร้อมแพ็กเกจ โดยใช้ Python ของระบบคุณ (แนะนำให้ใช้ตัวเปิด `py.exe` แต่หากไม่ทำงานจะใช้ `python -m pip` แทน) ไม่จำเป็นต้องดำเนินการใดๆ — `import chloros_sdk` จะทำงานในสภาพแวดล้อม Python ของคุณหลังจากติดตั้งสำเร็จ หากไม่มี Python บนเครื่อง โปรแกรมติดตั้งจะข้ามขั้นตอนนี้ไปโดยอัตโนมัติ และ GUI + CLI จะยังคงทำงานได้

#### Linux (.deb)

ไฟล์ .deb จะวาง wheel ที่ `/usr/lib/chloros/sdk/`. `postinst` จะพิมพ์คำสั่งที่ถูกต้อง — ระบบปฏิบัติการตาม PEP 668 ปฏิเสธการเขียน pip แบบ global ตามค่าเริ่มต้น ดังนั้นเราจึงไม่ติดตั้งอัตโนมัติ:

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

สำหรับการติดตั้ง Jetson แบบ air-gapped นี่เป็นการทำงานแบบออฟไลน์อย่างสมบูรณ์ — wheel อยู่บนดิสก์แล้ว

#### PyPI สาธารณะ

สำหรับโฮสต์ที่ใช้ pip เท่านั้น (ไม่ติดตั้งแพ็กเกจเดสก์ท็อป Chloros; กระบวนการทำงานแบบ remote-backend หรือ DAQ เท่านั้น):

```bash
pip install chloros-sdk
```

PyPI จะได้รับการอัปเดตในเวอร์ชันติดตั้งที่ออกเป็นรุ่นปล่อย ดังนั้น wheel ที่เผยแพร่จะตรงกับรุ่นเสถียรล่าสุด ส่วนเวอร์ชันพัฒนา (เช่น `1.1.4.dev1`) จะถูกส่งผ่านไฟล์ wheel ที่มาพร้อมกับตัวติดตั้งเท่านั้น

#### ตรวจสอบ

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)
```

> **Chloros+ ต้องสมัครสมาชิก** การเรียกใช้ SDK ทุกครั้งต้องมีการเข้าสู่ระบบ Chloros+ ที่ยังใช้งานได้ ให้เรียกใช้ `chloros-cli login user@example.com 'YourPassword'` ครั้งเดียวต่อเครื่อง; ข้อมูลการเข้าสู่ระบบจะถูกเก็บไว้ในแคชของ `~/.chloros/`.

### ฉันจำเป็นต้องใช้แพ็กเกจ Desktop หรือไม่?

แพ็กเกจ pip เพียงอย่างเดียว **ไม่** เพียงพอสำหรับกระบวนการทำงานส่วนใหญ่ นี่คือสิ่งที่แต่ละพื้นผิว SDK ต้องการ:

| พื้นผิว SDK | จำเป็นต้องใช้แพ็กเกจ Desktop หรือไม่? | เหตุผล |
| --- | --- | --- |
| `ChlorosLocal`, `process_folder`, `process_lattice_capture` | **ใช่** | เริ่มต้นไฟล์ไบนารีแบ็กเอนด์อัตโนมัติที่ `/usr/lib/chloros/chloros-backend` (Linux) หรือ `C:\Program Files\MAPIR\Chloros\…` (Windows) |
| `connect_camera`, `connect_array`, `connect_daq_sensor`, `analyze_array_network`, `list_*`, `discover_*` | **ใช่**(ท้องถิ่น)**/ ไม่**(ระยะไกล) | ลูกค้า PureHTTP ผ่านแบ็กเอนด์ แบ็กเอนด์ท้องถิ่น → ต้องมีแพ็กเกจเดสก์ท็อป แบ็กเอนด์ระยะไกล → `backend_url=`**ผ่านอุโมงค์** (ดู โหมดแบ็กเอนด์ระยะไกล — แบ็กเอนด์ที่จัดส่งมาจะผูกกับ loopback เท่านั้น) |
| `ChlorosProject` / `open_project` | **ใช่** | ควบคุมโครงการที่บันทึกไว้ผ่านระบบแบ็กเอนด์ |
| คลาส LATTICE แบบตรง (`LatticeCamera`, `CameraPool`, `Calibration`, `DLS`, …) | **ใช่** | ต้องใช้ runtime ดั้งเดิมของ Arena SDK ที่มาพร้อมกับแพ็กเกจเดสก์ท็อป `CAMERA_AVAILABLE` จะถูกแปลงเป็น `False` เมื่อนำเข้า |
| คลาส DAQ โดยตรง (`DAQUSensor`, `DAQMSensor`, `DAQESensor`, `SensorFleet`, `discover_all`) | **ไม่** | Python แบบบริสุทธิ์ผ่าน pyserial/bleak/zeroconf สภาพแวดล้อมที่ใช้ pip เท่านั้นสามารถควบคุม DAQ ได้แบบ end-to-end |

### โหมด Remote-Backend (โฮสต์ที่ใช้ pip เท่านั้น ผ่าน tunnel)

> **แบ็กเอนด์ที่มาพร้อมระบบไม่สามารถเข้าถึงได้ผ่าน LAN** เวอร์ชัน
> สำหรับการใช้งานจริงจะผูกกับ loopback เท่านั้น (ทั้งสองตระกูล loopback) และปฏิเสธอย่างเด็ดขาด
> โหมดที่ไม่ใช่ loopback เพียงอย่างเดียว (`CHLOROS_CLOUD_MODE`) ดังนั้น
> `backend_url="http://<lan-ip>:5000"` **ไม่สามารถทำงานกับ
> Chloros ที่ติดตั้งไว้** — รูปแบบนี้เคยทำงานได้เฉพาะกับ backend แบบ source/dev
> เท่านั้น เพื่อควบคุม backend บนเครื่องอื่น ให้ส่งต่อพอร์ต loopback
> ของมันด้วยตนเอง และชี้ SDK ไปยัง tunnel:

```bash
# on the pip-only host: forward local 5000 to the Chloros machine's loopback
ssh -N -L 5000:127.0.0.1:5000 user@chloros-host
```

```python
import chloros_sdk

BACKEND = "http://127.0.0.1:5000"   # the tunnel endpoint

chloros_sdk.connect_camera("213800234", backend_url=BACKEND)
chloros_sdk.connect_array(serials, backend_url=BACKEND)
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local", backend_url=BACKEND)
```

เครื่องโฮสต์แบบ Headless / CI / robotics สามารถใช้เครื่องหนึ่งที่มีระบบเดสก์ท็อปติดตั้งเต็มรูปแบบเป็น &quot;Chloros server&quot; และ `pip install chloros-sdk` ที่ทุกที่อื่น — แต่การส่งข้อมูลระหว่างทั้งสองเครื่องคืออุโมงค์ที่ผู้ใช้จัดเตรียมไว้ข้างต้น ไม่ใช่การเชื่อมต่อ LAN URL แบบตรง

> **ข้อจำกัดที่ทราบ — `ChlorosLocal` ไม่รองรับการทำงานผ่าน pip เท่านั้น** `ChlorosLocal(backend_url=BACKEND)` ปัจจุบันจะกำหนดค่าไฟล์ไบนารีของ backend ท้องถิ่นในตัวสร้าง (constructor) *ก่อน* ที่จะตรวจสอบ URL และจะส่งข้อผิดพลาด `ChlorosBackendError` (&quot;ไม่พบ backend Chloros…&quot;) เมื่อไม่มีแพ็กเกจเดสก์ท็อปติดตั้ง — แม้จะมี backend ระยะไกลที่สามารถเข้าถึงได้ก็ตาม เฉพาะส่วน smart-connect ด้านบนเท่านั้น (`connect_camera` / `connect_array` / `connect_daq_sensor`, รวมถึง `analyze_array_network` และ `list_*` / `discover_*`) เท่านั้นที่ทำงานได้จากโฮสต์ที่ใช้ pip เท่านั้น

### กระบวนการทำงานเฉพาะ DAQ (โฮสต์ที่ใช้ pip เท่านั้น)

หากคุณต้องการใช้เซ็นเซอร์ DAQ เท่านั้น และไม่ใช้กล้อง LATTICE หรือการประมวลผลภาพ แพ็กเกจ pip เป็นระบบที่ครบถ้วนในตัวเอง:

```bash
pip install chloros-sdk
```

```python
from chloros_sdk import DAQUSensor, DAQMSensor, DAQESensor, discover_all

for d in discover_all(timeout=3.0):
    print(d.model, d.display, d.address)   # USB serials: d.extra.get("serial_number")

sensor = DAQUSensor(port="/dev/ttyUSB0")
sensor.connect()
sensor.start_streaming()
```

ไม่จำเป็นต้องใช้ backend, .deb หรือเข้าสู่ระบบ Chloros+ สำหรับการทำงาน DAQ ที่เชื่อมต่อกับฮาร์ดแวร์โดยตรง

---

## เริ่มต้นอย่างรวดเร็ว

```python
import chloros_sdk

# === Image processing ===
results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "NDRE", "GNDVI"],
)

# === Live LATTICE single-cam ===
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)
    cam.capture("output/")

# === Live LATTICE synchronized array (GUI smart-prep flow) ===
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")

# === Live DAQ spectral sensor ===
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])

# === Drive a saved project end-to-end ===
proj = chloros_sdk.open_project("/path/to/project")
proj.connect_all()
proj.arrays["main_rig"].capture("output/", processing="reflectance")
proj.disconnect_all()
```

---

## ดัชนีระดับบนสุดของ API

```python
import chloros_sdk

# === Image processing (full pipeline) ===
chloros_sdk.ChlorosLocal                          # class
chloros_sdk.process_folder(...)                   # one-shot helper
chloros_sdk.process_lattice_capture(...)          # LATTICE-friendly defaults
chloros_sdk.read_image_audit_tags(path)           # post-run audit

# === Live cameras (persistent backend pool) ===
chloros_sdk.connect_camera(serial, ...)           # → CameraSession
chloros_sdk.connect_array(serials, ...)           # → ArraySession (smart-prep)
chloros_sdk.attach_array(serials_or_id, ...)      # → ArraySession (attach without re-connecting)
chloros_sdk.list_cameras()
chloros_sdk.list_arrays()
chloros_sdk.discover_lattice_cameras()
chloros_sdk.analyze_array_network(...)            # network capability + recommendation
chloros_sdk.CaptureResult                         # list subclass returned by ArraySession.capture
chloros_sdk.RecorderHandle                        # handle for an array record()/burst() job

# === Live DAQ sensors (persistent backend pool) ===
chloros_sdk.connect_daq_sensor(...)               # → DAQSensorSession
chloros_sdk.discover_daq_sensors()                # scan USB/BLE/ETH (finds a DAQ-M MAC)
chloros_sdk.list_daq_sensors()

# === Project lifecycle ===
chloros_sdk.open_project(path)                    # → ChlorosProject
chloros_sdk.ChlorosProject                        # class
chloros_sdk.AlignmentSpec                         # dataclass
chloros_sdk.ArrayHandle, CameraHandle, SensorHandle

# === Direct-hardware (no-backend) classes (from lattice_sdk / daq_sdk) ===
chloros_sdk.LatticeCamera, CameraSettings, PRESETS, CameraPool
chloros_sdk.Calibration, CalibrationCoefficients, FilterModel, list_filters
chloros_sdk.DLS, NetworkDiagnostics
chloros_sdk.DAQUSensor, DAQMSensor, DAQESensor, SensorFleet, discover_all

# === Exceptions ===
chloros_sdk.ChlorosError                          # base
chloros_sdk.ChlorosBackendError
chloros_sdk.ChlorosLicenseError
chloros_sdk.ChlorosConnectionError
chloros_sdk.ChlorosProcessingError
chloros_sdk.ChlorosAuthenticationError
chloros_sdk.ChlorosConfigurationError
chloros_sdk.ChlorosConnectError                   # raised by smart-connect surface
chloros_sdk.LatticeError, CameraNotFoundError, ...  # from lattice_sdk

# === Availability flags ===
chloros_sdk.CAMERA_AVAILABLE     # True iff lattice_sdk imported cleanly
chloros_sdk.DAQ_AVAILABLE        # True iff daq_sdk imported cleanly
chloros_sdk.PROJECT_AVAILABLE    # True iff ChlorosProject deps available
```

---

## การประมวลผลภาพ — `ChlorosLocal`

คลาส pipeline หลัก สร้าง backend เมื่อใช้ครั้งแรก สร้าง/กำหนดค่าโครงการ ติดตามความคืบหน้า และส่งคืนสรุปผลหลังการรัน

### คอนสตรัคเตอร์

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

### เมธอด

| เมธอด | คำอธิบาย |
| --- | --- |
| `create_project(project_name, camera=None)` | สร้างโครงการใหม่ (สามารถใช้แม่แบบกล้องได้ เช่น `"Survey3N_RGN"`) |
| `import_images(folder_path, recursive=False)` | นำเข้าภาพ RAW/TIF/JPG/DNG **และข้อมูลบันทึกจากเซ็นเซอร์แสง `.daq`**. ส่งคืน `count` (ภาพ) และ `scan_count` (ข้อมูลบันทึก). แจ้งเตือนเฉพาะเมื่อโฟลเดอร์ไม่มีทั้งสอง |
| `export_light_sensor(daq=True, csv=True)` | เขียนค่า `.daq` + `.csv` ที่ได้รับการปรับเทียบสำหรับทุกการบันทึกของเซ็นเซอร์แสงในโครงการ ลงใน `<project>/Light Sensor/` ดู [การบันทึกจากเซ็นเซอร์แสง](#light-sensor-recordings--calibrated-daq--csv). |
| `configure(debayer=..., vignette_correction=..., reflectance_calibration=..., indices=[...], export_format=..., ppk=..., daq_log_path=..., input_level=..., radiometric_output=..., array_alignment=..., array_alignment_crop=..., array_alignment_interpolation=..., custom_settings=None)` | ตั้งค่าปุ่มปรับการประมวลผล |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | ดำเนินการตาม pipeline. คืนค่า `{"status": "complete", "async": False}` พร้อมด้วยคีย์ `summary` เมื่อระบบหลัง (backend) ให้คีย์ดังกล่าว — ดู [สรุปหลังการดำเนินการและคำแนะนำ](#post-run-summary--hints). |
| `get_config()` / `get_status()` / `status()` | ตรวจสอบสถานะของ backend |
| `logout()` | ลบข้อมูลรับรองที่เก็บไว้ในแคช |
| `shutdown_backend()` | ยุติการทำงานของ backend (หาก SDK -started) |
| `discover_cameras()` | ค้นหากล้อง LATTICE **ผ่าน backend ของอินสแตนซ์นี้** (`/api/camera/discover`). คืนค่าเป็นรายการของ dicts (`serial`, `model`, `ip`, …) — มีรูปแบบเดียวกันกับที่ GUI/CLIเห็น หากไม่พบกล้องใดหรือไม่สามารถเข้าถึง backend ได้ จะคืนค่าเป็นรายการว่าง |
| `camera_capture(output_dir, format="tiff", **settings)` | จับภาพเฟรมเดียว**ผ่านแบ็กเอนด์**(เริ่มต้นอัตโนมัติโดยแฮนเดิลนี้) เพื่อให้ได้รับการเตรียมการแบบเดียวกับที่ GUI/CLI เห็น (ค่าเริ่มต้น 12 บิต, ใช้ซ้ำจากพูล, ข้อมูลเมตาข้อมูล cal ที่ฝังตัว). กำหนดเป้าหมายด้วย `serial=` หรือ `device_index=`; ส่ง `exposure`/`gain`/`pixel_format`/`preset` เป็น `**settings`. คืนค่าพจนานุกรมข้อมูลเมตาดาต้าแบบเดิม (`filepath`, `width`, `height`, `pixel_format`, `exposure_time`, `gain`, `timestamp`). |
| `camera_stream(serial, *, fps=10.0, overlay=None, decode=True, connect_timeout=10.0, read_timeout=15.0)` | สร้างเฟรมตัวอย่างที่รวมทับซ้อนจากกล้องที่รวมกลุ่ม — ลูกค้า MJPEG แบบบางผ่านแบ็กเอนด์`/api/camera/<serial>/stream-annotated` (ลายม้าลาย / ตาราง / เส้นกากบาท / ฮิสโตแกรม / การแสดงจุดสูงสุด / จุดที่วาดจากฝั่งเซิร์ฟเวอร์). `decode=True` ให้อาร์เรย์ BGR; `False` ให้ไบต์ดิบJPEG. สามารถเข้าถึงได้ตามโครงการเป็น `ChlorosProject.stream(overlays=True)` |

ใช้ในฐานะผู้จัดการบริบทเพื่อรับประกันการทำความสะอาด:

```python
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

### การบันทึกข้อมูลจากเซ็นเซอร์แสง — `.daq` + `.csv` ที่ได้รับการปรับเทียบ

สามารถบันทึกข้อมูลจาก DAQ-U / DAQ-M / DAQ-E **โดยไม่ต้อง**ใช้ชุดข้อมูลการปรับเทียบ นั่นคือ
สิ่งที่เครื่องบันทึก [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts)
(`record_daq.py`) ทำตามค่าเริ่มต้น: มันเขียนค่าการนับของเซ็นเซอร์แบบดิบและประทับเวลา
ในไฟล์เพื่อให้ Chloros ดึงข้อมูลการปรับเทียบจากโรงงานของเซ็นเซอร์นั้น **ตามหมายเลขซีเรียล** — แคชท้องถิ่น
ก่อน แล้วจึงส่งไปยัง MAPIR Cloud — และนำไปใช้เมื่อนำเข้า

Chloros ส่งผลลัพธ์กลับออกมาเป็นสองผลิตภัณฑ์ต่อการบันทึก ภายใต้
`<project>/Light Sensor/`:

| ผลิตภัณฑ์ | อธิบาย |
| --- | --- |
| `<name>_calibrated.daq` | ไฟล์เก็บถาวรที่สามารถประมวลผลใหม่ได้ — มีโครงสร้างข้อมูลเหมือนกับการบันทึกแบบเรียลไทม์ แต่ตอนนี้ระบุชุดข้อมูลที่สร้างมันขึ้นมา การนำเข้าใหม่จะไม่ **ไม่** ทำให้ต้องปรับเทียบใหม่ |
| `<name>_calibrated.csv` | ความเข้มรังสีสเปกตรัมในหน่วย W/m²/nm บนกริดความยาวคลื่นของเซ็นเซอร์เอง หนึ่งแถวต่อการอ่านหนึ่งครั้ง พร้อมด้วยคอลัมน์โฟโตเมตริก (กำลังรวม, lux แบบ photopic/scotopic, PPFD และการแบ่งสีฟ้า/เขียว/แดง, ความยาวคลื่นสูงสุด). |
| `<name>_raw.daq` / `<name>_raw.csv` | **เฉพาะเซ็นเซอร์ที่ไม่มีชุดข้อมูลเท่านั้น (DAQ-A).** ค่าการนับสเปกตรัมดิบของเซ็นเซอร์ — *ไม่ใช่* ความเข้มรังสี ดูด้านล่าง |

`process()` ดำเนินการส่งออกนี้ในฐานะหนึ่งในขั้นตอนของมัน มัน **ไม่** ไม่ต้องการภาพ:
เซ็นเซอร์แสงที่บินด้วยตัวเองเป็นกระบวนการทำงานหลัก และโครงการดังกล่าวมีภาพเป็นศูนย์
ตามการออกแบบ

**การบันทึกของ DAQ-A ส่งออกเป็นค่าการนับดิบ** ครอบครัว DAQ-A มีอยู่ก่อนระบบบันเดิลตามหมายเลขซีเรียล
และไม่มีบันเดิลใดที่จะดึงมา — มันถูกปรับเทียบในสนามกับ
เป้าหมายการสะท้อนแสงแทน ซึ่งนี่คือเหตุผลที่มันไม่เคยต้องการบันเดิลเลย การบันทึกเหล่านั้นจะถูกส่งออก
ภายใต้รากชื่อไฟล์ `_raw` แทนที่จะเป็น `_calibrated`: ใช้ชื่อไฟล์ที่ต่างกันแทนที่จะใช้แฟล็ก
ภายในไฟล์ เนื่องจากข้อมูลต้องคงอยู่เมื่อส่งต่อทางอีเมลด้วยชื่อไฟล์เปล่าๆ ส่วน
ส่วนหัว `.csv` ระบุ `raw spectral sensor counts (NOT irradiance)` และเตือนว่า
ค่าต่างๆ สามารถเปรียบเทียบได้ **ภายใน** ไฟล์ — ซึ่งเป็นสิ่งที่การปรับเทียบตามเป้าหมายใช้
ใช้ค่าเหล่านั้น — และไม่ใช่ระหว่างเซ็นเซอร์ต่าง ๆ คอลัมน์โฟโตเมตริกที่ขึ้นอยู่กับกำลัง (กำลังรวม,
ลักซ์แบบโฟโตปิก/สโคโตปิก, PPFD) จะคืนค่า **NULL** แทนที่จะถูกรวมจากจำนวนนับ

DAQ-U / DAQ-M / DAQ-E ที่ไม่สามารถดึงชุดข้อมูล (bundle) ได้จะยังคงถูก **ข้าม** ไป
แทนที่จะเขียนข้อมูลดิบ: ในกรณีนี้ ชุดข้อมูลมีอยู่จริง และคำแนะนำ &quot;เชื่อมต่อใหม่และประมวลผลใหม่&quot; เป็นคำแนะนำที่ถูกต้อง

**v1.01 / v1.02** (ที่ DAQ-A-SD บันทึกไว้) ไม่มีการระบุ epoch สำหรับแต่ละการอ่าน
มีเพียงเวลาเขียนของไฟล์เท่านั้น ระบบจับคู่ภาพ↔แสงลงยังคงปฏิเสธข้อมูลเหล่านี้ — การจับคู่
เฟรมกับเวลาเขียนจะผิดพลาดโดยที่ผู้ใช้ไม่สังเกตเห็น — แต่ตัวส่งออกสามารถอ่านข้อมูลเหล่านี้ได้ และ
CSV จะพิมพ์ `clock=daq_created_on` เพื่อให้ผลิตภัณฑ์ระบุได้ว่ากำลังใช้ clock ใด

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("DAQ-U_2026-08-26")
    cl.import_images("C:/Flights/raw_daq")     # .daq only — no camera involved
    result = cl.export_light_sensor()          # or just cl.process()

for rec in result["exported"]:
    print(rec["csv"])
for rec in result["skipped"]:
    print("skipped", rec["source"], "--", rec["reason"])
```

การบันทึกที่ชุดข้อมูลการปรับเทียบไม่สามารถดึงมาได้ (อยู่ในโหมดออฟไลน์ หรือเซ็นเซอร์ที่ไม่มี
ข้อมูลการปรับเทียบในไฟล์) จะถูกรายงานภายใต้ `skipped` **พร้อมเหตุผล** ไฟล์นี้จะไม่
ถูกบันทึกเป็นไฟล์ &quot;ที่ผ่านการปรับเทียบ&quot; ซึ่งเก็บค่าการนับดิบ — ให้เชื่อมต่ออินเทอร์เน็ตและ
รันใหม่ แล้วการส่งออกจะเสร็จสิ้น

### การเรียกกลับความคืบหน้า

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### สรุปและคำแนะนำหลังการรัน

เมื่อเสร็จสิ้น `process()` จะเรียกข้อมูล `GET /api/processing-summary` และแนบเนื้อหาเป็น `result["summary"]` การเรียกข้อมูลนี้ทำตามความพยายามสูงสุดและไม่เคยขัดขวางการส่งคืนที่สำเร็จ — หากสรุปข้อมูลไม่มีอยู่ `process()` จะกลับสู่รูปแบบ `{"status": "complete", "async": False}` แบบธรรมดา แต่ละรายการใน `summary["hints"]` — ซึ่งเป็นประโยคเต็มพร้อมคำแนะนำการแก้ไข เช่น เหตุผลที่การรันให้ผลลัพธ์เป็นศูนย์ — จะถูกส่งออกมาเป็น-ถูกส่งออกเป็นรูปแบบ &quot;Python&quot; `UserWarning` ดังนั้นการรันที่ให้ผลลัพธ์เป็นศูนย์จึงสามารถวินิจฉัยตัวเองได้ แม้คุณจะไม่ตรวจสอบ dict เลยก็ตาม:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

`summary["totals"]` เป็นส่วนที่เครื่องอ่านได้:

| Key | สิ่งที่นับ |
| --- | --- |
| `models` | กลุ่มกล้องในรอบการรัน |
| `images_in_groups` | ภาพต้นทางจากกลุ่มเหล่านั้น |
| `targets_found` | เป้าหมายการสะท้อนแสงที่ตรวจพบ |
| `images_calibrated` | ภาพที่ผ่านการปรับเทียบในรอบการทำงาน |
| `exported_files` | **ไฟล์ผลิตภัณฑ์ภาพที่รอบการทำงานสร้างขึ้น** |
| `daq_recordings_exported` / `daq_recordings_skipped` | ข้อมูลบันทึกจากเซ็นเซอร์แสง, นับแยกกันโดยเจตนา — ข้อมูลเหล่านี้มาจากขั้นตอนที่ต่างกัน และมีอยู่สำหรับรอบการทำงานที่ไม่มีภาพเลย ดังนั้นการรวมข้อมูลเหล่านี้เข้าไปจะทำให้รอบการทำงานที่ใช้เพียงระบบ DAQ ดูเหมือนว่าได้ส่งออกภาพ |

พร้อมกับไฟล์เหล่านี้: `summary["output_dirs"]` (ทุกโฟลเดอร์ที่ถูกเขียนข้อมูลลง),
`summary["light_sensor_export"]`, `summary["stopped"]` (มีค่าเป็น true เมื่อผู้ใช้หยุดการ
รัน, ดังนั้นการนับส่วนจึงไม่ถูกอ่านเป็นการรันที่เสร็จสมบูรณ์แต่ผลิตได้น้อยกว่าที่คาด), และ
`summary["groups"]` (การแบ่งตามกลุ่ม).

`exported_files` ถูกบันทึกโดยระบบ **ขณะเขียน** ไม่ใช่จากการสแกนจาก
อ็อบเจกต์ภาพของโครงการในภายหลัง กลยุทธ์การทำงานแบบขนานและ GPU สร้างอ็อบเจกต์ภาพ
ของตนเอง (ในกระบวนการย่อยของ worker สำหรับเส้นทาง GPU) ดังนั้นการสแกนแบบเก่าที่รายงาน
`0 file(s) written` สำหรับการรันทุกครั้งเช่นนี้ และจากนั้นส่งสัญญาณเตือน zero-exports — ในการรัน
ที่ทุกอย่างทำงานได้ปกติ หากคุณเขียนสคริปต์อ้างอิงจากตัวเลขนี้ การรันแบบขนานที่ทำงานปกติจะ
รายงานจำนวนที่ไม่เป็นศูนย์

รายงานการข้ามของเซ็นเซอร์แสงจะระบุเหตุผลที่ตัวอ่านกำหนดไว้จริงสำหรับแต่ละไฟล์ — เช่น
สคีมาที่อ่านไม่ได้, แพ็กเกจที่ขาดหายไป, ข้อผิดพลาดในการเขียน — **ถูกลดการซ้ำ** ดังนั้นไฟล์ยี่สิบไฟล์
ที่ถูกข้ามไปเนื่องจากสาเหตุเดียว จะถูกนับเป็นสาเหตุเดียว แทนที่จะนับเป็นการซ้ำยี่สิบครั้ง

> **`process()` ไม่ถูกเรียกใช้เมื่อการรันไม่สร้างภาพใดๆ** นี่คือจุดเดียวที่SDK และ
> CLI มีความแตกต่างโดยเจตนา: `chloros-cli process` ถือว่า &quot;มีการร้องขอผลิตภัณฑ์ แต่ไม่มี
> ผลิตภัณฑ์ใดถูกเขียน&quot; เป็นความล้มเหลวและออกค่าไม่เท่ากับศูนย์ ในขณะที่ SDK กลับค่าปกติและรายงาน
> สภาวะดังกล่าวผ่าน `summary` / hints. หาก pipeline ของคุณควรหยุดลงเมื่อการรันไม่มีผลลัพธ์ ให้ตรวจสอบ
> ด้วยตัวเอง — ตรวจสอบ `summary` (หรือนับจำนวนไฟล์ในโฟลเดอร์โครงการ) แทนที่จะพึ่งพา
> การไม่มีข้อยกเว้น สาเหตุทั่วไปคือโฟลเดอร์อินพุตที่ไม่ได้ไม่ถูกรับรู้ว่าเป็น
> การจับภาพ และผลิตภัณฑ์ถูกข้ามไปเนื่องจากไม่เหมาะสมกับกล้องที่มีอยู่ (เช่น ความส่องสว่างจากกล้องRGB เท่านั้น
> )

### ฟังก์ชันอำนวยความสะดวก

```python
# One-call process: project + import + configure + process
results = chloros_sdk.process_folder(
    folder_path="C:/DroneImages/Flight001",
    project_name="FieldA_2026-05-26",
    camera="Survey3N_RGN",
    indices=["NDVI", "NDRE", "GNDVI"],
    vignette_correction=True,
    reflectance_calibration=True,
    export_format="TIFF (16-bit)",
    mode="parallel",
    debayer="High Quality (Faster)",      # or "Texture Aware (Slow, Highest Quality)"
    ppk=False,
    recursive=False,
    processing_timeout=14400,
)

# LATTICE-friendly defaults (no panel-target detection, standard debayer)
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)

# Audit which calibration sources were applied to a processed image
tags = chloros_sdk.read_image_audit_tags("output/Reflectance_Calibrated/x.tif")
print(tags["CalibrationSource"])   # 'per_serial' / 'legacy_lookup' / 'none'
print(tags["VignetteSource"])      # 'per_serial' / 'legacy_polynomial' / 'none'
```

### ค่าที่รองรับ

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"               # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
"Standard (Fast, Medium Quality)"      # alias used internally for LATTICE

# input_level (LATTICE only; Survey3 .raw ignores)
"auto"        # default — infers from each file's XMP ProcessingLevel tag
"raw"         # force-treat as raw Bayer
"debayered"   # force-treat as already-debayered BGR
"processed"   # force-treat as already-calibrated radiance

# array_alignment / array_alignment_crop (LATTICE arrays; None = keep saved setting)
True          # backend default — apply the module-to-module transform stamped
              # in each capture's Chloros:Alignment* XMP to every product
False         # export in native sensor geometry / skip the common-overlap crop

# array_alignment_interpolation (alignment warp resampling)
"bilinear"    # backend default
"nearest"     # preserves exact source DNs (no inter-pixel value mixing)
"cubic"
```

#### ผลลัพธ์ทางรังสี (LATTICE multispectral pipeline)

ระดับการส่งออก LATTICE multispectral ของ pipeline `process` (M3C/M3M) — `reflectance` (ค่าเริ่มต้น), `radiance`, `sensor-response`, หรือ `all` (ทุกโหมดที่ applicable สำหรับภาพแต่ละภาพ) — สอดคล้องกับการตั้งค่าการประมวลผล **&quot;Radiometric output&quot;** ของโครงการ `configure()` มีคำสำคัญเฉพาะสำหรับมัน:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("Field_A")
    cl.import_images("C:/Captures/lattice_flight")
    cl.configure(
        radiometric_output="radiance",   # reflectance (default) / radiance / sensor-response / all
        export_format="TIFF (32-bit, Percent)",
    )
    cl.process()
```

ทางออกฉุกเฉินขั้นสูง — การเขียนคีย์ `"Radiometric output"` ของโครงการผ่าน `custom_settings` — ยังทำงานได้ แต่โปรดจำไว้ว่ามันจะแทนที่บล็อกการตั้งค่าทั้งหมด (ดูคำเตือนด้านล่าง):

```python
cl.configure(custom_settings={
    "Project Settings": {
        "Processing": {"Radiometric output": "radiance"},
        "Export": {"Calibrated image format": "TIFF (32-bit, Percent)"},
    }
})
```

`reflectance` (ค่าเริ่มต้น) จะแบ่งความส่องของกล้องด้วย **สัญญาณ DAQ ที่ตรงกับเวลา**, ซึ่งถูกแก้ไขอัตโนมัติจาก `.daq` (DAQ-U/M/E) ที่บันทึกไว้**หรือ `.csv` แบบดั้งเดิมของ DAQ-M**ที่พบพร้อมกับภาพ; ชุดข้อมูลการปรับเทียบสำหรับกล้องแต่ละตัวหรือ DAQ ที่ขาดหายไปในระบบท้องถิ่นจะ**ถูกดึงมาอัตโนมัติจาก AWS** เมื่อใช้ครั้งแรก ระบบจัดการข้อมูล (CLI) แสดงตัวเลือกนี้ในรูปแบบสวิตช์ผลิตภัณฑ์ตามประเภทบน `chloros-cli process`: `--radiance`/`--no-radiance`, `--reflectance`/`--no-reflectance`, `--debayered`, `--preview`.

> `custom_settings` **แทน** บล็อกการตั้งค่าที่คำนวณไว้ทั้งหมด (ตามการออกแบบ มันจะข้ามคำสำคัญอื่นๆ และการตรวจสอบความถูกต้องของ `configure()` ไป) เมื่อใช้คำสั่งนี้ ให้รวมทุกคีย์ `Project Settings` ที่คุณต้องการไว้ด้วย เช่นในตัวอย่างข้างต้น

---

## Smart-Connect สำหรับกล้อง LATTICE

เซสชันแบ็กเอนด์ที่คงอยู่สำหรับฮาร์ดแวร์แบบเรียลไทม์ ใช้จุดปลายทางเดียวกันกับที่ GUI ใช้ ดังนั้นพฤติกรรมจึงเหมือนกันทั้งใน SDK / CLI / GUI.

### กล้องเดียว — `CameraSession`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    # cam is a CameraSession; supports context manager + manual disconnect
    cam.set_settings(
        exposure_time=10000,    # microseconds
        gain=0.0,               # dB
        pixel_format="BayerRG12",
        target_brightness=80,
        ae_damping=8.0,
    )
    cam.capture("output/", ext=".tiff")
```

#### ลายเซ็น `connect_camera()`

```python
connect_camera(
    serial,
    *,
    preset=None,                       # "default" | "high_quality" | "high_speed" | "triggered"
    settings=None,                     # dict overlaid on the preset
    backend_url="http://127.0.0.1:5000",  # deliberately not 'localhost' (::1-first on Windows ≈ 2 s/request)
    timeout=60.0,
    auto_start_backend=True,           # spawn a local backend if none is running
) -> CameraSession
```

#### `CameraSession` Methods

| Method | Description |
| --- | --- |
| `read_nodes(names, enum_names=(), timeout=30.0)` | อ่านโหนด GenICam; คืนค่า `{nodes, errors, enums, device}` |
| `set_settings(**kwargs)` | เขียนโหนดโดยใช้ชื่อที่เข้าใจง่าย (`exposure_time`, `gain`, `pixel_format`, `width`, `height`, `target_brightness`, `ae_damping`, `ae_upper_limit`, `trigger_mode`, `trigger_source`, …). |
| `capture(output_dir="output", ext=".tiff", jpeg_quality=95, processing=None, levels=None, force_daq=None, settings=None, timeout=None)` | จับภาพ **เฟรมเดียว** คืนค่าเป็นรายการที่มีหนึ่งองค์ประกอบ ซึ่งประกอบด้วยพจนานุกรมข้อมูลเมตาของเฟรม (การจับภาพแบบ Burst/หลายเฟรมได้ถูกลบออก — หากต้องการชุดภาพ ให้เรียก `capture()` ในลูป) |
| `disconnect()` | ปล่อยจากพูล ไม่ดำเนินการใดๆ หากได้แนบกับเซสชันที่เปิดอยู่แล้ว |

`capture()` การควบคุมการส่งออก (ใช้โมเดลเดียวกันกับอาร์เรย์ + GUI):

- `processing` / `levels` — `processing="all"` บันทึกทุกประเภทการส่งออกที่ตรงตามเงื่อนไข; `levels=["raw","radiance"]` บันทึกเฉพาะประเภทเหล่านั้น (แทนที่ `processing`) หากไม่ระบุทั้งสองค่า จะใช้ค่าเริ่มต้นของระบบหลังบ้าน
- `force_daq=True` — บันทึก DAQ/DLS ที่ถูกกำหนดไว้เป็น sidecar `.daq` แม้ในการจับข้อมูลแบบ raw เท่านั้น เพื่อให้สามารถประมวลผลเฟรมใหม่เป็น reflectance/index ได้ในภายหลัง ไม่ดำเนินการใดๆ หากไม่มี DAQ ที่เชื่อมโยงอยู่

### Synchronized Array — `ArraySession` (Smart-Prep)

`connect_array` เป็น **จุดเริ่มต้นที่แนะนำ** สำหรับการตั้งค่าหลายกล้อง มันทำงานตามขั้นตอน smart-prep ของ GUI อย่างเต็มรูปแบบเบื้องหลัง:

1. **การวิเคราะห์เครือข่าย** (`/api/camera/array/recommend`) — หาขนาดเฟรมที่ใหญ่ที่สุดที่เหมาะกับระดับ sim-emit โดยไม่ทำให้เฟรมถูกทิ้ง
2. **การเลือกระดับอัตโนมัติ** — `sim-capture-sim-emit` หากสายสัญญาณสามารถรองรับได้; หากไม่ `sim-capture-ftd-stagger` หรือ `slip-emit-and-capture`.
3. **การลดขนาดเฟรมอัตโนมัติ**— ลดขนาดเฟรม / เพิ่มการรวมพิกเซล (binning) โดยไม่แจ้งเตือน เมื่อสายสัญญาณไม่สามารถรักษาความละเอียดที่ร้องขอได้**มาตรการป้องกันนี้ไม่ครอบคลุมกรณีการสมัครเกินขีดจำกัดโดยรวม**: จำนวนกล้องที่มากเกินไปสำหรับสายไม่สามารถแก้ไขได้ด้วยการลดขนาดเฟรม — ดู [Over-Subscription](#over-subscription-the-per-cam-floor).
4. **PTP เปิดใช้งาน**ตามค่าเริ่มต้น — เวลาประทับของกล้องต่าง ๆ จะถูกปรับให้ตรงกับนาฬิกาที่ใช้ร่วมกันภายใน**~1 ms**. การเปิดรับแสงพร้อมกันเกิดจากทริกเกอร์ฮาร์ดแวร์ M8 (**&lt; 100 µs** ระหว่างโมดูล) ไม่ใช่จาก PTP: PTP จัดให้ *เวลาประทับ* ตรงกัน ไม่ใช่การเปิดรับแสง
5. **การเลือกรูปแบบพิกเซลอัตโนมัติตามกล้อง** — กล้อง RGB → `BayerRG8`, กล้องมัลติสเปกตรัม → `BayerRG12`.
6. **การตั้งค่าเริ่มต้น AE** — บันทึกสถานะ AE ปัจจุบันของแต่ละกล้อง เพื่อป้องกันไม่ให้การเชื่อมต่อรีเซ็ตการเปิดรับแสงระหว่างการทำงาน
7. **การตั้งค่าทริกเกอร์ GPIO** — `connect_array` เปิดใช้งานกล้องทุกตัว (`TriggerMode=On`, `TriggerSource=Line2`) เพื่อให้สัญญาณพัลส์จากกล้องหลักควบคุมกล้องรองผ่านสาย M8 นี่เป็นขั้นตอนสำหรับระบบอาร์เรย์เท่านั้น: กล้องเดียวที่เปิดด้วย `LatticeCamera` จะทำงานแบบอิสระแทน

```python
import chloros_sdk

# First serial is the MASTER (fires the trigger pulse); rest are slaves.
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    print(arr.array_id, arr.sync_mode, arr.ptp_enabled)
    arr.capture("output/", processing="reflectance")
```

#### ลายเซ็น `connect_array()`

```python
connect_array(
    serials,                              # list[str]; serials[0] = master
    *,
    line="Line2",                         # GPIO sync line: Line0 | Line2 | Line3
    target_fps=None,                      # master trigger fire rate (auto if None)
    force_tier=None,                      # override tier picker; see below
    wire_ceiling_mbps=None,               # host sustained wire budget, MB/s (auto if None)
    width=None,                           # explicit frame size; skips network analysis
    height=None,
    pixel_format=None,
    binning=None,
    recommend=True,                       # set False to skip the recommend step
    ptp_enable=True,                      # set False to disable PTP
    backend_url="http://127.0.0.1:5000",  # same IPv6-avoidance default as connect_camera
    timeout=180.0,
    auto_start_backend=True,              # spawn a local backend if none is running
) -> ArraySession
```

ค่า `force_tier`:
- `"sim-capture-sim-emit"` — การทำงานพร้อมกันจริง (กล้องทั้งหมดทำงานบนขอบสัญญาณนาฬิกาเดียวกัน)
- `"sim-capture-ftd-stagger"` — การจัดเรียงแบบยืดหยุ่นในโดเมนเวลา (กล้องส่งข้อมูลในเวลาที่เลื่อนกันเล็กน้อย ทำให้แพ็กเก็ตเรียงลำดับบนสายสัญญาณ)
- `"slip-emit-and-capture"` — การจับภาพแบบลำดับต่อกล้อง (ไม่มีการซิงค์เวลา; เป็นตัวเลือกเดียวเมื่อไม่มีขนาดเฟรมใดที่เหมาะกับโหมดซิมูเลชัน)

`wire_ceiling_mbps` จะแทนที่ **งบประมาณสายสัญญาณต่อเนื่องของโฮสต์** ใน MB/s — ตัวเลขเดียว
ที่การจัดสรรอาร์เรย์ทั้งหมดขึ้นอยู่กับ ให้ตั้งค่าเป็น `None` เพื่อใช้ค่าที่ตรวจจับอัตโนมัติ
ลดค่านี้ลงเมื่ออาร์เรย์รายงานเฟรมที่เสียหายจาก GVSP: ค่าอัตโนมัตินี้ถูกคำนวณ
จากอัตราการเชื่อมต่อที่ NIC ประกาศ ซึ่งเกิน-สถานะของอะแดปเตอร์ USB, เลน PCIe ที่บาง และ
โครงสร้างเครือข่ายที่ใช้ร่วมกันซึ่งมีภาระงานสูง — และการประเมินค่าสูงเกินไปนี้จะปรากฏเป็นเฟรมที่เสียหาย แทนที่จะเป็น
ลิงก์ที่ช้าอย่างเห็นได้ชัด ค่านี้จะถูกบันทึกไว้ในบล็อกการจับภาพอาร์เรย์ของโครงการ ดังนั้นการ
เปิดใหม่หรือการตั้งค่า `connect_array` ในภายหลังจะคืนค่า ให้กลับสู่สภาพเดิมได้เหมือนการตั้งค่าอาร์เรย์อื่น ๆ
ดู [Array Health](#array-health--which-subsystem-is-losing-frames).

#### การสมัครเกินขีดจำกัด (ระดับขั้นต่ำต่อกล้อง)

การกำหนดจังหวะ Sim-emit จะจัดสรรส่วนแบ่งของงบประมาณสายที่ปลอดภัยจากการชนกันให้กับแต่ละกล้อง โดยมีระดับขั้นต่ำที่ **8 MB/s ต่อกล้อง**(`per_cam_floor_bps`). เมื่อ `N × floor` เกินขีดจำกัดสูงสุดที่ปลอดภัยจากการชนกัน ระบบอาร์เรย์จะ**จัดสรรสายเกินขีดจำกัด**— รูปแบบความล้มเหลวคือการสูญเสียแพ็กเก็ต GVSP ไม่ใช่การลดลงของอัตราเฟรม — และไม่มีวิธีแก้ไขที่เกี่ยวข้องกับขนาดเฟรม:**การรวมภาพ (binning) และ ROI จะลดจำนวนไบต์ต่อเฟรม ไม่ใช่จำนวนไบต์ต่อวินาทีที่ถูกควบคุม**ซึ่งการตรวจสอบรวมจะเปรียบเทียบกัน ขีดจำกัดความละเอียดเต็มในทางปฏิบัติบนโฮสต์ 1 GbE:**6 กล้อง @ 1500 MTU, 9 กล้องพร้อมเฟรมจัมโบ้** (`max_cams_collision_safe` ในรายงานผลการวิเคราะห์จะแสดงขีดจำกัดสูงสุดสำหรับสายของคุณ) วิธีแก้ไข: ลดจำนวนกล้อง, ใช้เฟรมจัมโบ้แบบ end-to-end, หรือใช้ NIC ที่เร็วขึ้น

- คำตอบ `analyze_array_network()` และ `/api/camera/array/connect` ประกอบด้วย `oversubscribed`, `aggregate_demand_bps`, `collision_safe_ceiling_bps`, `max_cams_collision_safe` และ `per_cam_floor_bps`. เมื่อ `oversubscribed` เป็น true การแปลง **จะตั้งค่าฟิลด์ fps เป็นศูนย์** (`achievable_fps_max` / `fps_bright` / `fps_dark`) แทนที่จะ แทนที่จะรายงานอัตราที่ช้าแต่ยังทำงานได้ ซึ่งอาจทำให้เข้าใจผิด
- `POST /api/camera/array/connect` รับพารามิเตอร์ตัวเนื้อหา `pin_resolution` (**เฉพาะ HTTP — ไม่ใช่ kwarg ของ SDK**; `connect_array` ไม่เปิดเผยพารามิเตอร์นี้). การกำหนดค่าคงที่ (pinning) จะลบเครือข่ายความปลอดภัยของการลดระดับการรวมกลุ่ม (binning walk-down) ดังนั้น การเชื่อมต่อที่มีการสมัครเกินจำนวน (over-subscribed) ที่ตั้งค่า `pin_resolution` จะถูก**ปฏิเสธอย่างเด็ดขาด** พร้อมข้อผิดพลาดที่ระบุทุกวิธีแก้ไข หากไม่ใช้การกำหนดค่า pinning การเชื่อมต่อจะดำเนินการตามขั้นตอน walk-down แต่จะแจ้งเตือนว่า การลดขนาดไม่สามารถเคลียร์ค่ารวมได้
- ทางออกสำหรับการทดสอบบนเบนช์: ตั้งค่า `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` ในสภาพแวดล้อมของ backend เพื่อลดระดับการปฏิเสธลงเป็นคำเตือนที่ชัดเจน — คุณสามารถเชื่อมต่อต่อไปได้และยอมรับการสูญเสียแพ็กเก็ต

#### สภาพของอาร์เรย์ — ระบบย่อยใดกำลังสูญเสียเฟรม

`GET /api/camera/array/<array_id>/capability` พกพาบล็อก `health` ที่กำลังทำงานอยู่บน
อาร์เรย์ที่เชื่อมต่ออยู่, และถูกประเมินใหม่ในหน้าต่าง **10 วินาที** ที่หมุนเวียน มันแบ่งการสูญเสียเฟรม
ออกเป็นสองสาเหตุที่ต้องการการแก้ไขที่ตรงกันข้าม แทนที่จะเป็นอัตรา &quot;ไม่สมบูรณ์&quot; เดียวที่
ไม่ระบุสาเหตุใด:

| สนาม | ความหมาย | ระบบย่อยใด |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (ต่อซีเรียล) | เฟรม **มาถึงแต่มีโครงสร้างผิดปกติ**— การสูญเสียแพ็กเก็ต GVSP |**เครือข่าย**: งบประมาณสาย, การควบคุมความเร็ว, วงแหวนรับ NIC, MTU |
| `never_arrived_rate_pct` (ต่อซีเรียล) | เฟรม **ไม่มาถึงเลย**— กล้องไม่ทำงาน หรือไม่มีข้อมูลส่งออกมา |**ทริกเกอร์ / ซิงค์**: สาย M8, `line=`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | อัตราการส่งข้อมูลต่ำที่สุดของกล้องสำหรับ แต่ละตัว | — |
| `per_cam_rate_pct` | อัตราความไม่สมบูรณ์รวมต่อกล้อง (ทั้งสองสาเหตุรวมกัน). | — |
| `stable_for_seconds` | เวลาที่กล้องแต่ละตัวอยู่ในระดับต่ำกว่า 0.01 % | — |

ร่วมกับ `health` รายงานเดียวกันนี้ยังระบุจำนวนที่การจัดสรรทั้งหมดถูกระงับ:

| ฟิลด์ | ความหมาย |
| --- | --- |
| `wire_ceiling_mbps` | งบประมาณสายที่ใช้งานต่อเนื่องของโฮสต์, MB/s |
| `wire_ceiling_source` | ที่มาของตัวเลขนั้น, ในรูปแบบคำอธิบาย — เช่น `USB-capped 200 MB/s (was theoretical 1062; …)` หรือ `user override 120 MB/s (auto said 200)` |
| `wire_ceiling_is_user_set` | `true` เมื่อ `wire_ceiling_mbps=` ตั้งค่าไว้ |
| `nic_is_usb` | `true` สำหรับอะแดปเตอร์ USB Ethernet |

ไม่มี SDK wrapper สำหรับจุดปลายนี้ — อ่านโดยตรง:

```python
import requests, chloros_sdk

arr = chloros_sdk.attach_array(["213800234", "214000533"])
h = requests.get(
    f"http://127.0.0.1:5000/api/camera/array/{arr.array_id}/capability",
    timeout=10).json()

health = h.get("health", {})
print("wire ceiling:", h["wire_ceiling_mbps"], "MB/s", h["wire_ceiling_source"])
print("corrupt (network) :", health.get("worst_gvsp_corrupt_pct"), "%")
print("absent  (trigger) :", health.get("worst_never_arrived_pct"), "%")

if (health.get("worst_gvsp_corrupt_pct") or 0) > 1.0:
    # Network path. Reconnect with a lower budget -- NOT a lower target_fps.
    arr.disconnect()
    arr = chloros_sdk.connect_array(serials, wire_ceiling_mbps=120)
```

**การอ่านค่า:** ค่า `gvsp_corrupt_rate_pct` ที่ไม่เท่ากับศูนย์ พร้อมกับค่า `never_arrived_rate_pct` ที่ 0 หมายความว่า
การทริกเกอร์และการซิงค์สายเคเบิลสมบูรณ์แบบ และ 100% ของการสูญเสียเกิดขึ้นบนเส้นทางเครือข่าย — ค่าที่ต่ำกว่า
`wire_ceiling_mbps` และเชื่อมต่อใหม่ รูปแบบตรงกันข้ามชี้ไปที่สายซิงค์หรือ
สายทริกเกอร์แทน

> **`target_fps` ไม่ใช่ตัวควบคุมสำหรับเฟรมที่เสียหาย** การกำหนดจังหวะ GevSCPD ถูกเขียนเพียงครั้งเดียวเมื่อ
> เชื่อมต่อ ดังนั้นการลดอัตราการทริกเกอร์จะเปลี่ยนรอบการทำงาน (duty cycle) แต่ไม่เปลี่ยน
> อัตราการส่งข้อมูลแบบ burst พร้อมกัน การลดความต้องการลง 5× ตามการวัดไม่ก่อให้เกิดการปรับปรุงใด ๆ ในขณะที่
> การลดขีดจำกัดความเร็วของสายจาก 240 เป็น 200 MB/s ทำให้อุปกรณ์เดียวกันนี้ลดอัตราการเสียหายจาก 10.4 % ลงเหลือ
> 0.00 %

> **การลดความกว้างอัตโนมัติระหว่างการส่งข้อมูล (mid-stream auto-shrink) ไม่สามารถใช้งานได้บนเฟิร์มแวร์ TRI032S.** อาร์เรย์ที่กำลังทำงานอยู่ไม่สามารถ
> แก้ไขปัญหานี้ได้ด้วยตัวเอง; ให้ตัดการเชื่อมต่อและเชื่อมต่อใหม่ เพื่อให้ตัวเลือกระยะเวลาการเชื่อมต่อวางแผนใหม่ตาม
> ค่าเพดานใหม่

**อะแดปเตอร์ USB Ethernet ถูกจำกัดความเร็วไว้ที่ 200 MB/s** โดยตัวตรวจสอบ ไม่ว่าข้อมูลบนป้ายชื่อจะระบุไว้อย่างไร: ตารางประสิทธิภาพที่แปลงอัตราการเชื่อมต่อเป็นค่าความเร็วคงที่นั้น
มีต้นกำเนิดจาก PCIe และ NIC USB จะประกาศความเร็วการเชื่อมต่อ Ethernet ของมัน ในขณะที่ถูกจำกัดโดย
บัส USB และไดรเวอร์ของมัน ขีดจำกัดนี้เป็นค่าสัมบูรณ์ ไม่ใช่เศษส่วน — อะแดปเตอร์ USB 1 GbE
ให้อัตรา ~80 MB/s และไม่ได้รับผลกระทบ

#### `ArraySession` Methods

| วิธี | คำอธิบาย |
| --- | --- |
| `status(timeout=10.0)` | Live `{fps, ptp, frame_count, last_error, …}`. |
| `capture(output_dir="output", format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, smart=False, timeout=300.0)` | กลุ่มการจับภาพที่ซิงค์หนึ่งกลุ่ม คืนค่า `CaptureResult` (รายชื่อของ frame dicts + `.skipped`). ตัวควบคุมการส่งออกอยู่ด้านล่าง |
| `capture(..., smart=True)` | **Smart capture** — รอให้ AE เสถียรในทุกกล้อง แล้วจึงเริ่มบันทึก |
| `capture_fastest(output_dir="output", force_daq=True, render_index=True, timeout=120.0)` | การบันทึกเร็วที่สุด: raw-only + ค่าอ่าน DAQ ที่กำหนด (+ ดัชนีรวมที่ว่าง) สะท้อนปุ่ม &quot;Fastest Capture&quot; ใน GUI |
| `capture_repeated(output_dir="output", count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | โหมดเดี่ยว / ต่อเนื่อง / ตามช่วงเวลา ในวงจรจำกัดเดียว คืนค่า `list[CaptureResult]`**ต้องใช้ `count` และ/หรือ `duration_s`** เพื่อให้กระบวนการสิ้นสุด (SDK ไม่รองรับ Ctrl+C) |
| `record(output_dir="output", fps=10.0, duration_s=None, video=True, gif=False, timeout=30.0)` | เริ่มบันทึกมุมมองดัชนีรวมแบบสดเป็นวิดีโอ/GIF → `RecorderHandle`. เครื่องบันทึกแบบรวมหนึ่งเครื่องต่ออาร์เรย์ |
| `burst(output_dir="output", duration_s=None, max_frames=None, index_config=None, serial_index_config=None, timeout=30.0)` | เริ่มบันทึกแบบ burst Bayer ดิบที่ fps สูง → `RecorderHandle`. ประมวลผลใหม่แบบออฟไลน์ด้วย `build_video()` | raw-Bayer → `RecorderHandle`. ประมวลผลใหม่แบบออฟไลน์ด้วย `build_video()`. |
| `build_video(burst_dir, products=None, fps=10.0, video=True, gif=False, save_tiffs=False, wait=True, poll_s=2.0, timeout=1800.0)` | ประมวลผลใหม่แบบออฟไลน์ชุดภาพ raw ที่บันทึกไว้เป็นวิดีโอที่ปรับเทียบแล้ว หยุดทำงานจนกว่าจะเสร็จ (`wait=True`) และส่งคืน `{outputs, errors, combined}` |
| `build_video_status(job_id, timeout=15.0)` | ตรวจสอบงานสร้างแบบออฟไลน์: `{running, result, error, burst_dir}`. |
| `disconnect()` | ปล่อยอาร์เรย์ทั้งหมด |

`capture()` การควบคุมการส่งออก (ใช้จุดปลายทางเดียวกันกับ GUI/CLI):

- `processing` / `levels` — `processing="all"` (หรือ `levels=["raw","radiance",…]`) บันทึกทุกประเภทการส่งออกที่ applicable สำหรับแต่ละกล้อง; ค่า `processing` จะบันทึกเพียงระดับนั้นเท่านั้น
- `aligned=True` — ปรับการส่งออกที่ไม่ใช่แบบดิบของสมาชิกทุกคนให้สอดคล้องกับ [โปรไฟล์การจัดแนว](#array-alignment) (ที่ลงทะเบียนร่วมกัน); ข้อมูลดิบจะไม่ถูกปรับแนว แต่จะเก็บการแปลงไว้ในเมตาดาต้า หากไม่มีโปรไฟล์ จะใช้การจัดแนวแบบไม่จัดแนว (พร้อมคำเตือนที่ปรากฏใน `alignment` ของผลลัพธ์) หากอาร์เรย์ไม่มีโปรไฟล์
- `render_index=False` — ข้ามการทับซ้อนดัชนีพืชพรรณต่อกล้องแต่ละตัว; ค่าเริ่มต้นจะแสดงผลตามการตั้งค่า
- `force_daq=True` — บันทึกค่าอ่าน DAQ/DLS ที่กำหนดไว้เป็นไฟล์ sidecar `.daq` แม้ระดับที่เลือกจะไม่ต้องการก็ตาม

**การบีบอัดTIFF (ปุ่มควบคุมเฉพาะ HTTP):** `ArraySession.capture()` ไม่ส่งคีย์ `compression`, ดังนั้นค่าเริ่มต้นของระบบหลังจึงถูกใช้ — `POST /api/camera/array/capture` อ่านพารามิเตอร์ตัวเนื้อหา `compression`, `"deflate"` ตามค่าเริ่มต้น (zlib L1 แบบไม่สูญเสียข้อมูล + ตัวทำนายแนวนอน, ~4.1 MB ต่อเฟรมความละเอียดเต็ม). `"none"` เขียนแบบไม่บีบอัด (~6.3 MB/เฟรม) ด้วย**ความเร็วการเขียนที่เร็วขึ้น ~5 เท่า** — ทั้งสองแบบไม่สูญเสียข้อมูลและอ่านได้เหมือนกันเมื่อนำเข้า SDK ไม่เปิดเผย kwarg สำหรับมัน; ทางออกคือ `chloros-cli lattice array-capture --compression none` หรือ raw HTTP. DEFLATE ยังถือ GIL ของ Python ด้วย ดังนั้นการเขียนข้อมูลที่ถูกบีบอัดจึงไม่สามารถทำงานแบบขนานข้ามเธรดผู้เขียนต่อกล้องได้ — การบันทึกแบบต่อเนื่อง 8 กล้องที่ความละเอียดเต็ม-res ที่อัตราความเร็วของเซ็นเซอร์ ต้องใช้ `compression: "none"` รายละเอียด: [CLI Reference → array-capture](cli-reference.md).**การแทนที่การส่งออกต่อสมาชิก (เฉพาะ HTTP):**จุดปลายทางเดียวกันนี้ยังรับ `exclude_serials` (list — ลบสมาชิกออกจากชุดที่บันทึกไว้; มصفوفยังคงทำงานเป็นกลุ่มที่ซิงค์กัน และสมาชิกที่ถูกยกเว้นจะถูกส่งคืนใน `excluded`), `serial_levels` (การแทนที่ระดับกล้องต่อกล้อง `{serial: [level tokens]}`), และ `serial_index` (`{serial: bool}` ต่อ-cam index-overlay overrides). These are GUI-parity body params and**not SDK kwargs yet**; members absent from the maps fall back to the array-wide `levels` / `render_index`.

##### ตรวจสอบแคมที่ถูกข้าม — `CaptureResult.skipped`

`ArraySession.capture()` คืนค่า `CaptureResult` ซึ่งคือ `list` : สามารถวนซ้ำ, จัดดัชนี, และใช้ `len()` กับมันได้ — ทุกแบบแผนที่มีอยู่ยังคงทำงานได้ปกติ โค้ดใหม่สามารถตรวจสอบคุณสมบัติ `.skipped` เพื่อดูว่ากล้องใดถูกยกเว้นและเหตุผลที่นำไปสู่การยกเว้นนั้น กรณีที่พบบ่อยที่สุดคือกล้องRGBในอาร์เรย์ที่มีฟิลเตอร์ผสม เมื่อคุณร้องขอ `processing="radiance"` หรือ `"reflectance"` — ค่า radiance ต่อ Bayer แต่ละจุดไม่มีความหมายสำหรับเซ็นเซอร์แบบแบนด์วิดท์กว้าง ดังนั้นระบบหลังจึงข้ามกล้องเหล่านั้นแทนที่จะสร้างข้อมูลที่ไม่มีประโยชน์

```python
with chloros_sdk.connect_array(serials) as arr:
    result = arr.capture("output/", processing="reflectance")

    # Back-compat: iterate as a plain list
    for frame in result:
        print(frame["filepath"], frame["serial"])

    # New: see why N-1 cams were saved
    for skip in result.skipped:
        print(f"skipped SN:{skip['serial']} reason={skip['reason']}")
        # e.g. {'serial': '214701292', 'level': 'reflectance',
        #       'reason': 'reflectance-not-applicable-to-rgb-cam',
        #       'filter': 'RGB'}
```

โทเค็นเหตุผลจะตามรูปแบบ `<level>-not-applicable-to-rgb-cam` (หนึ่งรายการต่อระดับที่ถูกข้ามไป แต่ละรายการมี `level`) การข้ามที่เฉพาะเจาะจงสำหรับค่าสะท้อนแสงคือ `reflectance-skipped-no-fresh-dls` (ไม่มีค่าการวัดแสงลงใหม่), `reflectance-skipped-bound-daq-unavailable (…)` (ไม่สามารถติดต่อ DAQ ที่ผูกไว้ได้), และ `dls-uncalibrated-band-<nm>` — ช่วงความยาวคลื่นนี้ส่วนใหญ่อยู่นอกช่วงที่ปรับเทียบทางรังสีของเซ็นเซอร์แสง DAQ (~374–974 นาโนเมตร) ดังนั้น การแบ่งค่าการสะท้อนแสงแบบสัมบูรณ์ที่อิงจาก DAQ จึงถูกปฏิเสธ และเฟรมจะถูกลดระดับลงอย่างชัดเจนเพื่อใช้การตอบสนองของเซ็นเซอร์ ในบรรดา SKU ที่จัดส่ง มีเพียง F988 เท่านั้นที่ทริกเกอร์เหตุการณ์นี้; เส้นทางที่กล้องรุ่นนี้รองรับคือกระบวนการทำงานของแผงสะท้อนแสง

ระดับ `processing`:

| ระดับ | ผลลัพธ์ |
| --- | --- |
| `"raw"` | Bayer 1 ช่อง (กล้องโมโนโครม: แถบเดียว) จากเซ็นเซอร์โดยตรง |
| `"debayered"` *(ค่าเริ่มต้นของ SDK)* | BGR 3 ช่องทางผ่านกระบวนการเดโมซิกแบบบิลิเนียร์ (กล้องโมโนโครม: 1 ช่องทางแบบเกรย์สเกล). |
| `"radiance"` | float32 W/m²/sr/nm ผ่านห่วงโซ่รังสีวัดเต็มรูปแบบ เฉพาะแบบมัลติสเปกตรัม — กล้องRGB จะถูกข้าม |
| `"reflectance"` | uint16 0..32768 (พร้อมสำหรับ Pix4D); ต้องมีการจับคู่ DAQ แบบเรียลไทม์เพื่ออ้างอิงค่าสัมบูรณ์ ใช้ได้เฉพาะแบบมัลติสเปกตรัมเท่านั้น |
| `"display"` | โซ่การวัดเต็มรูปแบบที่ตรงกับ GUI (CCM + WB + gamma ตามโปรไฟล์ของกล้อง). |
| `"all"` | **ไฟล์หนึ่งต่อระดับที่ applicable** สำหรับกล้องแต่ละตัว (ตรงกับค่าเริ่มต้นของ GUI &quot;Capture All&quot; / CLI). `CaptureResult` ที่ส่งกลับมาจะเก็บ dict ของเฟรมหนึ่งต่อ `(cam, level)` โดยระดับจะอยู่ในแต่ละ dict; ระดับที่ไม่เกี่ยวข้องจะปรากฏใน `.skipped` ค่าการอ่าน DAQ ที่ใช้สำหรับเฟรมการสะท้อนแสงใดๆ จะถูกบันทึกเป็น sidecar `.daq` |

> **หมายเหตุ — ค่าเริ่มต้นแตกต่างจาก CLI.** `ArraySession.capture()` มีค่าเริ่มต้นเป็น `processing="debayered"`; คำสั่ง `chloros-cli lattice array-capture` มีค่าเริ่มต้นเป็น `processing="all"`. ส่ง `processing="all"` อย่างชัดเจนจาก SDK เพื่อสะท้อนการบันทึกหลายระดับของ CLI /GUI

### โหมดการจับภาพและเครื่องบันทึก

พื้นผิวของอาร์เรย์สะท้อนแผงจับภาพของ GUI: โหมดชัตเตอร์แบบเดี่ยว / ต่อเนื่อง / ตามช่วงเวลา / เร็วที่สุด พร้อมด้วยเครื่องบันทึกสองตัว (วิดีโอคอมโพสิตแบบสดและโหมดถ่ายภาพต่อเนื่องแบบ raw → ประมวลผลใหม่แบบออฟไลน์).

```python
import time, chloros_sdk

with chloros_sdk.connect_array(serials) as arr:
    # Single (default) — one synced group
    arr.capture("out/", processing="reflectance")

    # Fastest — raw + .daq + combined index now, calibrate later
    arr.capture_fastest("flightline/")

    # Interval — one reflectance pass every 2 s, 5 passes (bounded so it ends)
    arr.capture_repeated("timelapse/", count=5, interval_s=2.0,
                         processing="reflectance",
                         on_capture=lambda i, r: print(f"pass {i}: {len(r)} frames"))

    # Combined-index video/GIF recorder (needs the combined live view streaming)
    with arr.record("monitoring/", fps=10, gif=True) as rec:
        time.sleep(30)
    print(rec.result["video_path"])

    # Raw-Bayer burst → offline reprocess into calibrated video(s)
    with arr.burst("capture/", duration_s=5) as b:
        pass
    out = arr.build_video(b.result["out_dir"], products=[
        {"kind": "per_cam", "level": "reflectance"},
        {"kind": "combined", "level": "index"}])
    print(out["outputs"])
```

- **`capture_repeated`**คือโหมด Continuous/Interval loop ของ SDK เนื่องจากไม่มี `Ctrl+C` เพื่อหยุดการทำงานจากสคริปต์ คุณ**ต้อง** ส่ง `count` และ/หรือ `duration_s` (ระบบจะหยุดเมื่อถึงค่าใดค่าหนึ่ง) `interval_s` ถูกวัดจากจุดเริ่มต้นของแต่ละรอบ (ตรงกับ GUI) kwargs ที่เหลือจะถูกส่งผ่านตรงไปยัง `capture()`
- **`record`** เป็น *ระดับการเฝ้าติดตาม*: มันบันทึกคอมโพสิตดัชนีรวมแบบเรียลไทม์ตามที่แสดง ดังนั้นสตรีมรวมต้องเปิดอยู่เพื่อให้เฟรมสามารถส่งเข้ามาได้ บันทึกคอมโพสิตหนึ่งตัวต่ออาร์เรย์ (จะแจ้งเตือนหากมีตัวหนึ่งกำลังทำงานอยู่แล้ว).
- **`burst` → `build_video`** เป็น *ระดับการวิเคราะห์*: `burst` จะเขียนเฟรมดิบ + รายการข้อมูลต่อเฟรม + `.daq` สำหรับการอ่าน DLS ที่แตกต่างกันแต่ละครั้งภายใต้ `<output>/bursts/<base>/` ที่ความเร็วเต็มของวงจรการจับภาพ (ไม่มี chain, ไม่มี exiftool, ไม่มี live view) `build_video` จับคู่เวลาของแต่ละเฟรมกับ `.daq` ที่ใกล้ที่สุด และรันใหม่กระบวนการนำเข้า radiance/reflectance/index chain ของ pipeline การนำเข้า `products` เป็นรายการของ `{"kind": "per_cam"|"combined", "level": "radiance"|"reflectance"|"index"}` (ค่าเริ่มต้น: ดัชนีรวม) `burst().stop()` ยังจะเริ่มสร้างดัชนีรวมแบบ best-effort โดยอัตโนมัติ, ซึ่งถูกส่งคืนเป็น `build_job` ในผลลัพธ์การหยุด

#### `RecorderHandle`

ถูกส่งคืนโดย `ArraySession.record()` และ `ArraySession.burst()` ใช้เป็นผู้จัดการบริบทเพื่อหยุดอัตโนมัติเมื่อออกจากขอบเขต หรือควบคุมด้วยตนเอง

| สมาชิก | คำอธิบาย |
| --- | --- |
| `job_id` | รหัสงานแบ็กเอนด์ (str). |
| `kind` | `"composite"` (จาก `record`) หรือ `"raw"` (จาก `burst`). |
| `start_stats` | พจนานุกรมที่ส่งคืนจากการเรียก `start` |
| `result` | `None` ระหว่างการทำงาน; พจนานุกรมผลลัพธ์การหยุดสุดท้ายเมื่อหยุดแล้ว |
| `stats(timeout=10.0)` | สถิติงานแบบเรียลไทม์ (จำนวนเฟรมที่เขียน, fps ที่ทำได้จริง, เวลาที่ผ่านไป). |
| `stop(timeout=60.0)` | หยุดเครื่องบันทึก; คืนค่าและเก็บผลลัพธ์สุดท้ายไว้ในแคช. การเรียกซ้ำได้ (การเรียกใช้ครั้งที่สองจะคืนค่าผลลัพธ์ที่เก็บไว้ในแคช) |

```python
rec = arr.burst("capture/")
# ... drive manually ...
print(rec.stats()["frames"])
result = rec.stop()
print(result["out_dir"], result.get("build_job"))
```

### การเชื่อมต่อกับอาร์เรย์ที่เชื่อมต่ออยู่แล้ว — `attach_array`

หากอาร์เรย์กำลังทำงานอยู่ (GUI ได้เปิดมันขึ้นแล้ว, หรือเซสชัน SDK ก่อนหน้านี้ได้เรียก `connect_array`), ให้ใช้ `attach_array` เพื่อรับ handle ของมันแทนที่จะเชื่อมต่อใหม่ `connect_array` จะแสดงข้อผิดพลาดเสมอว่า &quot;Camera  is<sn> already in array <id>&quot; ในสถานการณ์นั้น เนื่องจากการส่ง POST ไปยัง `/array/connect` สำหรับสมาชิกในพูลไม่ใช่การดำเนินการแบบ idempotent; `attach_array` อ่าน `/api/camera/array/list` และจับคู่โดยใช้ array_id หรือ serials

```python
import chloros_sdk

# By serials (matches if every serial is a member of one existing array)
arr = chloros_sdk.attach_array(
    ["213800234", "214000533", "214701288", "214701292"])

# By array_id (when you've already noted it down)
arr = chloros_sdk.attach_array("array-1779862544497")

# attach_array returns the same ArraySession as connect_array
arr.capture("output/", processing="reflectance")
```

รูปแบบ: SDK สคริปต์ที่ทำงานร่วมกับ GUI บนเดสก์ท็อปควรลองใช้ `attach_array` ก่อน และใช้ `connect_array` เป็นทางเลือกสำรองหากยังไม่มีอาร์เรย์ในพูล

```python
import chloros_sdk

try:
    arr = chloros_sdk.attach_array(serials)
except chloros_sdk.ChlorosConnectError:
    arr = chloros_sdk.connect_array(serials)
```

> **สำคัญ — การออกจาก context-manager จะตัดการเชื่อมต่อ**`ArraySession.disconnect()` จะส่ง POST ไปยัง `/array/disconnect` เสมอ; ไม่มีตัวตรวจสอบ &quot;attached-not-owned&quot; เหมือนที่มีใน `CameraSession` / `DAQSensorSession` หากคุณกำลังใช้ร่วมกับ GUI และไม่ต้องการรื้ออาร์เรย์เมื่อออกจากขอบเขต**อย่าใช้บล็อก `with`** — เก็บ handle ไว้ในตัวแปรปกติและข้าม `disconnect()` ที่ระบุไว้อย่างชัดเจน:
>
> ```python
> arr = chloros_sdk.attach_array(serials)
> arr.capture("output/", processing="reflectance")
> # … script ends; array stays up for the GUI
> ```

### เครื่องมือช่วยวิเคราะห์เครือข่าย

มีประโยชน์ก่อนเปิดอาร์เรย์ — คาดการณ์ว่าการตั้งค่าที่คุณเสนอจะเหมาะสมหรือไม่:

```python
result = chloros_sdk.analyze_array_network(
    master_serial="214701288",
    slave_serials=["213800234", "214000533", "214701162"],
    width=2048, height=1536,
    pixel_format="BayerRG12",
    binning=1,
)

if result["status"] == "ok":
    print("Use the requested settings.")
elif result["status"] == "auto_capped_fps":
    r = result["recommended"]
    print(f"Keep the resolution; cap the trigger rate at {r['recommended_target_fps']} fps")
elif result["status"] == "auto_shrunk":
    r = result["recommended"]
    print(f"Shrink to {r['out_width']}x{r['out_height']} binning={r['binning']}")
elif result["status"] == "needs_force_slip":
    print("Sim-sync impossible on this wire; force_tier='slip-emit-and-capture' required")
```

`status` เป็นหนึ่งใน `ok` / `auto_capped_fps` / `auto_shrunk` / `needs_force_slip` (หรือ `error`). `auto_capped_fps` หมายความว่าความละเอียดที่ขอมาจะเหมาะกับวงแหวน RX ได้เฉพาะที่อัตราการทริกเกอร์ถูกจำกัด — ให้รักษาความละเอียดไว้และส่ง `target_fps=result["recommended"]["recommended_target_fps"]` ไปยัง `connect_array` (ดู [ตัวอย่าง 6](#6-capability-probe-before-connecting-a-4-cam-array)).

**วิธีอ่านการฉายภาพ** (ใช้แบบเดียวกันกับแผง Array Settings ใน GUI):

- **Burst (`frame_bytes_total`) จะถูกรวมผลตามแต่ละกล้องในรูปแบบพิกเซลจริงของกล้องนั้น**Mono**M3M**กล้องจะส่งข้อมูล Mono12 (2 B/px) ไม่ว่าคุณจะส่งค่า `pixel_format` ใด ดังนั้นเฟรมความละเอียดเต็มของกล้อง 4 ตัวจะมีขนาด**~25 MB** เมื่อใช้กล้องโมโน 3 ตัว ไม่ใช่ ~12.6 MB ตามสมมติฐานที่ใช้ 8 บิตทั้งหมด ระบบหลังบ้าน จะกำหนดรูปแบบของแต่ละกล้องจากรุ่นของกล้องนั้น
- **ค่าการรับ (`burst_fits_nic_ring`) คำนึงถึงการระบายข้อมูล**ไม่ใช่การเปรียบเทียบระหว่าง burst ทั้งหมดกับ ring: การจำลองการส่งข้อมูล (sim-emit) จะเหมาะสมเมื่อโฮสต์ระบายข้อมูลจาก ring รับ (RX ring) ได้เร็วกว่าที่กล้องเติมข้อมูลเข้าไป โฮสต์ 10G + กล้อง 1 GbE**รองรับ** ความละเอียดเต็ม แม้เมื่อการส่งข้อมูลแบบ burst เกินความจุของ ring; โฮสต์ 1 GbE จะบล็อก (`needs_force_slip` / `auto_shrunk`).
- **`achievable_fps_max` เป็นขีดจำกัดการรับข้อมูลแบบอนุรักษ์นิยม** — `max(readout+emit, N×emit)` ที่การส่งข้อมูลต่อกล้องถูกจำกัดไว้ที่ลิงก์กล้อง 1 GbE โดยไม่ขึ้นอยู่กับค่าการเปิดรับแสง ตัวอย่างเช่น ~2.8 fps สำหรับอาร์เรย์ 4 กล้อง ความละเอียดเต็ม 12 บิต (ตรงกับค่าที่วัดได้ในช่วงรันไทม์ ~2.7–3.0) แบบจำลองเต็ม: [CLI Reference → Array fps &amp; burst model](cli-reference.md#array-fps--burst-model).
- **Over-subscription (`oversubscribed: true`) หมายความว่า ค่าขั้นต่ำต่อกล้อง N × เกินค่าสูงสุดที่ปลอดภัยจากการชนกัน** — สนาม fps (`achievable_fps_max` / `fps_bright` / `fps_dark`) แสดงค่า 0 และฟังก์ชัน auto-shrink/binning ไม่สามารถแก้ไขได้ (ฟังก์ชันเหล่านี้ลดจำนวนไบต์ต่อเฟรม ไม่ใช่จำนวนไบต์ที่จัดจังหวะต่อวินาที). วิธีแก้ไขคือลดจำนวนกล้อง, ใช้เฟรมจัมโบ้ หรือใช้ NIC ที่เร็วขึ้น; `max_cams_collision_safe` รายงานค่าเพดาน (6 กล้องความละเอียดเต็มบน 1 GbE @ 1500 MTU, 9 เมื่อใช้จัมโบ้เฟรม). คำตอบยังส่งมาพร้อม `aggregate_demand_bps`, `collision_safe_ceiling_bps` และ `per_cam_floor_bps` (8 MB/s) ดู [Over-Subscription](#over-subscription-the-per-cam-floor).

### การค้นพบและแสดงรายการ

```python
chloros_sdk.discover_lattice_cameras()   # list all cams visible to the backend
chloros_sdk.list_cameras()               # cams currently in the pool
chloros_sdk.list_arrays()                # active arrays in the pool
```

---

## Smart-AE / Smart-Capture

อาร์เรย์ LATTICE จะทำงาน AE แบบต่อเนื่องในพื้นหลังทันทีที่เชื่อมต่อ แต่ฉากที่เพิ่งปรับโฟกัสใหม่จะใช้เวลาสักครู่เพื่อให้ค่าต่าง ๆ บรรจบกัน **Smart-Capture** คือฟังก์ชันที่ออกแบบมาเพื่อความสะดวก: มันจะตรวจสอบค่าการเปิดรับแสงของแต่ละกล้อง รอจนกระทั่งอาร์เรย์มีค่าที่เสถียรทั่วทั้งหน้าต่าง แล้วจึงเริ่มการจับภาพ ฟังก์ชันนี้เทียบเท่ากับ GUI: ปุ่ม &quot;smart&quot; capture button calls the same backend endpoint.

```python
import chloros_sdk

with chloros_sdk.connect_array([
        "213800234", "214000533", "214701288", "214701292"]) as arr:
    # Initial pose
    arr.capture("pose_a/", processing="reflectance", smart=True)
    input("Move the rig, then press Enter...")
    # New pose — smart-capture waits for AE to re-settle automatically
    arr.capture("pose_b/", processing="reflectance", smart=True)
```

เมื่อควบคุมผ่าน `ChlorosProject` (ส่วนถัดไป) คุณจะได้รับตัวเลือกเพิ่มเติม:

```python
proj.arrays["main_rig"].capture_smart(
    output_dir="out/",
    processing="reflectance",
    settle_timeout_s=5.0,           # max wait
    stability_window_s=1.5,         # exposure must hold steady this long
    exposure_tolerance_pct=5.0,     # %-spread allowed within the window
)
```

นโยบาย smart-AE ตั้งค่าเป็นแบบอนุรักษ์นิยมตามค่าเริ่มต้น ปรับค่า `exposure_tolerance_pct` ให้แคบลงสำหรับงานวัดรังสีที่ต้องการความแม่นยำสูง; ปรับให้กว้างขึ้นสำหรับฉากที่เปลี่ยนแปลงอย่างรวดเร็ว ซึ่งคุณต้องการเพียง &quot;ใกล้เคียงพอ&quot;

---

## เซสชันเซ็นเซอร์ DAQ

กลุ่มแบ็กเอนด์ที่คงที่สำหรับเซ็นเซอร์สเปกตรัม (DAQ-U ผ่าน USB, DAQ-M ผ่าน BLE, DAQ-E ผ่าน Ethernet) สะท้อนคุณสมบัติของกล้อง: smart-detect, การใช้กลุ่มซ้ำ, การเชื่อมต่อแบบ idempotent

### Smart-Detect (Zero-Config)

```python
import chloros_sdk

with chloros_sdk.connect_daq_sensor() as daq:
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=10):
        spectrum = frame["spectrum"]   # list[float] (W/m²/nm if calibrated)
        is_sat = frame["is_saturated"]
        x, y, z = frame["x"], frame["y"], frame["z"]
        print(len(spectrum), is_sat)
```

ลำดับความสำคัญ: Ethernet → BLE → USB. ส่งคำชี้แนะที่ชัดเจนใดก็ตามเพื่อกำหนดการขนส่ง

### การขนส่งที่กำหนด

```python
# DAQ-U on a specific serial port
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")

# DAQ-M over BLE by MAC (implies transport="ble")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")

# DAQ-E over Ethernet by hostname (implies transport="eth")
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")

# Tuning knobs
daq = chloros_sdk.connect_daq_sensor(
    port="COM3",
    integration_time=64,      # ms
    frame_avg=20,
    enable_ae=True,
    start_streaming=True,
)
```

### `DAQSensorSession` Methods

| Method | Description |
| --- | --- |
| `status(timeout=10.0)` | สรุปรายการในพูล (สถานะการสตรีม/บันทึก, ช่วงความยาวคลื่น, ค่า calibrated sha, เวลาการรวม, frame_avg, สถานะ AE). |
| `latest(n=1, timeout=10.0)` | คืนค่าเฟรมสเปกตรัมล่าสุดสูงสุด N เฟรม |
| `stream_start()` / `stream_stop()` | ดำเนินการต่อ / หยุดชั่วคราวการสตรีมมิ่ง (handle ยังคงเปิดอยู่). |
| `record_start(output_dir=None, device_name=None)` | เริ่มบันทึกไฟล์ .daq และคืนค่าเส้นทางไฟล์ (filepath) | ปฏิเสธสำหรับ DAQ-U/M ที่ไม่มีชุดข้อมูลการปรับเทียบ AWS (DAQ-E ยกเว้น). |
| `record_stop()` | หยุดการบันทึก. คืนค่า `{path, rows}`. |
| `disconnect()` | ปล่อยจากพูล ไม่ดำเนินการสำหรับ handle ที่ถูกแนบแต่ไม่ใช่ของตัวเรียก |

> **โปรไฟล์การปรับค่า Cap (`cap_id`) ไม่ใช่ปุ่มปรับค่าSDK** `connect_daq_sensor()` / `DAQSensorSession` ไม่เปิดเผยพารามิเตอร์ `cap_id` หรือวิธีการ `set_cap` ใดๆ ทั้งสิ้น ให้เลือกโปรไฟล์การปรับแก้ขีดจำกัดของกลุ่มผ่าน CLI (`chloros-cli daq pool-connect --cap-id …` / `chloros-cli daq pool-set-cap …`) หรือเส้นทาง `/api/daq` HTTP ของระบบหลัง (`/api/daq/connect` และ `/api/daq/<id>/cap-id` รับ `cap_id`).

### การค้นพบ — การค้นหาที่อยู่เพื่อเชื่อมต่อ

`discover_daq_sensors()` สแกน USB / BLE / ETH เพื่อค้นหาเซ็นเซอร์ที่คุณ *อาจ* เปิดได้ นี่คือเวอร์ชัน DAQ ที่เทียบเท่ากับ `discover_lattice_cameras()` และเป็นวิธีเดียวที่จะได้รับ **BLE MAC**ของ DAQ-M&#x27;** — DAQ-E มีชื่อโฮสต์ และ DAQ-U มีพอร์ต COM แต่ MAC ไม่ถูกพิมพ์บนอุปกรณ์หรือแสดงในระบบปฏิบัติการ

```python
for s in chloros_sdk.discover_daq_sensors():
    print(s["transport"], s["address"], s["model"], s["extra"])
# ble  C3:D8:85:E0:0A:19  DAQ-M  {'name': 'NSP32_SPECTRUM'}
# usb  COM3               None   {'manufacturer': 'Intel'}

# `address` is exactly what connect_daq_sensor wants:
for s in chloros_sdk.discover_daq_sensors(transports=["ble"]):
    if s["model"] == "DAQ-M":
        daq = chloros_sdk.connect_daq_sensor(mac=s["address"])
```

| สนาม | คำอธิบาย |
| --- | --- |
| `transport` | `usb` \| `ble` \| `eth`. |
| `address` | พอร์ต COM / MAC BLE / hostname — ส่งต่อไปยัง `connect_daq_sensor` ในรูปแบบ `port=` / `mac=` / `eth_host=`. |
| `display` | ชื่อที่อ่านได้โดยมนุษย์ |
| `model` | `DAQ-U` \| `DAQ-M` \| `DAQ-E` หรือ `None` สำหรับพอร์ตที่การสแกนไม่สามารถระบุได้ (อะแดปเตอร์ USB แบบซีเรียลไม่สามารถแยกแยะได้หากไม่มีโพรบ ดังนั้นค่าที่ไม่ทราบจะถูกแสดงแทนที่จะถูกซ่อน). |
| `extra` | รายละเอียดตามโปรโตคอล (ชื่อที่ BLE โฆษณา, ผู้ผลิต USB, DAQ-E ip/fw/…) ค่าที่ว่างจะถูกข้ามไป |

| พารามิเตอร์ | ค่าเริ่มต้น | คำอธิบาย |
| --- | --- | --- |
| `transports` | ทั้งสาม | ลำดับ (หรือสตริง csv) ที่จำกัดการสแกน ควรส่งค่านี้เมื่อทราบสิ่งที่ต้องการ — BLE เป็นส่วนที่ช้าที่สุด |
| `scan_timeout` | 5 | หน้าต่างการสแกนต่อโปรโตคอล (transport) ในหน่วยวินาที; ระบบหลังจะจำกัดค่าให้อยู่ระหว่าง 1–20 |
| `timeout` | 60.0 | ค่าสูงสุดของ HTTP สำหรับการเรียกทั้งหมด (เช่นเดียวกับในส่วนอื่น ๆ ของ SDK) | |
| `auto_start_backend` | `True` | สร้าง backend ท้องถิ่นหากยังไม่มีที่ทำงานอยู่ ไม่สร้างสำหรับ `backend_url` ที่อยู่ห่างไกล |

> **เซ็นเซอร์ที่เปิดอยู่แล้วในพูลจะไม่ปรากฏ** อุปกรณ์ BLE ที่เชื่อมต่ออยู่จะหยุดการโฆษณา และพอร์ต COM ที่เปิดอยู่ไม่สามารถตรวจหาได้ ดังนั้นการค้นพบจะแสดงรายการสิ่งที่ *พร้อมสำหรับการเชื่อมต่อ* ผลลัพธ์ที่ว่างเปล่าทันทีหลังจากที่คุณเชื่อมต่ออุปกรณ์ใด ๆ เป็นสิ่งที่คาดการณ์ได้ — ใช้ `list_daq_sensors()` สำหรับสิ่งที่คุณถืออยู่แล้ว. การขนส่งที่ไม่สามารถสแกนได้ (ไม่มีการติดตั้ง bleak / zeroconf) จะถูกข้ามไปแทนที่จะแจ้งข้อผิดพลาด ดังนั้นเครื่องที่ไม่มี Bluetooth ยังคงได้รับคำตอบจาก USB และ ETH

### รายการ

```python
for s in chloros_sdk.list_daq_sensors():
    print(s["sensor_id"], s["model"], s["transport"], s["wavelength_range"])
```

### การใช้งานร่วม (Co-Tenancy) กับ GUI / CLI

หาก GUI มีเซ็นเซอร์ที่เปิดอยู่แล้ว การเรียก `connect_daq_sensor(port="COM3")` จาก Python จะคืนค่า handle ที่ถูกทำเครื่องหมายเป็น `already_connected=True` ส่วน `disconnect()` ของเซสชันนั้น จะไม่มีผลใดๆ ดังนั้นสคริปต์ SDK ของคุณจึงไม่ดึงเซ็นเซอร์ออกจาก GUI เมื่อ scope ปิดตัวลง

### คลาสฮาร์ดแวร์โดยตรง (ไม่มีแบ็กเอนด์)

`daq_sdk` ถูกส่งออกใหม่โดย `chloros_sdk` ดังนั้นคุณ สามารถควบคุมเซ็นเซอร์แบบ end-to-end ภายในกระบวนการได้โดยไม่ต้องใช้ backend:

> **ความพร้อมใช้งาน:**`daq_sdk` มาพร้อมกับแพ็กเกจติดตั้งบนเดสก์ท็อป Chloros,**ไม่ใช่** มาพร้อมกับแพ็กเกจ PyPI — `pip install chloros-sdk` ให้คุณใช้ `lattice_sdk` แต่ยังคง `chloros_sdk.DAQ_AVAILABLE == False` ไว้ ตรวจสอบธงนี้ก่อนใช้คลาสเหล่านี้; บนโฮสต์ที่ใช้ pip เท่านั้น ให้ควบคุมเซ็นเซอร์ผ่าน [`connect_daq_sensor()`](#daq-sensor-sessions) แทน ซึ่งไม่ต้องการไลบรารีการขนส่งท้องถิ่น

```python
from chloros_sdk import DAQUSensor, DAQMSensor, DAQESensor, discover_all

# Discovery
for d in discover_all(timeout=3.0):
    print(d.model, d.display, d.address)   # USB serials: d.extra.get("serial_number")

# Direct DAQ-U
sensor = DAQUSensor(port="COM3")
sensor.connect()
sensor.start_streaming()
# ... use sensor.add_spectrum_callback(...) ...
sensor.stop()
```

ควรเลือกเส้นทาง smart-connect (`connect_daq_sensor`) เมื่อต้องการแบ่งปันสิทธิ์การเป็นเจ้าของกับ GUI; ใช้คลาส direct สำหรับสคริปต์แบบ headless ที่ถือสิทธิ์การเป็นเจ้าของเซ็นเซอร์อย่างเอกสิทธิ์

---

## การอัตโนมัติของโครงการ — `ChlorosProject`

โครงการChlorosที่บันทึกไว้คือโฟลเดอร์ที่ประกอบด้วย `cameras.json` + `sensors.json` + `project.json`. `open_project` จะโหลดไฟล์แมนิเฟสต์ และ `connect_all` จะนำอุปกรณ์ที่บันทึกไว้ทั้งหมดกลับมาออนไลน์พร้อมการตั้งค่าที่บันทึกไว้ — ในสภาพฮาร์ดแวร์ที่เหมือนกับที่ GUI จะสร้างขึ้น

### ตัวอย่างขั้นต่ำ

```python
import chloros_sdk

proj = chloros_sdk.open_project("/home/user/Chloros Projects/Field_A")
report = proj.connect_all(verbose=True)
print(report)  # {'cameras': {...}, 'arrays': {...}, 'sensors': {...}}

# Cameras and arrays are addressable by name OR serial / array_id
cam = proj.cameras["FrontLeft"]
cam.capture("./out", format="tiff", processing="reflectance")

arr = proj.arrays["main_rig"]
arr.capture("./out", format="tiff", processing="reflectance")

# Read a DAQ
spectrum = proj.sensors["Sky"].read()

# Trigger every device simultaneously
proj.capture_all("./out")

proj.disconnect_all()
```

หรือในฐานะผู้จัดการบริบท:

```python
with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    proj.arrays["main_rig"].capture("./out", processing="reflectance")
```

### `ChlorosProject` Methods

| Method | Description |
| --- | --- |
| `connect_all(cameras=True, arrays=True, sensors=True, verbose=False, align=None)` | ค้นหาและเชื่อมต่ออุปกรณ์ทุกเครื่องที่บันทึกไว้ คืนรายงานการเชื่อมต่อตามแต่ละคลาส ใช้แบ็กเอนด์ที่กำลังทำงานอยู่เมื่อมีแบ็กเอนด์หนึ่งตัวกำลังรับฟังบน `127.0.0.1:5000`; หากไม่มี จะเปลี่ยนไปใช้การควบคุมอุปกรณ์แบบตรง (ไม่มีแบ็กเอนด์) `lattice_sdk` โดยไม่แจ้งเตือน — ไม่เคยสร้างแบ็กเอนด์ขึ้นมาเลย |
| `disconnect_all()` | ยกเลิกการเชื่อมต่อทุกสิ่ง |
| `capture_all(output_dir=".")` | 1 เฟรมจากกล้องทุกตัว + อาร์เรย์ + สเปกตรัมจากเซ็นเซอร์ทุกตัว |
| `stream(camera, overlays=False, fps=10.0)` | ผลผลิตของเครื่องกำเนิดเฟรม BGR `numpy` จากกล้องที่ระบุชื่อ (หรืออาร์เรย์) `overlays=False` เป็นลูปการจับภาพ `lattice_sdk` แบบตรง (อาร์เรย์ให้ผลลัพธ์เป็น `{serial: frame}` dicts) `overlays=True` ส่งผ่าน `ChlorosLocal.camera_stream()` → ฟีด MJPEG ของ `/api/camera/<serial>/stream-annotated` จากแบ็กเอนด์ โดยส่งบล็อก `ui.overlay` ที่บันทึกไว้ของกล้องผ่านเป็นพารามิเตอร์การค้นหา ต้องใช้โหมดแบ็กเอนด์และ **กล้องแบบสแตนด์อโลน**: กล้องในโหมดตรงจะส่งสัญญาณ `RuntimeError` (แบ็กเอนด์ไม่สามารถรับกล้องที่กระบวนการนี้เป็นเจ้าของได้) และอาร์เรย์จะส่งสัญญาณ `NotImplementedError` (ซ้อนทับภาพรวมต่อกล้อง — สตรีมสมาชิกตามชื่อ) เทียบเท่าแบบครั้งเดียว: `CameraHandle.capture(annotated=True)`. |
| `align_arrays(align=True, verbose=False)` | ดำเนินการจัดแนว (alignment) บนทุกอาร์เรย์ที่เชื่อมต่ออยู่ในขณะนี้ |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | ดำเนินการปรับเทียบ / ดัชนี (calibration / index pipeline) บนภาพของโครงการ(ห่อหุ้ม `ChlorosLocal.process`; สี่ตัวนี้คือ **เพียง** kwargs ที่ได้รับการยอมรับ — `indices=` เป็นต้น จะทำให้เกิดข้อผิดพลาด `TypeError`; ตั้งค่าดัชนีผ่าน `ChlorosLocal.configure()`). สร้าง `ChlorosLocal()` แบบล่าช้า ซึ่งจะเริ่มต้น backend โดยอัตโนมัติ |

คุณสมบัติ:
- `proj.cameras` — `Dict[str, CameraHandle]` ใช้ชื่อและหมายเลขซีเรียลเป็นคีย์
- `proj.arrays` — `Dict[str, ArrayHandle]` ใช้ชื่อและ array_id เป็นคีย์
- `proj.sensors` — `Dict[str, SensorHandle]` ใช้ชื่อและ slot_id เป็นคีย์
- `proj.config` — `project.json["config"]` แบบพจนานุกรม

### `CameraHandle`

```python
cam = proj.cameras["FrontLeft"]

# Save a frame to disk (processing-aware)
filepath = cam.capture(
    output_dir="./out",
    format="tiff",
    processing="radiance",           # see the level table below
    apply_calibration=True,          # DSNU + flat + 3x3 unmix + NIST
    apply_white_balance=True,        # DLS-aware WB
    apply_index=False,
    index_expression=None,
)

# In-memory grab (numpy array)
frame = cam.grab(processing="debayered")
frame, header = cam.grab(processing="radiance", with_metadata=True)

# Frame iterator (generator)
for arr in cam.frame_stream(processing="debayered", fps=5, count=100):
    my_analysis(arr)
```

**ระดับการประมวลผล** `capture()`, `grab()` และ `frame_stream()` ทั้งหมดใช้โทเคน `processing`
เดียวกัน และ ห่วงโซ่นี้ทำงานแบบสะสม — แต่ละระดับจะประมวลผลทุกขั้นตอนที่อยู่เหนือมัน:

| ระดับ | ผลลัพธ์ | หมายเหตุ |
| --- | --- | --- |
| `raw` | Bayer 1 ช่อง, ตามค่าเริ่มต้นของเซ็นเซอร์ | ไม่มีการถอดโมเสก. ไม่สามารถใช้การซ้อนทับ (overlays) ได้ในระดับนี้ |
| `debayered` | BGR 3 ช่อง (**ค่าเริ่มต้น**) | การถอดโมเสกแบบ bilinear. ระดับเดียวที่ทำงานได้โดยไม่ต้องใช้โหมด backend. |
| `radiance` | float32, W/m²/sr/nm | ห่วงโซ่รังสีวัดเต็มรูปแบบ: การถอดโมเสก + การแยกสีแบบ 3×3 (multispec) + DSNU + flat-field + NIST scale, โดยแบ่งค่าการเปิดรับแสง × ค่าการขยายออกเพื่อให้ค่าเป็นค่าสัมบูรณ์ |
| `reflectance` | uint16, 32768 = 1.0 | Radiance แบ่งด้วย irradiance ที่ส่องลง (ρ = π·L/E). ต้องใช้ค่าอ่านจาก DLS/DAQ — ดูหมายเหตุด้านล่าง |
| `display` | 8-bit sRGB-ish | การเรนเดอร์เทียบเท่า GUI: CCM + white balance + gamma ผ่านโปรไฟล์สีที่ใช้งานอยู่ของกล้อง |

สิ่งอื่นใดนอกจาก `debayered` ต้องใช้โหมดแบ็กเอนด์; กล้องในโหมดตรงจะส่ง
`NotImplementedError`. `reflectance` ต้องการค่าการวัดแสงลงที่ใช้งานได้ — จุดสิ้นสุดของเฟรมจะดึง
ข้อมูล DAQ ที่รวมไว้เข้าสู่สล็อต DLS ของกล้องโดยอัตโนมัติ แต่หากไม่มี DAQ ที่ผูกไว้ โซ่จะปฏิเสธ
ทางออกของการสะท้อนแสง และจะบันทึกการลดระดับลงในเมตาดาต้าที่ส่งคืนอย่างตรงไปตรงมา แทนที่จะ
ส่งคืนผลิตภัณฑ์ที่มีคุณภาพต่ำกว่าอย่างเงียบๆ

> **สเกล DN ของการสะท้อน — อย่ากำหนดค่าคงที่** LATTICE reflectance ใช้ `32768` = ρ 1.0 และบันทึก
> XMP `Chloros:PixelScale=32768`; Survey3 reflectance ใช้ `65535` = ρ 1.0 และไม่
> มีแท็ก `Chloros:*` อ่านแท็กและหารด้วยค่านั้น มันถูกกำหนดในโดเมน uint16 ดังนั้นจึงคงอยู่
> `32768` สำหรับทุกรูปแบบที่ปรับสเกลใหม่ (TIFF 16 บิต, PNG 8 บิต /JPG, เปอร์เซ็นต์ 32 บิต) — ปรับให้
> ประเภทข้อมูลที่เก็บไว้กลับเป็น uint16 ก่อน (×257 จาก 8 บิต, ×65535 จาก float). ข้อยกเว้นเดียว:
> การจับภาพจากแหล่งที่มา 8-bit ที่เขียนเป็น 8-bit TIFF จะถูก *ตัดขอบ* (clipped) ไม่ใช่ปรับขนาดใหม่ ดังนั้นจึงไม่มีค่าสเกลใดอธิบาย
> ได้ — Chloros จะละเว้น `PixelScale` และ tuple ของ MicaSense ทั้งหมดในกรณีนั้น ให้ถือว่า
> แท็กที่ขาดหายไปในไฟล์สะท้อนแสง LATTICE เป็น &quot;ไม่มีสเกลที่ถูกต้อง&quot; ไม่ใช่ค่าเริ่มต้น

> **EXIF ถูกส่งต่อไปยังไฟล์ที่ส่งออก** `process()` คัดลอกบล็อก GPS ของการจับภาพต้นทาง
> **และ ExifIFD ของมัน** ไปยังทุกผลิตภัณฑ์ ดังนั้นไฟล์ที่ส่งออกจึงมี `FocalLength`, `FNumber`,
> `ExposureTime`, `ISO`, `DateTimeOriginal` และ `CameraSerialNumber` รวมถึง
> การอ้างอิงตำแหน่งทางภูมิศาสตร์ด้วย `FocalLength` คือค่าที่ Pix4D ใช้ในการคำนวณระยะตัวอย่างพื้นดิน — หากไม่มีค่านี้
> การสร้างแบบจำลองจะกลับไปใช้มาตราส่วนที่ผิดพลาดอย่างรุนแรง (ในกรณีที่วัดได้ พื้นที่ขนาด 411 ม.
> ถูกแปลงเป็น 47.8 กม.) ไฟล์ที่คัดลอกนี้ไม่ได้ตั้งชื่อเป็น `-all:all` โดยเจตนา: แท็กโครงสร้างของ IFD0 จะขัดขวาง
> ผลลัพธ์ LATTICE และ `ExifImageWidth`/`Height` ถูกยกเว้นเพราะพวกมันอธิบายการ
> จับภาพต้นทางแทนที่จะเป็นภาพแรสเตอร์ที่ส่งออก

การจับภาพ- แฟล็กย่อยของขั้นตอน (ใช้กับระดับรังสี — `radiance`, `reflectance`, `display`):

| แฟล็ก | ค่าเริ่มต้น | ความหมาย |
| --- | --- | --- |
| `apply_calibration` | `True` | DSNU + flat-field + 3x3 unmix + NIST radiometric scale. |
| `apply_white_balance` | `True` | WB LUT. รองรับ DLS เมื่อ DAQ ถูกผูกกับกล้อง |
| `apply_index` | `False` | การประเมินดัชนีพืชพรรณ |
| `index_expression` | `None` | สูตรการแทนที่ | ไม่ว่าง → เปิดใช้งานดัชนีอัตโนมัติ |
| `annotated` | `False` | การซ้อนทับการตกแต่ง GUI (ลายม้าลาย/กริด/การเน้นจุดสูงสุด) ไม่สามารถใช้ได้กับ `raw` |

### `ArrayHandle`

```python
arr = proj.arrays["main_rig"]

# Single synced capture group
files = arr.capture("./out", format="tiff", processing="reflectance")
# → {"213800234": "/path/to/x.tif", "214000533": "/path/to/y.tif", ...}

# Multi-level: each serial's value becomes an ordered LIST, not a str
files = arr.capture("./out", processing="all")
# → {"213800234": ["/raw.tif", "/debayered.tif", ...], "combined": "/idx.tif"}

# Smart capture (wait for AE to settle)
result = arr.capture_smart(
    "./out", processing="reflectance",
    settle_timeout_s=5.0,
    stability_window_s=1.5,
    exposure_tolerance_pct=5.0,
)
print(result["frames"], result["settle"])

# In-memory grab: {serial: numpy array}
frames = arr.grab(processing="debayered")
frames = arr.grab(processing="radiance", with_metadata=True)

# Stream-to-disk loop
arr.stream(count=60, output_dir="./stream", fps=5, processing="raw")

# Frame-iterator (tolerates per-cam drops; great for downstream analysis pipelines)
for frames in arr.frame_stream(processing="radiance", fps=5, count=100):
    if "213800234" in frames:
        my_analysis_pipeline(frames["213800234"])

# Preview iterator (live MJPEG-equivalent; tolerates partial cycles)
counts = arr.preview_stream("./preview", fps=3.0, duration=30.0)
print(counts)  # frames written per serial
```

> **ประเภทค่าที่คืนมาคือ `CapturePathMap` ไม่ใช่ `Dict[str, str]`.**
> `chloros_sdk.CapturePathMap` คือ `Dict[str, Union[str, List[str]]]`: แบบระดับเดียว
> `processing` ให้เส้นทางหนึ่งสำหรับแต่ละหมายเลขซีเรียล ส่วนแบบหลายระดับ (`"all"` หรือ
> รายการ `levels` ที่ระบุไว้อย่างชัดเจน) จะให้ **รายการเรียงลำดับ** ของผลิตภัณฑ์ทุกชิ้นที่บันทึกไว้สำหรับ
> กล้องนั้น ภาพรวมแบบสดที่รวมกัน (หากมีการสตรีม) จะปรากฏภายใต้คีย์
> `"combined"` แทนที่จะอยู่ภายใต้หมายเลขซีเรียล โค้ดที่สมมติว่า `str` จะเกิดข้อผิดพลาดเมื่อใช้
> รูปแบบรายการ โดยไม่มีตัวตรวจสอบประเภทใดคัดค้าน — คำอธิบายประกอบระบุว่า `Dict[str, str]`
> เป็นระยะเวลาหนึ่งหลังจากรูปแบบรายการถูกปล่อยออกมา ซึ่งเป็นเหตุผลที่ alias นี้มีอยู่ ให้ปรับให้เป็นมาตรฐาน
> เมื่อต้องการรูปแบบแบน:
>
> ```python
> paths = arr.capture(processing="all")
> flat = [p for v in paths.values()
>         for p in (v if isinstance(v, list) else [v])]
> ```

### การจัดแนวอาร์เรย์

`ArrayHandle` เปิดเผยพื้นผิวการจัดแนวทั้งหมด โปรไฟล์มีผลเฉพาะในเซสชันตามค่าเริ่มต้น — เรียก `export_alignment()` อย่างชัดเจนเพื่อเก็บรักษา

```python
from chloros_sdk import AlignmentSpec

arr = proj.arrays["main_rig"]

# Defaults: ORB / affine / one synced snapshot — same as the GUI's auto-cal
result = arr.calibrate_alignment()
print(result["profile"]["rms_residual_px"])

# Custom spec for tough scenes (low-contrast canopy)
spec = AlignmentSpec(
    method="feature_orb",         # feature_orb / feature_akaze / phase_correlation / checkerboard / manual
    model="rigid",                # translation / rigid / affine / homography
    num_frames=5,
    max_features=8000,
    ratio_threshold=0.7,
    ransac_threshold_px=2.0,
    min_matches=30,
    max_reproj_err_px=2.0,
)
arr.calibrate_alignment(spec)

# Or tweak one knob at a time
arr.calibrate_alignment(num_frames=3, model="affine")

# Inspect / manipulate
status = arr.alignment_status()
arr.tweak_alignment("214701292", dx=2.5, dy=-1.0, rotation_deg=0.0, scale=1.0)
arr.export_alignment("/tmp/main_rig_alignment.json")
arr.import_alignment("/tmp/main_rig_alignment.json", validate=True)
arr.clear_alignment()
```

#### การจัดแนวขณะเชื่อมต่อ

`connect_all(align=...)` สามารถจัดแนวทุกอาร์เรย์โดยอัตโนมัติขณะเชื่อมต่อ:

```python
# Align every array with defaults
proj.connect_all(align=True)

# Per-array control
proj.connect_all(align={
    "main_rig": AlignmentSpec(num_frames=5, model="affine"),
    "side_rig": True,             # use defaults
    "verify_rig": False,          # skip
})
```

จะกลับใช้ `project.json["config"]["auto_align_on_connect"]` เมื่อไม่มีการระบุ

### `SensorHandle`

```python
spectrum = proj.sensors["Sky"].read()
# (spectrum_list, is_saturated, integration_time, x, y, z) — matches the
# daq_sdk add_spectrum_callback signature.
```

---

## ฮาร์ดแวร์โดยตรง (ไม่มีแบ็กเอนด์)

เมื่อต้องการไม่พึ่งพาแบ็กเอนด์เลย (CI, หุ่นยนต์แบบไม่มีหน้าจอ, ระบบฝังตัว) ให้นำเข้า `lattice_sdk` และ `daq_sdk` โดยตรง — ทั้งสองถูกส่งออกใหม่โดย `chloros_sdk` หมายเหตุเกี่ยวกับ `CAMERA_AVAILABLE` / `DAQ_AVAILABLE`: `lattice_sdk` อยู่ในแพ็กเกจ PyPI (แต่ต้องมี runtime Arena SDK อยู่ด้วย), ส่วน `daq_sdk` มาพร้อมกับเวอร์ชันติดตั้งบนเดสก์ท็อปเท่านั้น

```python
from chloros_sdk import (
    # cameras
    LatticeCamera, CameraSettings, PRESETS, CameraPool,
    Calibration, CalibrationCoefficients, FilterModel, list_filters,
    DLS, NetworkDiagnostics, gpu_info, gpu_available,
    # discovery
    discover_cameras, discover_cameras_via_backend,
    # exceptions
    LatticeError, CameraNotFoundError, StreamError, CaptureError,
    CalibrationError, NetworkError, DLSError,
)

# Find a camera and capture in one go
cams = discover_cameras(timeout_ms=3000)
print(cams)

settings = PRESETS["high_quality"]
with LatticeCamera(serial="213800234", settings=settings) as cam:
    result = cam.capture(output_dir="./out", format="tiff")
    print(result.filepath, result.width, result.height)
```

##### การตั้งค่าล่วงหน้าและตัวทริกเกอร์

สามในสี่การตั้งค่าล่วงหน้า **free-run**: กล้องจะเปิดรับแสงอย่างต่อเนื่อง และ
`capture()` จะส่งเฟรมถัดไปกลับมา `triggered` เป็นข้อยกเว้น — มันจะตั้งกล้องให้พร้อม
รับสัญญาณขอบฮาร์ดแวร์ที่ Line 2 ดังนั้นจึงไม่บันทึกภาพใดๆ จนกว่าสัญญาณจะมาถึง

| การตั้งค่าล่วงหน้า | ตัวกระตุ้น | ใช้เมื่อ |
| --- | --- | --- |
| `default` | free-run | การใช้งานทั่วไป |
| `high_speed` | free-run | 8 บิต, จำกัดที่ 60 fps, เวลาเปิดรับแสงสั้น |
| `high_quality` | free-run | 12 บิต, ไม่จำกัด fps — ตัวเลือกทั่วไปสำหรับภาพนิ่ง |
| `triggered` | **เปิดใช้งาน, Line 2** | กล้องถูกต่อเข้ากับสายซิงค์ M8 และมีอุปกรณ์อื่นที่สั่งให้กล้องทำงาน |

หากคุณเลือก `triggered` (หรือตั้งค่า `trigger_mode="On"` ด้วยตัวเอง) โดยไม่มีอะไร
ควบคุม Line 2 ทุก `capture()` จะหมดเวลา — ซึ่งถูกต้อง เพราะคุณได้
สั่งให้กล้องรอ SDK อธิบายเรื่องนี้เมื่อมันเกิดขึ้น; ดู
[SC_ERR_TIMEOUT ระหว่างการจับภาพ](#direct-hardware-backend-free).

> **หมายเหตุ — ข้อความ &quot;GVSP probe&quot; / `SC_ERR_TIMEOUT -1011` เมื่อเชื่อมต่อไม่ใช่ข้อผิดพลาด**&gt; เมื่อเชื่อมต่อ SDK จะพยายามเจรจา**jumbo frames** (แพ็กเก็ต GVSP 9000 ไบต์) เพื่อเพิ่มปริมาณข้อมูลที่ส่งผ่านได้ บนลิงก์ NIC แบบจุดต่อจุดโดยตรง (เช่น ที่อยู่ `169.254.x.x` แบบ link-local) เครือข่ายมักไม่สามารถรองรับเฟรมจัมโบ้ได้ ดังนั้นการตรวจสอบนี้จะหมดเวลาและบันทึกบรรทัดเช่น:
>
> ```
> [Network] GVSP probe: unexpected error (TimeoutError: ... SC_ERR_TIMEOUT -1011)
> [Network] GVSP probe at 9000 did not deliver a complete buffer; reverting to ICMP-chosen size
> [Network] GVSP packet size: 1500 bytes (standard)
> ```
>
> นี่คือ **กลไกสำรองที่ออกแบบไว้**: SDK จะกลับสู่แพ็กเก็ตมาตรฐาน 1500 ไบต์โดยอัตโนมัติ และกล้องจะยังคงเชื่อมต่อได้ตามปกติ (บรรทัด `[chunk-enable …]` ที่ตามมาคือส่วนหนึ่งของลำดับการเชื่อมต่อปกติ) การบันทึกภาพยังคงทำงานได้
>
> คุณสามารถข้ามการตรวจสอบนี้ได้ แต่ **มันไม่ใช่แค่เครื่องมือปิดเสียงบันทึก — มันปิดการใช้งานเฟรมจัมโบ้** กล้องจะตอบกลับ Don&#x27;t-Fragment pings ได้เพียงสูงสุด 1500 ไบต์ ไม่ว่าเครือข่ายของคุณจะดีเพียงใด ดังนั้นการทดสอบ ping เพียงอย่างเดียวจึงไม่สามารถตรวจพบ jumbo ได้เลย; การตรวจสอบนี้คือสิ่งเดียวที่สามารถทำได้ หากปิดมันลง กล้องจะส่งแพ็กเก็ตขนาดมาตรฐาน 1500 ไบต์ตลอดไป บนเครือข่ายใดก็ตาม:
>
> ```bash
> CHLOROS_GVSP_PROBE_FALLBACK=0   # gives up jumbo — see the warning it prints
> ```
>
> ควรใช้เฉพาะบนเครือข่ายที่คุณ *รู้แน่ชัด* ไม่สามารถรองรับแพ็กเก็ตจัมโบได้ ซึ่งจะช่วยประหยัดเวลาการเชื่อมต่อประมาณหนึ่งวินาทีต่อกล้อง เนื่องจากนี่เป็นการแลกเปลี่ยนที่แท้จริง ไม่ใช่เพียงการเปลี่ยนแปลงทางรูปแบบเท่านั้น ดังนั้น SDK จึงจะแจ้งให้ทราบเมื่อคุณใช้งาน:
>
> ```
> [Network] ⚠️ GVSP probe disabled (CHLOROS_GVSP_PROBE_FALLBACK=0) — staying at
> 1500 bytes, jumbo NOT tested. … if this network does carry it, you are giving
> up ~1.45x wire ceiling. Unset the variable to test for jumbo.
> ```
>
> **อย่าไปแตะต้องมัน เว้นแต่คุณมีเหตุผลที่ชัดเจน** หากเปิดใช้งานไว้ ระบบจะวัดเครือข่ายที่คุณใช้งานอยู่ใหม่ทุกครั้งที่เชื่อมต่อ: เมื่อต่อเข้ากับสวิตช์ที่รองรับแพ็กเก็ตจัมโบ้ การเชื่อมต่อครั้งต่อไปจะตรวจจับแพ็กเก็ตจัมโบ้ได้โดยอัตโนมัติ โดยไม่ต้องตั้งค่าเพิ่มเติมหรือรีสตาร์ท
>
> หากคุณ *ต้องการ* ความเร็วการส่งข้อมูลแบบจัมโบ ให้เปิดใช้งานจัมโบแบบ end-to-end (NIC MTU 9000 + สวิตช์ที่ส่งผ่านได้), หรือกำหนดขนาดคงที่ด้วย `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` เมื่อคุณทราบว่าลิงก์นั้นรองรับ — แต่ควรใช้ `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 python …` แบบต่อคำสั่งแทนการตั้งค่าถาวร เนื่องจากขนาดที่กำหนดคงที่จะข้ามขั้นตอนการตรวจสอบและหยุดการปรับตัวให้เข้ากับเครือข่ายด้านหน้า ****ทุก** อุปกรณ์ในเส้นทางต้องสามารถส่งแพ็กเก็ตจัมโบได้ — รวมถึงตัวแยกหรือตัวฉีด PoE ซึ่งมักเป็นสาเหตุหลักที่ทำให้ระบบที่ปกติรองรับจัมโบไม่สามารถส่งแพ็กเก็ตจัมโบได้

> **`SC_ERR_TIMEOUT -1011` ระหว่าง `capture()` / `grab*()` เป็นปัญหาที่ต่างออกไป — ข้อผิดพลาดนั้นเป็นข้อผิดพลาดจริง**&gt; หมายเหตุด้านบนเกี่ยวข้องเฉพาะกับ `-1011` ที่บันทึกโดย**connect-time probe**เท่านั้น ส่วนข้อผิดพลาดเดียวกันที่เกิดขึ้นจาก**capture** หมายความว่ากล้องเชื่อมต่อได้ปกติ แต่ไม่ส่งภาพใดๆ:
>
> ```
> File ".../lattice_sdk/camera.py", line ..., in grab_frame_with_metadata
>   buffer = self._get_buffer(timeout)
> lattice_sdk.exceptions.CaptureError: Capture failed: ... SC_ERR_TIMEOUT -1011
> ```
>
> จุดสังเกตคือกล้องที่มีช่อง *control* ทำงานปกติ — การค้นพบทำงานได้, การตั้งค่าและ `[chunk-enable …]` เขียนข้อมูลสำเร็จทั้งหมด — แต่ *ทุก* เฟรมเกิดการหมดเวลา
>
> **สาเหตุทั่วไปคือกล้องถูกตั้งค่าให้ทำงานเมื่อมีสัญญาณทริกเกอร์จากฮาร์ดแวร์** ด้วย `trigger_mode="On"` และ `trigger_source="Line2"`, กล้องจะไม่ส่งข้อมูลใดๆ เลยจนกว่าจะมีสัญญาณไฟฟ้ามาถึงบนสายซิงค์ M8 หากไม่มีสายที่ส่งสัญญาณไปยังสายนั้น การจับภาพทุกครั้งจะรอไปเรื่อยๆ กล้องไม่ได้เสียหายและเครือข่ายก็ทำงานปกติ — มันกำลังทำตามคำสั่งที่ได้รับอย่างถูกต้อง
>
> `CameraSettings()` และ `default` / `high_speed` / `high_quality` ตั้งค่าล่วงหน้าให้ทำงานแบบอิสระ (free-run) และการจับภาพที่จับเวลา เมื่ออยู่ในสถานะพร้อมใช้งานจะแสดงข้อความอธิบายแทนที่จะพิมพ์เพียง `-1011` `PRESETS["triggered"]` จะเปิดใช้งาน Line2 ตามการออกแบบ
>
> เพื่อบังคับให้กล้องใดก็ตามทำงานในโหมดฟรีรัน:
>
> ```python
> settings = PRESETS["high_quality"]
> settings.trigger_mode = "Off"        # free-run; don't wait for an M8 edge
> ```
>
> หากยังเกิดการหมดเวลาเมื่อใช้ `trigger_mode="Off"` หมายความว่ากล้องไม่ได้ส่งข้อมูลมาจริง — ส่งไฟล์บันทึกและ `ip link show` ให้เรา

#### โปรไฟล์สี (การดูตัวอย่างสดของ RGB) — `set_color_profile`

`LatticeCamera.set_color_profile(profile, custom_cct_k=None)` เลือกโปรไฟล์สีการแสดงผลสำหรับ **การดูตัวอย่างสด** บนกล้อง RGB (กล้องหลายสเปกตรัมจะไม่สนใจการตั้งค่านี้):

| โปรไฟล์ | ความหมาย |
| --- | --- |
| `raw` | ข้ามห่วงโซ่การวัดรังสีทั้งหมด | |
| `linear` | DSNU + flat + WB, ไม่มี CCM, ไม่มี gamma. |
| `natural` | Linear + CCM ที่วัดได้ + gamma sRGB, พร้อมการปรับแต่งขั้นสุดท้ายแบบประหยัดเท่านั้น (การปรับความเรียบของสี + การลดความอิ่มตัวของส่วนไฮไลท์) — ค่าเริ่มต้นที่สมจริง |
| `enhanced` | `natural` พร้อมการปรับแต่งแบบ hub-parity แบบเต็ม (defringe, vibrance, CLAHE local contrast). ดูมีสีสันมากขึ้นที่ประมาณ **สองเท่าของค่าใช้จ่ายต่อ**ค่าใช้จ่ายในการปรับแต่งต่อเฟรม** ดังนั้นอัตราเฟรม LIVE จะต่ำลง |
| `custom_temp` | `natural` แต่ WB ถูกตรึงไว้ที่ค่า Kelvin ของ `custom_cct_k` (DLS ถูกเพิกเฉย; ถูกจำกัดไว้ที่ 2000–10000 K ด้านหลัง). |

โปรไฟล์นี้เป็น **ปุ่มปรับความเร็ว/รูปลักษณ์สำหรับดูตัวอย่างสดเท่านั้น**: ภาพที่บันทึกไว้จะได้รับการปรับแต่งคุณภาพอย่างเต็มรูปแบบเสมอ ไม่ว่าจะเลือกโปรไฟล์ใด ดังนั้นการเลือก `natural` เพื่อประหยัดเวลาเฟรมจะไม่ทำให้คุณภาพของภาพที่บันทึกบนดิสก์ลดลง โปรไฟล์ที่ไม่รู้จักจะเรียกใช้ `ValueError`; เมื่อสามารถเข้าถึง backend chloros ได้ การเปลี่ยนแปลงจะถูกส่งไปยังมันผ่าน POST เพื่อให้เฟรมตัวอย่างถัดไปสะท้อนการเปลี่ยนแปลงนั้น (ผู้ใช้ direct-SDK ที่ไม่มี backend ยังคงได้รับการเปลี่ยนแปลงการตั้งค่า)

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_color_profile("enhanced")            # richer look, lower LIVE fps
    cam.set_color_profile("custom_temp", custom_cct_k=5600)
```

#### กล้อง Mono (M3M) และ `Calibration`

กล้อง Mono **M3M** (`M3M-<lens>-F<wavelength>`) เป็นกล้องแบบแถบเดียว: มีระนาบสีเทาเดียว ไม่มีโมเสก Bayer และไม่มีเมทริกซ์สเปกตรัม 3×3-crosstalk matrix. `Calibration` สามารถระบุได้และแสดง flag `is_mono` การสะท้อนแสงยังคงถูกใช้ในฐานะแผนที่รังสีวัดค่าต่อแต่ละแถบ (การแยกสีเป็นเมทริกซ์เอกลักษณ์), แต่การคำนวณหลายแถบบนกล้องเดียวจะให้ผลลัพธ์ที่เพิ่มขึ้นแทนที่จะให้ค่าผิดปกติ:

```python
from chloros_sdk import Calibration, CalibrationError

calib = Calibration("M3M-L87-F685")
print(calib.is_mono)        # True  (False for any M3C / RGN Bayer cam)
print(calib.filter_type)    # 'mono'  (sentinel; not a real crosstalk key)

# NDVI needs two bands (Red + NIR); one mono band can't supply both.
try:
    calib.compute_ndvi(reflectance_frame)
except CalibrationError as e:
    print(e)   # "...single-band mono (M3M) camera. Combine multiple..."
```

เพื่อสร้างดัชนีพืชพรรณจากอุปกรณ์ภาพเดียว ให้รวมกล้อง M3M หลายตัวที่ความยาวคลื่นต่างกันเป็นชุดภาพหลายแถบที่จัดแนวแล้ว (ดู [การจัดแนวอาร์เรย์](#array-alignment)) และคำนวณดัชนีบนชุดภาพนั้นแทนที่จะคำนวณบนกล้องเดียว

โหมดตรง DAQ:

```python
from chloros_sdk import (
    DAQUSensor, DAQMSensor, DAQESensor,
    SensorFleet, discover_all, DiscoveredSensor,
    apply_sensor_settings, SensorSettings,
)

for d in discover_all(timeout=3.0):
    print(d)

sensor = DAQUSensor(port="COM3")
sensor.connect()
apply_sensor_settings(sensor, settings={"integration_time_ms": 64, "frame_avg": 20})
sensor.start_streaming()
# ... sensor.add_spectrum_callback(your_callback) ...
sensor.stop()
```

> **`apply_sensor_settings` กุญแจที่ได้รับการยอมรับ**— คือ `integration_time_ms`, `frame_avg`, `ae_enabled`, `sunshine_diffuser_installed` (DAQ-E; ไม่แนะนำให้ใช้แล้ว เนื่องจากถูกแทนที่ด้วย `cap_id`), `filter_model` (DAQ-M), และ `cap_id` (ทุกประเภท DAQ; `None`/`""`/`"none"` = เซนเซอร์เปล่า ไม่มีการปรับค่าแคป) กุญแจที่คีย์ที่รู้จัก**จะถูกเพิกเฉยโดยอัตโนมัติ** — เช่น `{"integration_time": 64}` ไม่ทำงานอะไร (ต้องเป็น `integration_time_ms`) คืนค่า `{"applied": [...], "errors": {...}}` และไม่ทำให้เกิดข้อผิดพลาดใดๆ

`chloros_sdk` ส่งออกใหม่เฉพาะพื้นผิวหลักที่ใช้ข้างต้นเท่านั้น ส่วน `daq_sdk` ที่เปิดเผยต่อสาธารณะทั้งหมด API (22 ชื่อ) เพิ่มสิ่งต่อไปนี้ — นำเข้าโดยตรงจาก `daq_sdk`:

```python
from daq_sdk import (
    DAQULogger, DAQMLogger, DAQELogger,     # rotating-file recorders (the ones the GUI uses)
    ConnectResult, FleetRecordResult,       # SensorFleet result types
    discover_all_detailed, build_sensor,    # detailed discovery + build-by-descriptor
    scan_eth_devices, DaqEControl,          # DAQ-E Ethernet scan + control channel
    scan_ble_devices, detect_ble_device, list_ble_devices,   # DAQ-M BLE discovery
    detect_port, list_serial_ports,         # DAQ-U serial-port discovery
    TcpSerial,                              # serial-over-TCP transport shim
)
```

---

## ข้อยกเว้น

จับคลาสฐานเพื่อจัดการกับ &quot;ปัญหาใดๆ ที่เกิดขึ้นChloros&quot;:

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

> `ChlorosAuthenticationError` และ `ChlorosConfigurationError` ถูกส่งออกในระดับบนสุดพร้อมกับส่วนที่เหลือ; นอกจากนี้ยังสามารถนำเข้าได้จาก `chloros_sdk.exceptions` ตามที่แสดงไว้

ลำดับชั้น:

```

ChlorosError
├── ChlorosBackendError           (backend failed to start / unreachable)
├── ChlorosConnectionError        (HTTP transport failure)
├── ChlorosLicenseError           (subscription / tier gate)
├── ChlorosAuthenticationError    (login required)
├── ChlorosConfigurationError     (bad configure() / open_project() inputs)
└── ChlorosProcessingError        (pipeline failed)

ChlorosConnectError                (raised by connect_camera / connect_array /
                                    connect_daq_sensor only — derives from
                                    plain Exception, NOT from ChlorosError,
                                    so `except ChlorosError` will not catch it)

lattice_sdk exceptions:
LatticeError
├── CameraNotFoundError
├── CameraConnectionError
├── StreamError
├── CaptureError
├── CalibrationError
├── NetworkError
└── DLSError
```

---

## ตัวอย่างแบบครบวงจร

### 1. ประมวลผลโฟลเดอร์ด้วยแถบความคืบหน้าที่กำหนดเอง

```python
from chloros_sdk import ChlorosLocal

def progress(percent, message):
    bar = "#" * (percent // 5)
    print(f"\r[{bar:<20s}] {percent:3d}% {message}", end="", flush=True)

with ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26")
    cl.import_images("C:/DroneImages/Flight001", recursive=True)
    cl.configure(
        debayer="High Quality (Faster)",
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "NDRE", "GNDVI", "SAVI"],
        export_format="TIFF (16-bit)",
    )
    cl.process(progress_callback=progress)
print()
```

### 2. มصفوفة LATTICE แบบเรียลไทม์ → ค่าสะท้อน + ค่าอ้างอิง DAQ

```python
import chloros_sdk

# Open a paired sensor first so the array's reflectance step has an
# absolute reference. Smart-detect picks USB / BLE / ETH automatically.
with chloros_sdk.connect_daq_sensor() as daq:
    with chloros_sdk.connect_array([
            "213800234", "214000533", "214701288", "214701292"
    ]) as arr:
        # Smart capture: wait for AE to settle, then snap
        arr.capture("./out", processing="reflectance", smart=True)

        # Record the corresponding DAQ frames as ground truth
        daq.record_start(output_dir="./out", device_name="sky-reference")
        # ... do whatever capture campaign ...
        info = daq.record_stop()
        print(info["path"], info["rows"])
```

### 3. แคมเปญการจับภาพที่ขับเคลื่อนด้วยโครงการ

```python
import time, chloros_sdk

with chloros_sdk.open_project("/home/user/Chloros Projects/Field_A") as proj:
    report = proj.connect_all(verbose=True, align=True)
    if report["arrays"]["errors"]:
        raise SystemExit(f"Array(s) failed to connect: {report['arrays']['errors']}")

    rig = proj.arrays["main_rig"]

    # Re-align right before the campaign
    rig.calibrate_alignment(num_frames=5)
    rig.export_alignment("./alignments/main_rig.json")

    # 50 sequential single-frame captures at 2 fps
    for i in range(50):
        frames = rig.capture(
            output_dir=f"./out/frame_{i:04d}",
            processing="reflectance",
            apply_calibration=True,
            apply_white_balance=True,
        )
        time.sleep(0.5)

    # End-of-day: process the captured folder. process() accepts only
    # mode/wait/progress_callback/poll_interval — indices come from the
    # project's saved config (or set them via ChlorosLocal.configure()).
    proj.process()
```

### 4. สตรีมเฟรมจากกล้องหลายตัว → ท่อประมวลผล NumPy

```python
import chloros_sdk
import numpy as np

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    rig = proj.arrays["main_rig"]

    for frames in rig.frame_stream(
            processing="radiance",
            fps=5.0, count=300,
            apply_calibration=True,
            apply_white_balance=True):
        # frames is {serial: numpy_array}; cams not delivering this tick are omitted
        for serial, frame in frames.items():
            print(serial, frame.shape, frame.dtype, frame.mean())
```

### 5. สคริปต์การจับภาพแบบ Headless Direct-Hardware (ไม่มี Backend)

```python
from chloros_sdk import LatticeCamera, PRESETS, discover_cameras

cams = discover_cameras(timeout_ms=3000)
print(f"Found {len(cams)} cams")

settings = PRESETS["high_quality"]
for c in cams:
    with LatticeCamera(serial=c.serial, settings=settings) as cam:
        result = cam.capture(output_dir="./out", format="tiff")
        print(c.serial, result.filepath)
```

### 6. การตรวจสอบความสามารถก่อนเชื่อมต่อระบบกล้อง 4 ตัว

```python
import chloros_sdk

serials = ["214701288", "213800234", "214000533", "214701162"]

probe = chloros_sdk.analyze_array_network(
    master_serial=serials[0],
    slave_serials=serials[1:],
    width=2048, height=1536,
    pixel_format="BayerRG12",
)

if probe["status"] == "ok":
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12")
elif probe["status"] == "auto_capped_fps":
    r = probe["recommended"]
    print(f"Keeping resolution; capping trigger rate at "
          f"{r['recommended_target_fps']} fps")
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12",
        target_fps=r["recommended_target_fps"])
elif probe["status"] == "auto_shrunk":
    r = probe["recommended"]
    print(f"Auto-shrinking to {r['out_width']}x{r['out_height']} "
          f"binning={r['binning']} for sim-sync")
    arr = chloros_sdk.connect_array(
        serials,
        width=r["out_width"], height=r["out_height"],
        pixel_format=r["pixel_format"], binning=r["binning"])
elif probe["status"] == "needs_force_slip":
    print("Wire can't sustain sim-sync; falling back to slip mode")
    arr = chloros_sdk.connect_array(
        serials, force_tier="slip-emit-and-capture")
else:
    raise RuntimeError(f"Probe error: {probe.get('error')}")
```

### 7. สูตรการบันทึกที่เทียบเท่า (Python แบบบริสุทธิ์)

สูตร DSL ของ CLI มีสูตรที่เทียบเท่าโดยตรงใน Python:

```python
import time, chloros_sdk

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    cam = proj.cameras["FrontLeft"]
    rig = proj.arrays["main_rig"]
    sky = proj.sensors["Sky"]

    # apply
    # (CameraHandle has no direct apply method; use the underlying lattice_sdk
    #  helper or the backend's /api/camera/<sn>/apply-settings via requests)
    # For most cases just use cam.cam.set_exposure(...) in direct mode or
    # the GUI's saved settings via project.connect_all().

    # wait
    time.sleep(2)

    # capture
    cam.capture("pose_a/", format="tiff", processing="radiance")

    # stream
    rig.stream(count=60, fps=5, output_dir="stream/", processing="raw")

    # sensor read
    print(sky.read())
```

---

## การเริ่มต้นระบบแบ็กเอนด์อัตโนมัติ

จุดเข้า smart-connect — `connect_camera`, `connect_array`, `connect_daq_sensor` และ `discover_lattice_cameras` — เป็นไคลเอนต์แบบบาง HTTP ที่สมมติว่าแบ็กเอนด์กำลังรับฟังอยู่ที่ `127.0.0.1:5000` (URL ค่าเริ่มต้นของ smart-connect surface) เมื่อ GUI หรือ CLI กำลังทำงานอยู่แล้ว ก็จะมีเซิร์ฟเวอร์แบ็กเอนด์หนึ่งตัว แต่หากเริ่มต้นจากสคริปต์เปล่า อาจไม่มี — ดังนั้นฟังก์ชันเหล่านี้ **จะเริ่มต้นไฟล์ไบนารีแบ็กเอนด์ที่มาพร้อมกันโดยอัตโนมัติ** (แบบไม่มีหน้าต่าง, เหมือนกับที่ `ChlorosLocal` ทำ) ก่อนการเรียกใช้ครั้งแรก แล้วรอจนกว่า `backend_startup_timeout` จะเริ่มทำงาน

กฎ:

- **จะสร้าง URL ท้องถิ่นเท่านั้น** `backend_url` ที่ชี้ไปยัง `localhost` / `127.0.0.1` / `[::1]` เท่านั้นที่ผ่านเกณฑ์; โฮสต์อื่นใดจะถูกถือว่าเป็นการใช้งานของเครื่องคนอื่น และจะไม่ถูกสร้างขึ้นเลย
- **แบ็กเอนด์จะถูกปล่อยให้ทำงานต่อเพื่อใช้ซ้ำ** (เหมือนกับ CLI) — ไม่มีการปิดระบบโดยอัตโนมัติเมื่อสคริปต์ของคุณสิ้นสุดการทำงาน การเรียกสคริปต์ใหม่จะใช้แบ็กเอนด์ที่ยังทำงานอยู่
- **เลือกไม่ใช้ด้วย `auto_start_backend=False`** ในการเรียกใด ๆ ของคำสั่งเหล่านี้ (เช่น เมื่อคุณชี้ไปยัง backend ระยะไกล หรือคุณจัดการวงจรชีวิตของ backend ด้วยตัวเอง)

```python
import chloros_sdk

# Fresh shell, no backend running, no GUI open — this still works:
with chloros_sdk.connect_camera("213800234") as cam:   # spawns the backend
    cam.capture("output/")

# Remote backend (via tunnel — see Remote-Backend Mode): don't spawn one locally
arr = chloros_sdk.connect_array(serials,
                                backend_url="http://127.0.0.1:5000",
                                auto_start_backend=False)
```

หากไม่สามารถค้นหาหรือเริ่มต้นไบนารีที่มาพร้อมกันได้ การเรียก HTTP ครั้งถัดไปจะสร้างข้อผิดพลาดที่สามารถดำเนินการได้ และ **ที่ตระหนักถึงแพลตฟอร์ม** `ChlorosConnectError` แทนที่จะเป็นการติดตามการเชื่อมต่อแบบเปล่า— บน Windows มันจะชี้ให้คุณไปยังแอปเดสก์ท็อปหรือคำสั่ง `chloros-cli`; บน Linux (ไม่มี GUI) มันจะชี้ให้คุณไปยังคำสั่ง `chloros-cli` หรือ `.deb`.

---

## สภาพแวดล้อมและเฮดเดอร์

SDK จะทำเครื่องหมายทุกการเรียกใช้ backend HTTP ด้วย `X-Chloros-Client: sdk` Backend นี้ใช้กฎการให้สิทธิ์ใช้งานตาม SDK / CLI (ต้องเข้าสู่ระบบ **และ** มีแผน Chloros+ ที่ชำระเงินแล้ว) แทนที่จะใช้เส้นทางระดับฟรีของ GUI การตั้งค่านี้จะถูกกำหนดอัตโนมัติเมื่อนำเข้าข้อมูล — คุณไม่จำเป็นต้องทำอะไรทั้งสิ้น

`http://localhost` และ `http://127.0.0.1` จะถูกตรวจพบเป็นระบบหลังบ้านท้องถิ่น การเรียกใช้ไปยังโฮสต์อื่น (เช่น บริการวิเคราะห์ข้อมูลของคุณเอง) จะไม่ถูกเปลี่ยนแปลง

สามารถแทนที่ backend URL ได้โดยการส่ง `backend_url=` (หรือ `api_url=` บน `ChlorosLocal`):

```python
chloros_sdk.connect_camera("213800234", backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_array(serials, backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                backend_url="http://127.0.0.1:5000")
chloros_sdk.ChlorosLocal(backend_url="http://127.0.0.1:5000")
```

(`backend_url` ที่ไม่ใช่ loopback จะเข้าถึงได้เฉพาะ backend แบบ source/dev เท่านั้น — backend ที่มาพร้อมกับระบบจะผูกกับ loopback เท่านั้น; ดู Remote-Backend Mode สำหรับรูปแบบการเชื่อมต่อแบบ tunnel)

---

## การกำหนดเวอร์ชันและความเข้ากันได้

- เวอร์ชันSDKถูกเปิดเผยเป็น `chloros_sdk.__version__`
- เวอร์ชันSDKจะกำหนดพฤติกรรมของพินตามเวอร์ชันของ backend ที่มาพร้อมชุด โดยทั่วไปการผสมผสานระหว่างเวอร์ชันเก่า SDK กับ backend ที่ใหม่กว่าจะทำงานได้ (endpoint ที่เข้ากันได้กับเวอร์ชันใหม่) แต่การผสมผสานระหว่างเวอร์ชันใหม่ SDK กับ backend ที่เก่ากว่าอาจทำให้เกิดข้อผิดพลาด `404` ที่จุดปลายทางใหม่ — อัปเกรดแอปเดสก์ท็อปให้สอดคล้องกัน
- พื้นผิว smart-connect (`connect_camera` / `connect_array` / `connect_daq_sensor`) และจุดสิ้นสุดการวิเคราะห์เครือข่ายจะส่งคืนสคีมา JSON ที่เสถียร; ฟิลด์ใหม่ เป็นข้อมูลเพิ่มเติม

---

## คำแนะนำในการแก้ไขปัญหา

- **`ChlorosAuthenticationError: Login required`** → ดำเนินการ `chloros-cli login EMAIL PASSWORD` ครั้งเดียวบนเครื่องนี้ หรือเข้าสู่ระบบผ่านแอปเดสก์ท็อป Chloros
- **`ChlorosConnectError: No Chloros backend is running …`** → การเรียกใช้ smart-connect จะเริ่มต้น backend ท้องถิ่นโดยอัตโนมัติ ดังนั้นข้อผิดพลาดนี้จะปรากฏขึ้นเฉพาะเมื่อไม่สามารถค้นหาหรือเริ่มต้นไบนารีที่มาพร้อมแพ็กเกจได้ (เช่น โฮสต์ที่ใช้ pip เท่านั้นโดยไม่มีแพ็กเกจเดสก์ท็อป) ข้อความนี้ปรับตามแพลตฟอร์ม: บน Windows ให้เปิดแอปเดสก์ท็อปหรือรันคำสั่ง `chloros-cli` ใดก็ได้; บน Linux ให้รันคำสั่ง `chloros-cli` (ไม่มี GUI) หรือติดตั้ง `.deb` สำหรับแบ็กเอนด์ระยะไกล ให้ส่ง `backend_url=` (และ `auto_start_backend=False`)
- **`CAMERA_AVAILABLE == False`** เมื่อนำเข้า → `lattice_sdk` ไม่สามารถโหลดได้ (โดยทั่วไป DLL ของ Arena SDK สำหรับ runtime ยังไม่ถูกติดตั้ง) พื้นผิวที่ไม่ใช่กล้องยังคงทำงานได้
- **Array connect ส่งคืนความละเอียด sub-ความละเอียดแบบเนทีฟ**→ ระบบ smart-prep ของแบ็กเอนด์จะปรับขนาดเฟรมให้เล็กลงอัตโนมัติเพื่อให้พอดีกับสาย ใช้ `analyze_array_network()` เพื่อดูสาเหตุ จากนั้นสามารถอัปเกรดลิงก์ ยอมรับการปรับขนาด หรือส่งผ่าน `force_tier="slip-emit-and-capture"` เพื่อการจับภาพแบบต่อเนื่อง. ระบบความปลอดภัยในการลดขนาด**ไม่** ครอบคลุมการสมัครเกินขีดจำกัดแบบรวม (`oversubscribed: true`, สนาม fps 0): จำนวนกล้องที่มากเกินไปสำหรับสายไม่สามารถแก้ไขได้ด้วยการรวมภาพ/ROI — ลดจำนวนกล้อง, เปิดใช้งานเฟรมจัมโบ้, หรือเปลี่ยนไปใช้ NIC ที่เร็วขึ้น (ดู [Over-Subscription](#over-subscription-the-per-cam-floor)).
- **`analyze_array_network()` รายงานว่าวงรับ (RX ring) ของ NIC มีขนาดเล็กมาก (~0.26 MB) / ประตูเชื่อมต่อแสดงข้อความ &quot;FRAMES WILL DROP&quot;** → วงรับ (receive ring) ของ NIC ฝั่งโฮสต์อยู่ในค่าเริ่มต้น (มักถูกรีเซ็ตเป็น 32 หลังอัปเดตไดรเวอร์ NIC) สำหรับอะแดปเตอร์ Realtek USB 10GbE ให้ตั้งค่า `ReceiveBufferLen=256` และ `PendingReceives=64` (ระดับสูง), จากนั้นรีสตาร์ทระบบแบ็กเอนด์เพื่อให้มันอ่านวงรับใหม่ ขั้นตอนทั้งหมด: [CLI Reference → Host NIC Setup &amp; Tuning](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **โฮสต์ค้างเมื่อรีสตาร์ท/ปิดระบบ, ต่อมาเกิดข้อผิดพลาด WMI `Invalid class` / NIC ไม่สามารถเปิดใช้งานได้** → ไดรเวอร์ USB 10GbE ที่ล้าสมัยก่อให้เกิดข้อผิดพลาด `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). อัปเดต ให้อัปเดตไดรเวอร์อะแดปเตอร์เป็นเวอร์ชันล่าสุด (≥ 2026) และตั้งค่า receive-ring ใหม่ ดู [CLI Reference → Host NIC Setup &amp; Tuning](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **Reflectance refused** → ต้องผูกระบบ DAQ ที่ทำงานแบบเรียลไทม์กับกล้อง (หรืออาร์เรย์) เพื่อวัดค่าความสะท้อนในสเกลสัมบูรณ์ สามารถผูกผ่าน GUI หรือใช้ `processing="radiance"` (W/m²/sr/nm) ซึ่งไม่จำเป็นต้องมีเซ็นเซอร์ที่จับคู่
- **`smart=True` ใช้เวลานานกว่าที่คาด** → การบรรจบของ AE ขึ้นอยู่กับพลวัตของฉาก; ปรับค่า `exposure_tolerance_pct` ให้แคบลง หรือปรับค่า `stability_window_s` ให้สั้นลง หากต้องการทริกเกอร์ที่เร็วขึ้น (แต่ไม่เสถียรเท่าเดิม)

---

## ดูเพิ่มเติม

- [คู่มืออ้างอิง CLI](cli-reference.md) — ทุกคำสั่งย่อยของ CLI ล้วนสะท้อนการเรียกใช้ SDK
- [คู่มือเซ็นเซอร์ DAQ](../daq/README.md) — กฎการต่อสาย การปรับเทียบ และการบันทึกข้อมูลเฉพาะสำหรับเซ็นเซอร์แต่ละชนิด
- เอกสารออนไลน์: `https://mapir.gitbook.io/chloros/api-python-sdk`</id></sn>
