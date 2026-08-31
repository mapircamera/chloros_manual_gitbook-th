# คู่มือเริ่มต้นอย่างรวดเร็วสำหรับCLI (pool-*)

ไดรเวอร์ `chloros-cli` ที่มาพร้อมกับระบบจะควบคุมเซ็นเซอร์ DAQ ผ่านชุดคำสั่ง **`daq pool-*`** — ซึ่งเป็นไคลเอนต์แบบบาง (HTTP) ที่ควบคุมเซ็นเซอร์ผ่านกลุ่มเซ็นเซอร์แบบถาวร (persistent sensor pool) ของแบ็กเอนด์ Chloros ระบบหลัง (backend) เป็นผู้ควบคุมการส่งข้อมูล ดังนั้น GUI, CLI และสคริปต์ SDK จึงใช้ handle เดียวที่ทำงานอยู่ร่วมกัน แทนที่จะแข่งขันกันเพื่อใช้พอร์ต ทุกสิ่งที่ลูกค้าต้องการสามารถเข้าถึงได้ผ่าน `pool-*`: การเชื่อมต่อ, การส่งข้อมูลแบบสตรีม, การบันทึกไฟล์ `.daq` ที่ได้รับการปรับเทียบ, และการสลับโปรไฟล์แคปซูล

`pool-*` ยังเป็น **เพียง** พื้นผิว DAQ เดียวในเวอร์ชันที่ปล่อยออกมา `chloros-cli daq --help` แสดงรายการคำสั่งย่อยของ `pool-*`, และการเรียกใช้คำสั่งย่อย DAQ ที่เชื่อมต่อกับฮาร์ดแวร์โดยตรงในเวอร์ชันที่ปล่อยออกจะสิ้นสุดด้วยข้อผิดพลาดที่ระบุชื่อแพ็กเกจที่ขาดหายไปอย่างชัดเจน และชี้ให้คุณกลับไปที่ `pool-*` — ไม่มีข้อผิดพลาดใดที่ทำงานเงียบๆ (คำสั่งที่เชื่อมต่อกับฮาร์ดแวร์โดยตรงทำงานได้เฉพาะจากแหล่งที่มาที่ตรวจสอบแล้วใน MAPIR; `pip install chloros-sdk` ก็ไม่สนับสนุนคำสั่งเหล่านี้เช่นกัน.)

***

## ข้อกำหนดเบื้องต้น

* **ระบบหลังบ้าน Chloros ต้องกำลังทำงาน** — คำสั่ง `pool-*` เป็นไคลเอนต์ HTTP ไม่ใช่ไดรเวอร์ฮาร์ดแวร์ บน Windows ให้เปิดแอปเดสก์ท็อป Chloros (แอปนี้จะเปิดระบบแบ็กเอนด์) บน Linux /Jetson แบบไม่มีหน้าจอ ให้เปิดบริการ: `sudo systemctl enable --now chloros-backend.service`
* **บัญชีผู้ใช้ Chloros+ (ระดับบริการแบบเสียเงิน)**: ให้รัน `chloros-cli login` ก่อน การบังคับใช้เกิดขึ้นด้านเซิร์ฟเวอร์ — หากไม่มีการเข้าสู่ระบบ คำสั่งจะล้มเหลวด้วย `401 AUTH_REQUIRED`; ในระดับฟรี (Iron) คำสั่งจะล้มเหลวด้วย `403 PLAN_UPGRADE_REQUIRED`.
* คำสั่งเหล่านี้กำหนดเป้าหมายไปที่ `http://127.0.0.1:5000` ตามค่าเริ่มต้น; กลุ่ม `daq pool-*` จะยอมรับตัวแปรสภาพแวดล้อม `CHLOROS_BACKEND_URL` หากระบบหลังของคุณทำงานอยู่ที่อื่น

***

## เซสชัน 5 นาที

```bash
# 1. Connect a sensor into the backend pool (pick the line matching your model)
chloros-cli daq pool-connect                                  # smart-detect any DAQ
chloros-cli daq pool-connect --port COM3                      # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF          # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-def330.local    # DAQ-E by hostname (reliable)

# 2. List the pool — this shows the sensor_id used by every command below
chloros-cli daq pool-list

# 3. Read the most recent calibrated spectrum frame (add --json for scripting)
chloros-cli daq pool-latest --sensor-id daq-e-def330 --json

# 4. Record a calibrated .daq file for 60 seconds
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 60 \
  --device-name "field-A"

# 5. Release the sensor when done
chloros-cli daq pool-disconnect --sensor-id daq-e-def330
```

