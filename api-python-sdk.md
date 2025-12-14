# API: Python SDK

**Chloros Python SDK** ให้การเข้าถึงโปรแกรมประมวลผลภาพ Chloros โดยอัตโนมัติ เวิร์กโฟลว์แบบกำหนดเอง และการผสานรวมกับแอปพลิเคชัน Python และไปป์ไลน์การวิจัยของคุณได้อย่างราบรื่น

### คุณสมบัติที่สำคัญ

* 🐍 **Native Python** - Clean, Pythonic API สำหรับการประมวลผลภาพ
* 🔧 **การเข้าถึง API แบบเต็ม** - ควบคุมการประมวลผลคลอรอสได้อย่างสมบูรณ์
* 🚀 **ระบบอัตโนมัติ** - สร้างเวิร์กโฟลว์การประมวลผลแบบแบตช์แบบกำหนดเอง
* 🔗 **บูรณาการ** - ฝังคลอรอสในแอปพลิเคชัน Python ที่มีอยู่
* 📊 **พร้อมการวิจัย** - เหมาะสำหรับไปป์ไลน์การวิเคราะห์ทางวิทยาศาสตร์
* ⚡ **การประมวลผลแบบขนาน** - ปรับขนาดตามคอร์ CPU ของคุณ (Chloros+)

### ความต้องการ

| ความต้องการ          | รายละเอียด                                                             |
| - | - |
|**คลอรอสเดสก์ท็อป**  | จะต้องติดตั้งในเครื่อง                                           |
|**ใบอนุญาต**          | Chloros+ ([paid plan required](https://cloud.mapir.camera/pricing)) |
|**ระบบปฏิบัติการ** | Windows 10/11 (64 บิต)                                              |
|**Python**           | Python 3.7 หรือสูงกว่า                                                |
|**หน่วยความจำ**           | RAM ขั้นต่ำ 8GB (แนะนำ 16GB)                                  |
|**อินเทอร์เน็ต**         | จำเป็นสำหรับการเปิดใช้งานใบอนุญาต                                     |

{% hint style="warning" %}**ข้อกำหนดสิทธิ์การใช้งาน**: Python SDK ต้องมีการสมัครใช้งาน Chloros+ แบบชำระเงินสำหรับการเข้าถึง API แผนมาตรฐาน (ฟรี) ไม่มีการเข้าถึง API/SDK เยี่ยม [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) เพื่ออัปเกรด
{% endhint %}

## เริ่มต้นอย่างรวดเร็ว

### การติดตั้ง

ติดตั้งผ่าน pip:

```bash
pip install chloros-sdk
```

{% hint style="info" %}**การตั้งค่าครั้งแรก**: ก่อนใช้ SDK ให้เปิดใช้งานใบอนุญาต Chloros+ ของคุณโดยเปิด Chloros, Chloros (เบราว์เซอร์) หรือ Chloros CLI แล้วเข้าสู่ระบบด้วยข้อมูลประจำตัวของคุณ ต้องทำเพียงครั้งเดียวเท่านั้น
{% endhint %}

### การใช้งานขั้นพื้นฐาน

ประมวลผลโฟลเดอร์ที่มีเพียงไม่กี่บรรทัด:

```python
from chloros_sdk import process_folder

# One-line processing
results = process_folder("C:\\DroneImages\\Flight001")
```

### ควบคุมเต็มรูปแบบ

สำหรับขั้นตอนการทำงานขั้นสูง:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Create project
chloros.create_project("MyProject", camera="Survey3N_RGN")

# Import images
chloros.import_images("C:\\DroneImages\\Flight001")

# Configure settings
chloros.configure(
    vignette_correction=True,
    reflectance_calibration=True,
    indices=["NDVI", "NDRE", "GNDVI"]
)

# Process images
chloros.process(mode="parallel", wait=True)
```***

## คู่มือการติดตั้ง

### ข้อกำหนดเบื้องต้น

ก่อนติดตั้ง SDK ตรวจสอบให้แน่ใจว่าคุณมี:

1. **ติดตั้ง Chloros Desktop** แล้ว ([ดาวน์โหลด](download.md))
2.**Python 3.7+** ติดตั้งแล้ว ([python.org](https://www.python.org))
3.**ใบอนุญาตใช้งานคลอรอส+** ([อัปเกรด](https://cloud.mapir.camera/pricing))

### ติดตั้งผ่าน pip**การติดตั้งมาตรฐาน:**

```bash
pip install chloros-sdk
```**พร้อมรองรับการติดตามความคืบหน้า:**

```bash
pip install chloros-sdk[progress]
```**การพัฒนาการติดตั้ง:**

```bash
pip install chloros-sdk[dev]
```

### ตรวจสอบการติดตั้ง

ทดสอบว่าติดตั้ง SDK อย่างถูกต้อง:

```python
import chloros_sdk
print(f"Chloros SDK version: {chloros_sdk.__version__}")
```***

## การตั้งค่าครั้งแรก

### การเปิดใช้งานใบอนุญาต

SDK ใช้ใบอนุญาตเดียวกันกับ Chloros, Chloros (เบราว์เซอร์) และ Chloros CLI เปิดใช้งานครั้งเดียวผ่าน GUI หรือ CLI:

1. เปิด **Chloros หรือ Chloros (เบราว์เซอร์)** และเข้าสู่ระบบผู้ใช้<img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line">แท็บ หรือเปิด**CLI**
2. ป้อนข้อมูลรับรอง Chloros+ ของคุณแล้วเข้าสู่ระบบ
3. ใบอนุญาตถูกแคชไว้ในเครื่อง (ยังคงมีอยู่ตลอดการรีบูต)

{% hint style="success" %}**การตั้งค่าครั้งเดียว**: หลังจากเข้าสู่ระบบผ่าน GUI หรือ CLI แล้ว SDK จะใช้สิทธิ์การใช้งานที่แคชไว้โดยอัตโนมัติ ไม่จำเป็นต้องมีการรับรองความถูกต้องเพิ่มเติม!
{% endhint %}

### ทดสอบการเชื่อมต่อ

ตรวจสอบว่า SDK สามารถเชื่อมต่อกับคลอรอสได้:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK (auto-starts backend if needed)
chloros = ChlorosLocal()

# Check status
status = chloros.get_status()
print(f"Backend running: {status['running']}")
```***

## การอ้างอิง API

### คลอรอสคลาสท้องถิ่น

คลาสหลักสำหรับการประมวลผลภาพคลอรอสในพื้นที่

#### ตัวสร้าง

```python
ChlorosLocal(
    api_url="http://localhost:5000",     # Backend URL
    auto_start_backend=True,             # Auto-start backend if not running
    backend_exe=None,                    # Backend path (auto-detected)
    timeout=30,                          # Request timeout (seconds)
    backend_startup_timeout=60           # Backend startup timeout
)
```

**พารามิเตอร์:**

| พารามิเตอร์                 | พิมพ์ | ค่าเริ่มต้น                   | คำอธิบาย                           |
| - | - | - | - |
| `api_url`                 | STR  | `"http://localhost:5000"` | URL ของแบ็กเอนด์ Chloros ในเครื่อง          |
| `auto_start_backend`      | บูล | `True`                    | เริ่มแบ็กเอนด์โดยอัตโนมัติหากจำเป็น |
| `backend_exe`             | STR  | `None` (auto-detect)      | เส้นทางไปยังไฟล์ปฏิบัติการแบ็กเอนด์            |
| `timeout`                 | ภายใน  | `30`                      | ขอหมดเวลาเป็นวินาที            |
| `backend_startup_timeout` | ภายใน  | `60`                      | หมดเวลาสำหรับการเริ่มต้นแบ็กเอนด์ (วินาที) |**ตัวอย่าง:**

```python
# Default (auto-start backend)
chloros = ChlorosLocal()

# Connect to running backend
chloros = ChlorosLocal(auto_start_backend=False)

# Custom backend path
chloros = ChlorosLocal(backend_exe="C:/Custom/chloros-backend.exe")

# Custom timeout
chloros = ChlorosLocal(timeout=60)
```***

### วิธีการ

#### `create_project(project_name, camera=None)`

สร้างโปรเจ็กต์คลอรอสใหม่

**พารามิเตอร์:**

| พารามิเตอร์      | พิมพ์ | ที่จำเป็น | คำอธิบาย                                              |
| - | - | - | - |
| `project_name` | STR  | ใช่      | ชื่อโครงการ                                     |
| `camera`       | STR  | No       | เทมเพลตกล้อง (เช่น "Survey3N\_RGN", "Survey3W\_OCN") |**การส่งคืน:** `dict` - Project creation response**ตัวอย่าง:**

```python
# Basic project
chloros.create_project("DroneField_A")

# With camera template
chloros.create_project("DroneField_A", camera="Survey3N_RGN")
```***

#### `import_images(folder_path, recursive=False)`

นำเข้ารูปภาพจากโฟลเดอร์

**พารามิเตอร์:**

| พารามิเตอร์     | พิมพ์     | ที่จำเป็น | คำอธิบาย                        |
| - | - | - | - |
| `folder_path` | str/เส้นทาง | ใช่      | เส้นทางไปยังโฟลเดอร์ที่มีรูปภาพ         |
| `recursive`   | บูล     | No       | ค้นหาโฟลเดอร์ย่อย (ค่าเริ่มต้น: เท็จ) |**การส่งคืน:** `dict` - Import results with file count**ตัวอย่าง:**

```python
# Import from folder
chloros.import_images("C:\\DroneImages\\Flight001")

# Import recursively
chloros.import_images("C:\\DroneImages", recursive=True)
```***

#### `configure(**settings)`

กำหนดการตั้งค่าการประมวลผล**พารามิเตอร์:**

| พารามิเตอร์                 | พิมพ์ | ค่าเริ่มต้น                 | คำอธิบาย                     |
| - | - | - | - |
| `debayer`                 | STR  | "คุณภาพสูง (เร็วขึ้น)" | วิธีการชำระหนี้                  |
| `vignette_correction`     | บูล | `True`                  | เปิดใช้งานการแก้ไขบทความสั้น      |
| `reflectance_calibration` | บูล | `True`                  | เปิดใช้งานการสอบเทียบการสะท้อนแสง  |
| `indices`                 | รายการ | `None`                  | ดัชนีพืชพรรณที่จะคำนวณ |
| `export_format`           | STR  | "TIFF (16 บิต)"         | รูปแบบเอาต์พุต                   |
| `ppk`                     | บูล | `False`                 | เปิดใช้งานการแก้ไข PPK          |
| `custom_settings`         | คำสั่ง | `None`                  | การตั้งค่าแบบกำหนดเองขั้นสูง        |**ส่งออกรูปแบบ:**

* `"TIFF (16-bit)"`- แนะนำสำหรับ GIS/photogrammetry
* `"TIFF (32-bit, Percent)"`- การวิเคราะห์ทางวิทยาศาสตร์
* `"PNG (8-bit)"`- การตรวจสายตา
* `"JPG (8-bit)"`- เอาต์พุตที่ถูกบีบอัด

**ดัชนีที่มีอยู่:**

NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2 และอื่นๆ**ตัวอย่าง:**

```python
# Basic configuration
chloros.configure(
    vignette_correction=True,
    reflectance_calibration=True,
    indices=["NDVI", "NDRE"]
)

# Advanced configuration
chloros.configure(
    debayer="High Quality (Faster)",
    vignette_correction=True,
    reflectance_calibration=True,
    ppk=True,
    export_format="TIFF (32-bit, Percent)",
    indices=["NDVI", "NDRE", "GNDVI", "OSAVI", "CIG"]
)
```***

#### `process(mode="parallel", wait=True, progress_callback=None)`

ประมวลผลภาพโครงการ

**พารามิเตอร์:**

| พารามิเตอร์           | พิมพ์     | ค่าเริ่มต้น      | คำอธิบาย                               |
| - | - | - | - |
| `mode`              | STR      | `"parallel"` | โหมดการประมวลผล: "ขนาน" หรือ "อนุกรม"   |
| `wait`              | บูล     | `True`       | รอให้เสร็จสิ้น                       |
| `progress_callback` | เรียกได้ | `None`       | ฟังก์ชั่นการโทรกลับความคืบหน้า (ความคืบหน้า, msg) |
| `poll_interval`     | ลอย    | `2.0`        | ช่วงเวลาการโพลเพื่อความคืบหน้า (วินาที)   |**การส่งคืน:** `dict` - Processing results

{% hint style="warning" %}**โหมดขนาน**: ต้องมีใบอนุญาต Chloros+ ปรับขนาดตามคอร์ CPU ของคุณโดยอัตโนมัติ (สูงสุด 16 คน)
{% endhint %}**ตัวอย่าง:**

```python
# Simple processing
results = chloros.process()

# With progress monitoring
def show_progress(progress, message):
    print(f"[{progress}%] {message}")

chloros.process(
    mode="parallel",
    progress_callback=show_progress,
    wait=True
)

# Fire-and-forget (non-blocking)
chloros.process(wait=False)
```***

#### `get_config()`

รับการกำหนดค่าโครงการปัจจุบัน

**การส่งคืน:** `dict` - Current project configuration**ตัวอย่าง:**

```python
config = chloros.get_config()
print(config['Project Settings'])
```***

#### `get_status()`

รับข้อมูลสถานะแบ็กเอนด์

**การส่งคืน:** `dict` - Backend status**ตัวอย่าง:**

```python
status = chloros.get_status()
print(f"Running: {status['running']}")
print(f"URL: {status['url']}")
```***

#### `shutdown_backend()`

ปิดระบบแบ็กเอนด์ (หากเริ่มต้นโดย SDK)

**ตัวอย่าง:**

```python
chloros.shutdown_backend()
```***

### ฟังก์ชั่นอำนวยความสะดวก

#### `process_folder(folder_path, **options)`

ฟังก์ชั่นอำนวยความสะดวกบรรทัดเดียวในการประมวลผลโฟลเดอร์**พารามิเตอร์:**

| พารามิเตอร์                 | พิมพ์     | ค่าเริ่มต้น         | คำอธิบาย                    |
| - | - | - | - |
| `folder_path`             | str/เส้นทาง | ที่จำเป็น        | เส้นทางไปยังโฟลเดอร์ที่มีรูปภาพ     |
| `project_name`            | STR      | สร้างอัตโนมัติ  | ชื่อโครงการ                   |
| `camera`                  | STR      | `None`          | แม่แบบกล้อง                |
| `indices`                 | รายการ     | `["NDVI"]`      | ดัชนีในการคำนวณ           |
| `vignette_correction`     | บูล     | `True`          | เปิดใช้งานการแก้ไขบทความสั้น     |
| `reflectance_calibration` | บูล     | `True`          | เปิดใช้งานการสอบเทียบการสะท้อนแสง |
| `export_format`           | STR      | "TIFF (16 บิต)" | รูปแบบเอาต์พุต                  |
| `mode`                    | STR      | `"parallel"`    | โหมดการประมวลผล                |
| `progress_callback`       | เรียกได้ | `None`          | ความคืบหน้าการโทรกลับ              |**การส่งคืน:** `dict` - Processing results**ตัวอย่าง:**

```python
from chloros_sdk import process_folder

# Simple one-liner
results = process_folder("C:\\DroneImages\\Flight001")

# With custom settings
results = process_folder(
    "C:\\DroneImages\\Flight001",
    project_name="Field_A_Survey",
    camera="Survey3N_RGN",
    indices=["NDVI", "NDRE", "GNDVI"],
    mode="parallel"
)

# With progress monitoring
def show_progress(progress, message):
    print(f"[{progress}%] {message}")

results = process_folder(
    "C:\\DroneImages\\Flight001",
    progress_callback=show_progress
)
```***

## การสนับสนุนตัวจัดการบริบท

SDK รองรับตัวจัดการบริบทสำหรับการล้างข้อมูลอัตโนมัติ:

```python
from chloros_sdk import ChlorosLocal

# Auto-cleanup when done
with ChlorosLocal() as chloros:
    chloros.create_project("MyProject")
    chloros.import_images("C:\\Images")
    chloros.configure(indices=["NDVI"])
    chloros.process()
# Backend automatically shut down here
```

***

## ตัวอย่างที่สมบูรณ์

### ตัวอย่างที่ 1: การประมวลผลขั้นพื้นฐาน

ประมวลผลโฟลเดอร์ด้วยการตั้งค่าเริ่มต้น:

```python
from chloros_sdk import process_folder

# Process with default settings
results = process_folder("C:\\Datasets\\Field_A_2025_01_15")

print(f"Processing complete: {results}")
```***

### ตัวอย่างที่ 2: เวิร์กโฟลว์แบบกำหนดเอง

ควบคุมไปป์ไลน์การประมวลผลอย่างสมบูรณ์:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Create project with camera template
chloros.create_project("Research_Plot_A", camera="Survey3N_RGN")

# Import images
import_results = chloros.import_images("C:\\Research\\PlotA")
print(f"Imported {len(import_results.get('files', []))} images")

# Configure advanced settings
chloros.configure(
    debayer="High Quality (Faster)",
    vignette_correction=True,
    reflectance_calibration=True,
    ppk=False,
    export_format="TIFF (16-bit)",
    indices=["NDVI", "NDRE", "GNDVI", "OSAVI"]
)

# Process with progress monitoring
def show_progress(progress, message):
    print(f"Progress: {progress}% - {message}")

chloros.process(
    mode="parallel",
    progress_callback=show_progress,
    wait=True
)

print("Processing complete!")
```

***

### ตัวอย่างที่ 3: การประมวลผลหลายโฟลเดอร์เป็นชุด

ประมวลผลชุดข้อมูลเที่ยวบินหลายชุด:

```python
from chloros_sdk import ChlorosLocal
from pathlib import Path

# Initialize SDK once
chloros = ChlorosLocal()

# List of flight folders
flights = [
    "C:\\Datasets\\Flight_001",
    "C:\\Datasets\\Flight_002",
    "C:\\Datasets\\Flight_003"
]

for flight_path in flights:
    flight_name = Path(flight_path).name
    print(f"\n{'='*60}")
    print(f"Processing: {flight_name}")
    print('='*60)
    
    try:
        # Create project
        chloros.create_project(flight_name, camera="Survey3N_RGN")
        
        # Import images
        chloros.import_images(flight_path)
        
        # Configure
        chloros.configure(
            vignette_correction=True,
            reflectance_calibration=True,
            indices=["NDVI", "NDRE", "GNDVI"]
        )
        
        # Process
        chloros.process(mode="parallel", wait=True)
        
        print(f"✓ {flight_name} completed successfully")
    
    except Exception as e:
        print(f"✗ {flight_name} failed: {e}")

print("\n" + "="*60)
print("All flights processed!")
```

***

### ตัวอย่างที่ 4: การบูรณาการไปป์ไลน์การวิจัย

ผสานรวมคลอรอสเข้ากับการวิเคราะห์ข้อมูล:

```python
from chloros_sdk import ChlorosLocal
import pandas as pd
import matplotlib.pyplot as plt

# Initialize Chloros
chloros = ChlorosLocal()

# Field survey data
surveys = [
    {"name": "Plot_A", "folder": "C:\\Research\\PlotA", "biomass": 4500},
    {"name": "Plot_B", "folder": "C:\\Research\\PlotB", "biomass": 3800},
    {"name": "Plot_C", "folder": "C:\\Research\\PlotC", "biomass": 5200}
]

results = []

for survey in surveys:
    # Process with Chloros
    chloros.create_project(survey['name'])
    chloros.import_images(survey['folder'])
    chloros.configure(indices=["NDVI", "NDRE"])
    chloros.process(mode="parallel", wait=True)
    
    # Get results
    config = chloros.get_config()
    
    # Extract NDVI values (example - adjust based on your needs)
    # In real implementation, you would read the processed TIFF files
    
    results.append({
        'plot': survey['name'],
        'biomass': survey['biomass'],
        # Add your NDVI extraction here
    })

# Statistical analysis
df = pd.DataFrame(results)
print("\nResults:")
print(df)

# Create correlation plot
# plt.scatter(df['ndvi'], df['biomass'])
# plt.xlabel('NDVI')
# plt.ylabel('Biomass (kg/ha)')
# plt.title('NDVI vs Biomass Correlation')
# plt.show()
```***

### ตัวอย่างที่ 5: การติดตามความคืบหน้าแบบกำหนดเอง

การติดตามความคืบหน้าขั้นสูงพร้อมการบันทึก:

```python
from chloros_sdk import ChlorosLocal
from datetime import datetime
import logging

# Setup logging
logging.basicConfig(
    filename=f'processing_{datetime.now():%Y%m%d_%H%M%S}.log',
    level=logging.INFO,
    format='%(asctime)s - %(message)s'
)

# Progress callback with logging
def log_progress(progress, message):
    log_msg = f"[{progress}%] {message}"
    logging.info(log_msg)
    print(log_msg)

# Process with logging
chloros = ChlorosLocal()
chloros.create_project("LoggedProcess")
chloros.import_images("C:\\DroneImages")
chloros.configure(indices=["NDVI", "NDRE"])

logging.info("Starting processing...")
chloros.process(
    mode="parallel",
    progress_callback=log_progress,
    wait=True
)
logging.info("Processing complete!")
```

***

### ตัวอย่างที่ 6: การจัดการข้อผิดพลาด

การจัดการข้อผิดพลาดที่มีประสิทธิภาพสำหรับการใช้งานจริง:

```python
from chloros_sdk import ChlorosLocal
from chloros_sdk.exceptions import (
    ChlorosError,
    ChlorosBackendError,
    ChlorosLicenseError,
    ChlorosProcessingError
)

def process_safely(folder_path):
    """Process with comprehensive error handling"""
    try:
        with ChlorosLocal() as chloros:
            chloros.create_project("SafeProcess")
            chloros.import_images(folder_path)
            chloros.configure(indices=["NDVI"])
            chloros.process()
            
        return True, "Success"
    
    except ChlorosLicenseError as e:
        return False, f"License error: {e}. Upgrade to Chloros+ at cloud.mapir.camera/pricing"
    
    except ChlorosBackendError as e:
        return False, f"Backend error: {e}. Ensure Chloros Desktop is installed."
    
    except ChlorosProcessingError as e:
        return False, f"Processing error: {e}"
    
    except FileNotFoundError as e:
        return False, f"Folder not found: {e}"
    
    except ChlorosError as e:
        return False, f"Chloros error: {e}"
    
    except Exception as e:
        return False, f"Unexpected error: {e}"

# Use the safe function
success, message = process_safely("C:\\DroneImages\\Flight001")
if success:
    print(f"✓ {message}")
else:
    print(f"✗ {message}")
```***

### ตัวอย่างที่ 7: เครื่องมือบรรทัดคำสั่ง

สร้างเครื่องมือ CLI แบบกำหนดเองด้วย SDK:

```python
#!/usr/bin/env python
"""
Custom Chloros CLI Tool
Process multiple folders from command line
"""

import sys
import argparse
from pathlib import Path
from chloros_sdk import process_folder

def main():
    parser = argparse.ArgumentParser(description='Custom Chloros Processor')
    parser.add_argument('folders', nargs='+', help='Folders to process')
    parser.add_argument('--indices', nargs='+', default=['NDVI'],
                       help='Indices to calculate (default: NDVI)')
    parser.add_argument('--camera', default=None,
                       help='Camera template')
    parser.add_argument('--format', default='TIFF (16-bit)',
                       help='Export format')
    
    args = parser.parse_args()
    
    successful = []
    failed = []
    
    for folder in args.folders:
        folder_path = Path(folder)
        
        if not folder_path.exists():
            print(f"✗ Skipping {folder}: not found")
            failed.append(folder)
            continue
        
        print(f"\nProcessing: {folder_path.name}...")
        
        try:
            process_folder(
                folder_path,
                camera=args.camera,
                indices=args.indices,
                export_format=args.format
            )
            print(f"✓ {folder_path.name} complete")
            successful.append(folder)
        
        except Exception as e:
            print(f"✗ {folder_path.name} failed: {e}")
            failed.append(folder)
    
    # Summary
    print(f"\n{'='*60}")
    print(f"Summary: {len(successful)} successful, {len(failed)} failed")
    
    return 0 if not failed else 1

if __name__ == '__main__':
    sys.exit(main())
```

**การใช้งาน:**

```bash
python my_processor.py "C:\Flight001" "C:\Flight002" --indices NDVI NDRE GNDVI
```***

## การจัดการข้อยกเว้น

SDK มีคลาสข้อยกเว้นเฉพาะสำหรับข้อผิดพลาดประเภทต่างๆ:

### ลำดับชั้นข้อยกเว้น

```python
ChlorosError                    # Base exception
├── ChlorosBackendError        # Backend startup/connection issues
├── ChlorosLicenseError        # License validation issues
├── ChlorosConnectionError     # Network/connection failures
├── ChlorosProcessingError     # Image processing failures
├── ChlorosAuthenticationError # Authentication failures
└── ChlorosConfigurationError  # Configuration errors
```

### ตัวอย่างข้อยกเว้น

```python
from chloros_sdk import ChlorosLocal
from chloros_sdk.exceptions import *

try:
    chloros = ChlorosLocal()
    chloros.process()

except ChlorosLicenseError:
    print("Chloros+ license required. Upgrade at cloud.mapir.camera/pricing")

except ChlorosBackendError:
    print("Backend failed to start. Ensure Chloros Desktop is installed.")

except ChlorosProcessingError as e:
    print(f"Processing failed: {e}")

except ChlorosError as e:
    print(f"General Chloros error: {e}")
```

***

## หัวข้อขั้นสูง

### การกำหนดค่าแบ็กเอนด์แบบกำหนดเอง

ใช้ตำแหน่งหรือการกำหนดค่าแบ็กเอนด์ที่กำหนดเอง:

```python
chloros = ChlorosLocal(
    backend_exe="C:\\Custom\\chloros-backend.exe",
    api_url="http://localhost:5001",  # Custom port
    timeout=60,                        # Longer timeout
    backend_startup_timeout=120        # 2 minutes startup
)
```

### การประมวลผลแบบไม่ปิดกั้น

เริ่มการประมวลผลและทำงานอื่นต่อ:

```python
# Start processing (non-blocking)
chloros.process(wait=False)

# Do other work here...
print("Processing started in background...")

# Check status later
import time
while True:
    status = chloros.get_config()
    if status.get('processing_complete'):
        break
    time.sleep(5)

print("Processing complete!")
```

### การจัดการหน่วยความจำ

สำหรับชุดข้อมูลขนาดใหญ่ ให้ประมวลผลเป็นชุด:

```python
from pathlib import Path

base_folder = Path("C:\\LargeDataset")
batch_size = 100

# Get all image files
images = list(base_folder.glob("*.RAW"))

# Process in batches
for i in range(0, len(images), batch_size):
    batch = images[i:i+batch_size]
    batch_folder = base_folder / f"batch_{i//batch_size}"
    
    # Create batch folder and move images
    # ... (implementation details)
    
    # Process batch
    process_folder(batch_folder)
```

***

## การแก้ไขปัญหา

### แบ็กเอนด์ไม่เริ่มต้น**ปัญหา:** SDK fails to start backend**โซลูชั่น:**

1. ตรวจสอบว่ามีการติดตั้ง Chloros Desktop แล้ว:

```python
import os
backend_path = r"C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe"
print(f"Backend exists: {os.path.exists(backend_path)}")
```

2. ตรวจสอบว่าไฟร์วอลล์ Windows ไม่ได้บล็อกอยู่
3. ลองใช้เส้นทางแบ็กเอนด์ด้วยตนเอง:

```python
chloros = ChlorosLocal(backend_exe="C:\\Path\\To\\chloros-backend.exe")
```***

### ตรวจไม่พบใบอนุญาต

**ปัญหา:** SDK warns about missing license**โซลูชั่น:**

1. เปิด Chloros, Chloros (เบราว์เซอร์) หรือ Chloros CLI แล้วเข้าสู่ระบบ
2. ตรวจสอบว่าใบอนุญาตถูกแคชไว้:

```python
from pathlib import Path
import os

# Check cache location (Windows)
cache_path = Path(os.getenv('APPDATA')) / 'Chloros' / 'cache'
print(f"Cache exists: {cache_path.exists()}")
```

3. ติดต่อฝ่ายสนับสนุน: info@mapir.camera***

### ข้อผิดพลาดในการนำเข้า

**ปัญหา:** `ModuleNotFoundError: No module named 'chloros_sdk'`**โซลูชั่น:**

```bash
# Verify installation
pip show chloros-sdk

# Reinstall if needed
pip uninstall chloros-sdk
pip install chloros-sdk

# Check Python environment
python -c "import sys; print(sys.path)"
```***

### หมดเวลาการประมวลผล

**ปัญหา:** Processing times out**โซลูชั่น:**

1. เพิ่มการหมดเวลา:

```python
chloros = ChlorosLocal(timeout=120)  # 2 minutes
```

2. ประมวลผลแบทช์ที่มีขนาดเล็กลง
3. ตรวจสอบพื้นที่ว่างในดิสก์
4. ตรวจสอบทรัพยากรระบบ***

### พอร์ตมีการใช้งานแล้ว

**ปัญหา:** Backend port 5000 occupied**โซลูชั่น:**

```python
# Use different port
chloros = ChlorosLocal(api_url="http://localhost:5001")
```

หรือค้นหาและปิดกระบวนการที่ขัดแย้งกัน:

```powershell
# PowerShell
Get-NetTCPConnection -LocalPort 5000
```***

## เคล็ดลับประสิทธิภาพ

### ปรับความเร็วการประมวลผลให้เหมาะสม

1. **ใช้โหมดขนาน** (ต้องใช้คลอรอส+)

```python
chloros.process(mode="parallel")  # Up to 16 workers
```

2.**ลดความละเอียดเอาต์พุต** (หากยอมรับได้)

```python
chloros.configure(export_format="PNG (8-bit)")  # Faster than TIFF
```

3.**ปิดการใช้งานดัชนีที่ไม่จำเป็น**

```python
# Only calculate needed indices
chloros.configure(indices=["NDVI"])  # Not all indices
```

4.**ประมวลผลบน SSD** (ไม่ใช่ HDD)***

### การเพิ่มประสิทธิภาพหน่วยความจำ

สำหรับชุดข้อมูลขนาดใหญ่:

```python
# Process in batches instead of all at once
# See "Memory Management" in Advanced Topics
```

***

### การประมวลผลพื้นหลัง

ปลดปล่อย Python สำหรับงานอื่น:

```python
chloros.process(wait=False)  # Non-blocking

# Continue with other work
# ...
```***

## ตัวอย่างการบูรณาการ

### บูรณาการจังโก้

```python
# views.py
from django.http import JsonResponse
from chloros_sdk import process_folder

def process_images_view(request):
    if request.method == 'POST':
        folder_path = request.POST.get('folder_path')
        
        try:
            results = process_folder(folder_path)
            return JsonResponse({'success': True, 'results': results})
        except Exception as e:
            return JsonResponse({'success': False, 'error': str(e)})
```

### API ของขวด

```python
# app.py
from flask import Flask, request, jsonify
from chloros_sdk import process_folder

app = Flask(__name__)

@app.route('/api/process', methods=['POST'])
def process():
    data = request.get_json()
    folder_path = data.get('folder_path')
    
    try:
        results = process_folder(folder_path)
        return jsonify({'success': True, 'results': results})
    except Exception as e:
        return jsonify({'success': False, 'error': str(e)}), 500

if __name__ == '__main__':
    app.run()
```

### สมุดบันทึกจูปีเตอร์

```python
# notebook.ipynb
from chloros_sdk import ChlorosLocal
import matplotlib.pyplot as plt

# Initialize
chloros = ChlorosLocal()

# Process
chloros.create_project("JupyterTest")
chloros.import_images("C:\\Data")
chloros.configure(indices=["NDVI"])

# Progress in notebook
from IPython.display import clear_output

def notebook_progress(progress, message):
    clear_output(wait=True)
    print(f"Progress: {progress}%")
    print(message)

chloros.process(progress_callback=notebook_progress)

# Visualize results
# ... (your visualization code)
```

***

## คำถามที่พบบ่อย

### ถาม: SDK จำเป็นต้องมีการเชื่อมต่ออินเทอร์เน็ตหรือไม่**A:** Only for initial license activation. After logging in via Chloros, Chloros (Browser) or Chloros CLI the license is cached locally and works offline for 30 days.***

### ถาม: ฉันสามารถใช้ SDK บนเซิร์ฟเวอร์ที่ไม่มี GUI ได้หรือไม่

**A:** Yes! Requirements:

* Windows Server 2016 หรือใหม่กว่า
* ติดตั้งคลอรอส (ครั้งเดียว)
* ใบอนุญาตเปิดใช้งานบนเครื่องใด ๆ (คัดลอกใบอนุญาตแคชไปยังเซิร์ฟเวอร์)

***

### ถาม: Desktop, CLI และ SDK แตกต่างกันอย่างไร

| คุณสมบัติ         | GUI เดสก์ท็อป | บรรทัดคำสั่ง CLI | Python SDK  |
| - | - | - | - |
|**อินเทอร์เฟซ**   | คลิกชี้ | สั่งการ          | หลาม API  |
|**ดีที่สุดสำหรับ**    | งานทัศนศิลป์ | การเขียนสคริปต์        | บูรณาการ |
|**ระบบอัตโนมัติ**  | จำกัด     | ดี             | ยอดเยี่ยม   |
|**ความยืดหยุ่น** | ขั้นพื้นฐาน       | ดี             | สูงสุด     |
|**ใบอนุญาต**     | คลอรอส+    | คลอรอส+         | คลอรอส+    |***

### ถาม: ฉันสามารถเผยแพร่แอปที่สร้างด้วย SDK ได้หรือไม่

**A:** SDK code can be integrated into your applications, but:

* ผู้ใช้ปลายทางต้องติดตั้งคลอรอส
* ผู้ใช้ปลายทางต้องมีใบอนุญาต Chloros+ ที่ใช้งานได้
* การจำหน่ายเชิงพาณิชย์ต้องมีใบอนุญาต OEM

ติดต่อ info@mapir.cam เพื่อสอบถามข้อมูล OEM

***

### ถาม: ฉันจะอัปเดต SDK ได้อย่างไร

```bash
pip install --upgrade chloros-sdk
```***

### ถาม: ภาพที่ประมวลผลแล้วจะถูกบันทึกไว้ที่ไหน?

ตามค่าเริ่มต้นในเส้นทางโครงการ :

```
Project_Path/
└── MyProject/
    └── Survey3N_RGN/          # Processed outputs
```

***

### ถาม: ฉันสามารถประมวลผลรูปภาพจากสคริปต์ Python ที่ทำงานตามกำหนดเวลาได้หรือไม่**A:** Yes! Use Windows Task Scheduler with Python scripts:

```python
# scheduled_processing.py
from chloros_sdk import process_folder

# Process today's flights
results = process_folder("C:\\Flights\\Today")
```

กำหนดเวลาผ่าน Task Scheduler เพื่อให้ทำงานทุกวัน***

### ถาม: SDK รองรับ async/await หรือไม่

**A:** Current version is synchronous. For async behavior, use `wait=False` or run in separate thread:

```python
import threading

def process_thread():
    chloros.process()

thread = threading.Thread(target=process_thread)
thread.start()

# Continue with other work...
```***

## การขอความช่วยเหลือ

### เอกสารประกอบ

* **การอ้างอิง API**: หน้านี้

### ช่องทางการสนับสนุน

* **อีเมล**: info@mapir.cam
* **เว็บไซต์**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **ราคา**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

### รหัสตัวอย่าง

ตัวอย่างทั้งหมดที่แสดงไว้ที่นี่ได้รับการทดสอบและพร้อมสำหรับการผลิต คัดลอกและปรับใช้ให้เหมาะกับกรณีการใช้งานของคุณ***

## ใบอนุญาต

**ซอฟต์แวร์ที่เป็นกรรมสิทธิ์** - ลิขสิทธิ์ (c) 2025 MAPIR Inc.

SDK ต้องการการสมัครสมาชิก Chloros+ ที่ใช้งานอยู่ ห้ามใช้ แจกจ่าย หรือดัดแปลงโดยไม่ได้รับอนุญาต