***

## `pool-connect` — เปิดเซ็นเซอร์ในพูล

| ตัวเลือก | ความหมาย |
| --- | --- |
| `daq pool-connect` | Smart-detect: ค้นหา DAQ ใดก็ได้บนเครื่องนี้ |
| `daq pool-connect --port PORT` | DAQ-U บนพอร์ตซีเรียลเฉพาะ (เช่น `COM3`, `/dev/ttyUSB0`). |
| `daq pool-connect --ble` | DAQ-M ผ่าน BLE, MAC ถูกสแกนอัตโนมัติ |
| `daq pool-connect --mac MAC` | DAQ-M ที่ MAC BLE ที่ทราบ (หมายถึง `--ble`) |
| `daq pool-connect --eth-host HOST` | DAQ-E ที่ชื่อโฮสต์หรือ IP ที่ทราบ — **เส้นทางที่เชื่อถือได้** |
| `daq pool-connect --eth` | DAQ-E พร้อมการค้นพบอัตโนมัติ (mDNS, พร้อมการสำรองด้วย ARP). ดูข้อควรระวังด้านล่าง |

แฟล็กปรับแต่ง, ทั้งหมดเป็นตัวเลือก:

| แฟล็ก | ความหมาย |
| --- | --- |
| `--integration-time MS` / `-t MS` | เวลาการรวมสัญญาณแบบกำหนดเองในมิลลิวินาที |
| `--frame-avg N` / `-f N` | จำนวนเฟรมที่เฉลี่ยต่อสเปกตรัมที่รายงาน |
| `--no-ae` | ปิดการปรับค่าการเปิดรับแสงอัตโนมัติ (AE เปิดโดยค่าเริ่มต้น) |
| `--no-stream` | เชื่อมต่อโดยไม่เริ่มสตรีม (สามารถต่อต่อได้ภายหลังด้วย `pool-stream --start`). |
| `--cap-id CAP` | โปรไฟล์การปรับค่า Cap; ค่าเริ่มต้นของ backend คือ `sunshine_cosine`. ดู [`pool-set-cap`](#pool-set-cap-declare-the-fitted-cap). |

{% hint style="warning" %}
**ข้อควรระวังเกี่ยวกับการค้นพบอัตโนมัติของ `--eth`** บนโฮสต์ที่มีหลายอินเทอร์เฟซเครือข่าย (มีอินเทอร์เฟซเครือข่ายที่ใช้งานอยู่มากกว่าหนึ่งตัว) *การค้นหา `pool-connect --eth` ครั้งแรก* หลังการบูตอาจไม่พบข้อมูลใดๆ แม้เซ็นเซอร์จะอยู่ในสภาพดี — การค้นหาอาจพลาดอินเทอร์เฟซของเซ็นเซอร์ขณะที่แคช ARP ยังไม่ได้รับการอัปเดต หาก `--eth` ไม่พบอะไร ให้ลองทำใหม่ หรือข้ามขั้นตอนการค้นพบไปเลยโดยใช้ `--eth-host <ip-or-hostname>` ซึ่งเป็นวิธีที่เชื่อถือได้บนเครื่องที่มีหลายอินเทอร์เฟซเครือข่าย ชื่อโฮสต์ของ DAQ-E คือ `daq-e-<id>.local` (เช่น `daq-e-def330.local`); IP แบบธรรมดาของมันก็ใช้งานได้เช่นกัน
{% endhint %}

## `pool-list` — ตรวจสอบอุปกรณ์ที่เชื่อมต่อ

แสดงเซ็นเซอร์ทุกตัวในกลุ่มแบ็กเอนด์ รวมถึง `sensor_id` ที่คำสั่งอื่น ๆ จำเป็นต้องใช้:

| รุ่น | รูปแบบ `sensor_id` | ตัวอย่าง |
| --- | --- | --- |
| DAQ-U / DAQ-M | 5-octet hyphenated | `CB-7C-A8-2E-5F` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

## `pool-latest` — อ่านเฟรมสเปกตรัม

```bash
chloros-cli daq pool-latest --sensor-id daq-e-def330 --recent 10 --json
```

ส่งคืนเฟรมล่าสุด หรือเฟรม `--recent N` ล่าสุด; `--json` ส่งข้อมูลออกในรูปแบบที่เครื่องอ่านได้เพื่อใช้ในสคริปต์ เฟรมเป็นค่าความส่องผ่านสเปกตรัม (W/m²/nm) ที่ได้รับการปรับเทียบทางรังสีวิทยา บนกริด 135 จุด ในช่วง 340–1010 nm โดยได้นำโปรไฟล์ฝาเซ็นเซอร์มาใช้แล้ว เพื่อได้ค่าความเข้มรังสีเชิงปริมาณ ให้คำนวณค่าเฉลี่ยของเฟรมอย่างน้อย 15 วินาที — นี่เป็นลักษณะเฉพาะของเครื่องมือ ไม่ใช่ข้อบกพร่อง

## `pool-stream` — หยุดชั่วคราวหรือต่อต่อการสตรีม

```bash
chloros-cli daq pool-stream --sensor-id daq-e-def330 --stop    # pause
chloros-cli daq pool-stream --sensor-id daq-e-def330 --start   # resume
```

## `pool-record` — บันทึกไฟล์ `.daq`

```bash
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 150 \
  --output ~/Documents/spectra --device-name "rooftop-A"
chloros-cli daq pool-record --sensor-id daq-e-def330 --stop
```

| สัญลักษณ์ | ค่าเริ่มต้น | ความหมาย |
| --- | --- | --- |
| `--duration SEC` / `-d SEC` | `0` | ความยาวการบันทึกเป็นวินาที; `0` หมายถึงให้ทำงานจนกว่าจะออกคำสั่ง `--stop` |
| `--output DIR` / `-o DIR` | `~/Documents/DAQ Live View/` | โฟลเดอร์ผลลัพธ์ ซึ่ง **ถูกกำหนดบนเครื่องที่รันแบ็กเอนด์** |
| `--device-name NAME` | — | ป้ายกำกับที่เก็บไว้พร้อมกับการบันทึก |
| `--stop` | — | หยุดการบันทึกที่กำลังดำเนินการ |

{% hint style="info" %}
การบันทึกเกิดขึ้นใน backend ดังนั้นไฟล์ `.daq` จะถูกจัดเก็บในระบบไฟล์ของ **เครื่อง backend** — โดยค่าเริ่มต้นจะอยู่ใน `~/Documents/DAQ Live View/` ที่นั่น ไม่จำเป็นต้องเป็นที่ที่คุณเรียกใช้คำสั่ง CLI ชื่อไฟล์ประกอบด้วย ID ของเซ็นเซอร์และเวลา
{% endhint %}

## `pool-set-cap` — กำหนดฝาครอบที่ติดตั้ง

```bash
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id sunshine_cosine
```

ID ของตัวกรองจะเลือกโปรไฟล์การปรับแก้ที่วัดจากโรงงาน ซึ่งถูกนำไปใช้กับสเปกตรัมทุกชุด และ **ต้องตรงกับตัวกรองที่ติดตั้งจริงบนเซ็นเซอร์** — ทั้งเซ็นเซอร์และซอฟต์แวร์ไม่สามารถตรวจจับตัวกรองได้ด้วยตัวเอง และการเลือกนี้จะถูกบันทึกไว้ในไฟล์ `.daq` ทุกไฟล์ ค่าเริ่มต้นในทุกที่คือ `sunshine_cosine` (ทุกเครื่อง DAQ ส่งมาพร้อมติดตั้งฝาครอบ Sunshine cosine-corrector ที่มีการลดทอน ~12× ตามการออกแบบ — การเปลี่ยนฝาครอบโดยไม่ประกาศจะทำให้การแก้ไขสเปกตรัมผิดพลาดประมาณตามปัจจัยนั้น)

| `--cap-id` | มีให้ใช้บน |
| --- | --- |
| `sunshine_cosine` (ค่าเริ่มต้น) | DAQ-U, DAQ-M, DAQ-E |
| `fov_15`, `fov_45`, `fov_90` | DAQ-U, DAQ-E |
| `fov_30`, `fov_60` | DAQ-U เท่านั้น |
| `none` | DAQ-E เท่านั้น — ดูหมายเหตุ |

ID ฝาปิดที่อยู่นอกชุดของเซ็นเซอร์จะถูกปฏิเสธเมื่อเชื่อมต่อ พร้อมข้อความผิดพลาดที่ชัดเจน `none` (DAQ-E) หมายความว่าฝาครอบถูกถอดออกทางกายภาพ — มันยังคงใช้โปรไฟล์เรขาคณิตจากโรงงานสำหรับตัวกระจายแสงแก้วที่ฝังลึกของ DAQ-E ดังนั้นจึงไม่ใช่การไม่ดำเนินการ (no-op) และ DAQ-E ที่ไม่มีฝาครอบเป็นการตั้งค่าบนโต๊ะทดลอง ไม่ใช่การตั้งค่าภาคสนามที่ได้รับการสนับสนุน (DAQ-U แบบเปลือยเป็นแบบเปลือยจริงและไม่ต้องการโปรไฟล์การปรับแก้ใดๆ ทั้งสิ้น; DAQ-M ใช้ร่วมกับฝา Sunshine ของมัน)

## `pool-disconnect` — ปล่อยเซ็นเซอร์

```bash
chloros-cli daq pool-disconnect --sensor-id daq-e-def330   # one sensor
chloros-cli daq pool-disconnect --all                      # everything in the pool
```

***

## สรุปคำสั่ง

| คำสั่ง | วัตถุประสงค์ |
| --- | --- |
| `daq pool-connect [--port P \| --ble \| --mac M \| --eth \| --eth-host H] [-t MS] [-f N] [--no-ae] [--no-stream] [--cap-id CAP]` | เปิดเซ็นเซอร์ในพูลแบ็กเอนด์ |
| `daq pool-list` | แสดงเซ็นเซอร์ทุกตัวในพูลพร้อมค่า `sensor_id` |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | เฟรมสเปกตรัมที่ปรับเทียบล่าสุด N เฟรม |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | ดำเนินการสตรีมต่อ / หยุดชั่วคราว |
| `daq pool-record --sensor-id ID [-d SEC] [-o DIR] [--device-name NAME] [--stop]` | เริ่ม / หยุดการบันทึก `.daq` (ด้านแบ็กเอนด์) |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | เปลี่ยนโปรไฟล์การปรับค่า cap ระหว่างการทำงาน |
| `daq pool-disconnect --sensor-id ID [--all]` | ปล่อยเซ็นเซอร์หนึ่งตัว หรือทั้งหมด |

***

## การแก้ไขปัญหาการเชื่อมต่อ DAQ-E ครั้งแรก

1. DAQ-E ไม่มีไฟ LED แสดงสถานะ — ตรวจสอบการจ่ายไฟผ่านตัวบ่งชี้ PoE/link บนสวิตช์หรือพอร์ตอินเจกเตอร์ และรอสักครู่หลังเปิดเครื่องเพื่อให้ระบบบูตและเชื่อมต่อกับเครือข่าย
2. เครื่องเซิร์ฟเวอร์ต้องอยู่ใน **โดเมนการกระจายสัญญาณเดียวกัน** กับเซ็นเซอร์ — mDNS ไม่สามารถผ่านเราเตอร์ได้
3. บน Windows ให้ยอมรับคำเตือนของไฟร์วอลล์ Defender เมื่อเปิดใช้งานครั้งแรก (mDNS UDP 5353, ข้อมูล DAQ-E UDP 5002, PTP UDP 319/320)
4. ยังไม่ได้รับสัญญาณจาก `--eth`? ให้ใช้ `--eth-host` พร้อมชื่อโฮสต์ของอุปกรณ์ (`daq-e-<id>.local`) หรือ IP — ซึ่งเป็นเส้นทางที่เชื่อถือได้ โดยเฉพาะบนโฮสต์ที่มีหลายอินเทอร์เฟซเครือข่าย

***{% hint style="info" %}**เคล็ดลับสำหรับผู้ช่วย AI** ทุกหน้าในคู่มือนี้ถูกส่งในรูปแบบ Markdown ดิบ — ให้เพิ่ม `.md` ไปต่อท้าย slug ของหน้าในรูปแบบตัวพิมพ์เล็ก URL (หน้านี้: `https://mapir.gitbook.io/chloros/daq/cli-quick-start.md`); ดัชนีที่เครื่องอ่านได้คือ `https://mapir.gitbook.io/chloros/llms.txt`. เพื่อดูเอกสารระดับแฟล็กที่ครบถ้วนของ `chloros-cli daq` และทุกกลุ่มคำสั่งอื่น ๆ ให้ดาวน์โหลด [CLI Reference](../reference/cli-reference.md) (`https://mapir.gitbook.io/chloros/reference/cli-reference.md`); เส้นทางPython คือ `chloros_sdk.connect_daq_sensor()` ใน [SDK Reference](../reference/sdk-reference.md).
{% endhint %}
