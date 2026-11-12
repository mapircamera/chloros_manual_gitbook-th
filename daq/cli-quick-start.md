# คู่มือเริ่มต้นอย่างรวดเร็วสำหรับCLI (pool-*)

ไดรเวอร์ `chloros-cli` ที่มาพร้อมกับระบบจะควบคุมเซ็นเซอร์ DAQ ผ่านชุดคำสั่ง **`daq pool-*`** — ซึ่งเป็นไคลเอนต์ HTTP แบบบาง ที่ควบคุมเซ็นเซอร์ผ่านกลุ่มเซ็นเซอร์แบบคงที่ (persistent sensor pool) ของแบ็กเอนด์ Chloros ระบบแบ็กเอนด์เป็นผู้ควบคุมการส่งข้อมูล ดังนั้น GUI, CLI และสคริปต์ SDK จึงใช้ handle เดียวที่ทำงานอยู่ร่วมกัน แทนที่จะต้องแย่งกันใช้พอร์ต ทุกสิ่งที่ลูกค้าต้องการสามารถเข้าถึงได้ผ่าน `pool-*`: การเชื่อมต่อ การส่งข้อมูลแบบสตรีม การบันทึกไฟล์ `.daq` ที่ได้รับการปรับเทียบ และการสลับโปรไฟล์แคป

`pool-*` เป็นอินเทอร์เฟซ DAQ ที่ได้รับการสนับสนุนในเวอร์ชันที่ปล่อยออกมา `chloros-cli daq --help` แสดงรายการคำสั่งย่อยของ `pool-*` และ `daq discover` / `daq list` ดำเนินการสแกนเดียวกันกับ `pool-discover`. ในกรณีที่ไม่มีคำสั่งย่อย DAQ ที่เชื่อมต่อกับฮาร์ดแวร์โดยตรง ระบบจะออกด้วยข้อผิดพลาดที่ระบุชื่อแพ็กเกจที่ขาดหายไปอย่างชัดเจน และชี้ให้คุณกลับไปใช้ `pool-*` — ไม่มีข้อผิดพลาดใดที่เกิดขึ้นอย่างเงียบๆ (`pip install chloros-sdk` ก็ไม่สนับสนุนคำสั่ง DAQ ที่เชื่อมต่อกับฮาร์ดแวร์โดยตรงเช่นกัน)

***

## ข้อกำหนดเบื้องต้น

* **แบ็กเอนด์Chloros ต้องกำลังทำงาน** — คำสั่ง `pool-*` เป็นไคลเอนต์ HTTP ไม่ใช่ไดรเวอร์ฮาร์ดแวร์ บน Windows ให้เปิดแอปเดสก์ท็อป Chloros (แอปนี้จะเปิดแบ็กเอนด์) บนระบบ Linux/Jetson แบบ headless ให้เปิดใช้งานบริการด้วยบัญชีผู้ใช้ของคุณ: `sudo systemctl enable --now chloros-backend@$USER.service` (ดู [การติดตั้งบน Linux](../linux/linux-installation.md#always-on-ptp-for-headless-hosts)).
* **การเข้าสู่ระบบChloros+ (ระดับบริการแบบเสียค่าใช้จ่าย)**: ให้เรียกใช้ `chloros-cli login` ก่อน การบังคับใช้เกิดขึ้นด้านเซิร์ฟเวอร์ — หากไม่มีการเข้าสู่ระบบ คำสั่งจะล้มเหลวด้วย `401 AUTH_REQUIRED`; ในระดับฟรี (Iron) คำสั่งจะล้มเหลวด้วย `403 PLAN_UPGRADE_REQUIRED`.
* คำสั่งเหล่านี้มีเป้าหมายเป็น `http://127.0.0.1:5000` ตามค่าเริ่มต้น; กลุ่มคำสั่ง `daq pool-*` จะยอมรับตัวแปรสภาพแวดล้อม `CHLOROS_BACKEND_URL` หากระบบหลังของคุณทำงานอยู่ที่อื่น

***

## เซสชัน 5 นาที

```bash
# 1. Connect a sensor into the backend pool (pick the line matching your model)
chloros-cli daq pool-connect                                  # smart-detect any DAQ
chloros-cli daq pool-connect --port COM3                      # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF          # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-def330.local    # DAQ-E by hostname (reliable)
#    (a DAQ-M's BLE MAC is not printed anywhere: find it with `chloros-cli daq pool-discover --only ble`)

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
| `daq pool-connect --eth-host HOST` | DAQ-E ที่ชื่อโฮสต์หรือ IP ที่ทราบ — **เส้นทางที่เชื่อถือได้**. |
| `daq pool-connect --eth` | DAQ-E พร้อมการค้นพบอัตโนมัติ (mDNS, พร้อมการสำรองด้วย ARP). ดูข้อควรระวังด้านล่าง |

แฟล็กปรับแต่ง, ทั้งหมดเป็นตัวเลือก:

| แฟล็ก | ความหมาย |
| --- | --- |
| `--integration-time MS` / `-t MS` | เวลาการรวมสัญญาณแบบกำหนดเองในมิลลิวินาที |
| `--frame-avg N` / `-f N` | จำนวนเฟรมที่เฉลี่ยต่อสเปกตรัมที่รายงาน |
| `--no-ae` | ปิดการปรับค่าการเปิดรับแสงอัตโนมัติ (AE เปิดตามค่าเริ่มต้น) |
| `--no-stream` | เชื่อมต่อโดยไม่เริ่มสตรีม (สามารถต่อต่อได้ภายหลังด้วย `pool-stream --start`). |
| `--cap-id CAP` | โปรไฟล์การปรับแก้ Cap; ค่าเริ่มต้นของ backend คือ `sunshine_cosine`. ดู [`pool-set-cap`](#pool-set-cap-declare-the-fitted-cap). |

การเชื่อมต่อ DAQ-E ที่อยู่ใน pool แล้วภายใต้ที่อยู่อื่นของมัน — ชื่อโฮสต์ `daq-e-<id>.local` หรือ IP ของมัน — จะคืนค่าข้อมูลการเข้า pool ที่มีอยู่แทนที่จะเปิดอุปกรณ์นั้นเป็นครั้งที่สอง

{% hint style="warning" %}
**ข้อควรระวังในการค้นพบอัตโนมัติของ `--eth`** บนโฮสต์ที่มีหลายอินเทอร์เฟซเครือข่าย (มีอินเทอร์เฟซเครือข่ายที่ใช้งานอยู่มากกว่าหนึ่งตัว) *การค้นหา `pool-connect --eth` ครั้งแรก* หลังการบูตอาจไม่พบอะไรเลย แม้เซ็นเซอร์จะอยู่ในสภาพดี — การค้นหาอาจพลาดอินเทอร์เฟซของเซ็นเซอร์ในขณะที่แคช ARP ยังไม่ได้รับการอัปเดต หาก `--eth` ไม่พบอะไร ให้ลองทำใหม่ หรือข้ามขั้นตอนการค้นพบไปเลยโดยใช้ `--eth-host <ip-or-hostname>` ซึ่งเป็นวิธีที่เชื่อถือได้บนเครื่องที่มีหลายอินเทอร์เฟซเครือข่าย ชื่อโฮสต์ของ DAQ-E คือ `daq-e-<id>.local` (เช่น `daq-e-def330.local`); IP แบบธรรมดาของมันก็ใช้งานได้เช่นกัน
{% endhint %}

## `pool-discover` — สแกนโดยไม่เชื่อมต่อ

```bash
chloros-cli daq pool-discover                  # USB, BLE and Ethernet
chloros-cli daq pool-discover --only ble       # BLE only: this is how you find a DAQ-M's MAC
chloros-cli daq pool-discover --json           # machine-readable
```

สแกนหาเซ็นเซอร์ที่คุณสามารถเชื่อมต่อได้ และแสดงที่อยู่สำหรับการเชื่อมต่อ โดยไม่ต้องเชื่อมต่อจริง `--only` รับชุดย่อยที่คั่นด้วยเครื่องหมายจุลภาคจาก `usb,ble,eth`; `--timeout SEC` ตั้งค่าเวลาสแกนต่อโปรโตคอลการส่งข้อมูล (ค่าเริ่มต้น 5 วินาที, จำกัดระหว่าง 1–20 วินาที) เซ็นเซอร์ที่เปิดอยู่ในพูลแล้วจะไม่ถูกแสดงในรายการ (DAQ-M ที่เชื่อมต่ออยู่จะหยุดส่งสัญญาณ) — ใช้ `pool-list` สำหรับเซ็นเซอร์เหล่านั้น ในเวอร์ชันที่จัดส่ง `chloros-cli daq discover` และ `daq list` จะดำเนินการสแกนเดียวกันนี้

## `pool-list` — ตรวจสอบอุปกรณ์ที่เชื่อมต่ออยู่

แสดงเซ็นเซอร์ทุกตัวใน pool ด้านหลัง รวมถึง `sensor_id` ที่คำสั่งอื่น ๆ จำเป็นต้องใช้:

| Model | `sensor_id` shape | Example |
| --- | --- | --- |
| DAQ-U / DAQ-M | 5-octet hyphenated | `CB-7C-A8-2E-5F` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

อุปกรณ์ DAQ-E ที่ PTP ถูกปิด (การตั้งค่าถูกบันทึกไว้ในอุปกรณ์) จะถูกทำเครื่องหมายใน `pool-list` ด้วย `!! PTP is switched OFF on this unit (saved in its flash): its timestamps are not PTP-synced.` และคำสั่งเพื่อเปิด PTP กลับ — ดู [`pool-enable-ptp`](#pool-enable-ptp-switch-a-daq-es-ptp-back-on).

## `pool-latest` — อ่านเฟรมสเปกตรัม

```bash
chloros-cli daq pool-latest --sensor-id daq-e-def330 --recent 10 --json
```

ส่งคืนเฟรมล่าสุด หรือเฟรม `--recent N` ล่าสุด; `--json` ส่งข้อมูลออกในรูปแบบที่เครื่องอ่านได้เพื่อใช้ในสคริปต์ เฟรมเป็นค่าความส่องผ่านสเปกตรัมที่ได้รับการปรับเทียบทางรังสี (W/m²/nm) บนกริด 135 จุด ในช่วง 340–1010 nm โดยได้นำโปรไฟล์ฝาเซ็นเซอร์มาใช้แล้ว เพื่อได้ค่าความส่องรังสีเชิงปริมาณ ให้เฉลี่ยเฟรมอย่างน้อย 15 วินาที — นี่เป็นลักษณะเฉพาะของเครื่องมือ ไม่ใช่ข้อบกพร่อง

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
| `--duration SEC` / `-d SEC` | `0` | ความยาวการบันทึกเป็นวินาที; `0` หมายความว่าให้ทำงานจนกว่าจะออกคำสั่ง `--stop` |
| `--output DIR` / `-o DIR` | `~/Documents/DAQ Live View/` | โฟลเดอร์ผลลัพธ์ เมื่อใช้ backend บนคอมพิวเตอร์นี้ เส้นทางสัมพัทธ์จะถูกกำหนดตามโฟลเดอร์ที่คุณรันคำสั่ง; ส่วนเมื่อใช้ backend ระยะไกล เส้นทางจะถูกกำหนด **บนเครื่องที่รัน backend** |
| `--device-name NAME` | — | ป้ายกำกับที่เก็บไว้พร้อมกับการบันทึก |
| `--stop` | — | หยุดการบันทึกที่กำลังดำเนินการ |
| `--confirm-after SEC` | `20` | หลังจากเริ่มต้นแล้ว ให้รอจนถึงระยะเวลาที่กำหนดนี้เพื่อให้แถวแรกถูกเขียน และแจ้งเตือนอย่างชัดเจน (`!! WARNING: no rows written after …s. This recording is producing an EMPTY file.`) หากไม่มีข้อมูลมาถึง `0` ปิดการตรวจสอบ |
| `--heartbeat SEC` | `30` | ด้วย `--duration`, พิมพ์จำนวนแถวทุก SEC วินาที และแจ้งเตือนหากจำนวนแถวหยุดเพิ่มขึ้น `0` ปิดการตรวจสอบนี้ |

{% hint style="info" %}
การบันทึกเกิดขึ้นในระบบหลัง (backend) ดังนั้นไฟล์ `.daq` จะถูกจัดเก็บในระบบไฟล์ของ **เครื่องหลัง** — โดยค่าเริ่มต้นใน `~/Documents/DAQ Live View/` จะอยู่ที่นั่น ไม่จำเป็นต้องเป็นที่ที่คุณรันคำสั่งCLI ชื่อไฟล์ประกอบด้วย ID ของเซ็นเซอร์และเวลา
{% endhint %}

## `pool-set-cap` — กำหนดฝาครอบที่ติดตั้ง

```bash
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id sunshine_cosine
```

ID ของตัวกรองจะเลือกโปรไฟล์การปรับแก้ที่วัดจากโรงงาน ซึ่งถูกนำไปใช้กับสเปกตรัมทุกชุด และ **ต้องตรงกับตัวกรองที่ติดตั้งไว้บนเซ็นเซอร์จริง** — ทั้งเซ็นเซอร์และซอฟต์แวร์ไม่สามารถตรวจจับตัวกรองได้ด้วยตัวเอง และการเลือกนี้จะถูกบันทึกไว้ในทุกไฟล์ `.daq` ค่าเริ่มต้นในทุกที่คือ `sunshine_cosine` (ทุก DAQ ส่งมาพร้อมติดตั้งฝาครอบ Sunshine cosine-corrector — การลดทอนตามการออกแบบประมาณ 12× — การเปลี่ยนฝาครอบโดยไม่ประกาศจะทำให้การแก้ไขสเปกตรัมผิดพลาดประมาณตามปัจจัยนั้น)

| `--cap-id` | มีให้ใช้บน |
| --- | --- |
| `sunshine_cosine` (ค่าเริ่มต้น) | DAQ-U, DAQ-M, DAQ-E |
| `fov_15`, `fov_45`, `fov_90` | DAQ-U, DAQ-E |
| `fov_30`, `fov_60` | DAQ-U เท่านั้น |
| `none` | DAQ-E เท่านั้น — ดูหมายเหตุ |

ID ฝาปิดที่อยู่นอกชุดของเซ็นเซอร์จะถูกปฏิเสธเมื่อเชื่อมต่อ พร้อมแสดงข้อผิดพลาดที่ชัดเจน `none` (DAQ-E) หมายความว่าฝาครอบถูกถอดออกทางกายภาพ — มันยังคงใช้โปรไฟล์เรขาคณิตจากโรงงานสำหรับตัวกระจายแสงแก้วที่ฝังลึกของ DAQ-E ดังนั้นจึงไม่ใช่การไม่ดำเนินการ (no-op) และ DAQ-E ที่ไม่มีฝาครอบเป็นการตั้งค่าบนโต๊ะทดลอง ไม่ใช่การตั้งค่าในสนามที่ได้รับการสนับสนุน (DAQ-U แบบเปลือยเป็นแบบเปลือยจริงและไม่ต้องการโปรไฟล์การปรับแก้ใดๆ ทั้งสิ้น; DAQ-M ใช้ร่วมกับฝา Sunshine ของมัน)

## `pool-disconnect` — ปล่อยเซ็นเซอร์

```bash
chloros-cli daq pool-disconnect --sensor-id daq-e-def330   # one sensor
chloros-cli daq pool-disconnect --all                      # everything in the pool
```

`pool-disconnect` จะปล่อยเซ็นเซอร์ไม่ว่าใครเป็นผู้เปิดมัน ดังนั้นมันจึงปล่อยเซ็นเซอร์ที่สคริปต์ที่ล่มทิ้งไว้ในพูลด้วย (การบันทึกบนเซ็นเซอร์นั้นต้องเสร็จสิ้นก่อน) เซ็นเซอร์ที่ไม่อยู่ในพูลไม่มีอะไรที่จะปล่อย: คำสั่งจะพิมพ์ `<sensor-id>: already released (not in the pool)` และออกจาก `0` การปล่อยที่แบ็กเอนด์ปฏิเสธหรือไม่ยืนยันจะออกจาก `1`

## `pool-enable-ptp` — เปิด PTP ของ DAQ-E กลับขึ้น

```bash
chloros-cli daq pool-enable-ptp --sensor-id daq-e-def330
```

DAQ-E เก็บการตั้งค่าเปิด/ปิด PTP ไว้ในหน่วยความจำของตัวเอง ดังนั้น อุปกรณ์ที่ PTP ถูกปิดจะยังคงปิดอยู่ — และเวลาประทับตราจะไม่ถูกซิงค์กับ PTP — จนกว่าจะเปิด PTP กลับขึ้นมาใหม่ `pool-list` จะทำเครื่องหมายอุปกรณ์ดังกล่าวและพิมพ์คำสั่งนี้สำหรับอุปกรณ์นั้น Chloros ไม่เคยเปิดอุปกรณ์กลับเองโดยอัตโนมัติ เพราะการปิดอาจเป็นการตั้งใจทำเช่นนั้น ฟีเจอร์ใหม่นี้มีในเวอร์ชัน 1.3.0.

## การตั้งค่าอุปกรณ์ DAQ-E

```bash
chloros-cli daq pool-info --sensor-id daq-e-def330
chloros-cli daq pool-set-network --sensor-id daq-e-def330 \
  --static 192.168.1.50 --gateway 192.168.1.1
chloros-cli daq pool-set-network --sensor-id daq-e-def330 --auto
chloros-cli daq pool-set-auto-stream --sensor-id daq-e-def330 --on
chloros-cli daq pool-reboot --sensor-id daq-e-def330
chloros-cli daq pool-update-firmware --sensor-id daq-e-def330
```

`pool-info` แสดงเฟิร์มแวร์ของเซ็นเซอร์ (และว่าเวอร์ชันChloros นี้มีเฟิร์มแวร์ที่ใหม่กว่าหรือไม่), ที่อยู่ของเซ็นเซอร์และวิธีที่มันได้รับที่อยู่ดังกล่าว, การตั้งค่าเครือข่ายที่บันทึกไว้เมื่อแตกต่างจากการตั้งค่าที่กำลังใช้งานอยู่, การสตรีมเมื่อเปิดเครื่อง และ PTP. `pool-set-network` จะรีสตาร์ทเซ็นเซอร์เพื่อนำการเปลี่ยนแปลงไปใช้และเชื่อมต่อใหม่ด้วยตั้งค่าเดิม; `--no-reboot` จะบันทึกตั้งค่าไว้เท่านั้น เครือข่ายของคอมพิวเตอร์นี้ต้องมีที่อยู่แบบคงที่ (static address) มิฉะนั้นChloros จะปฏิเสธ; `--force` จะข้ามข้อจำกัดนี้ แต่เฉพาะสำหรับคอมพิวเตอร์ที่จะถูกกำหนดที่อยู่ใหม่หรือถูกส่งต่อไปยังที่อยู่ดังกล่าวเท่านั้น การรีสตาร์ทจะถูกปฏิเสธในขณะที่เซ็นเซอร์กำลังบันทึกข้อมูล `pool-update-firmware` คือ **Update to \<version\>**</version\> ของ GUI <version\>สำหรับบรรทัดคำสั่ง และเป็นวิธีอัปเดต DAQ-E บนLinux ซึ่งไม่มี GUI เพิ่มเข้ามาในเวอร์ชัน 1.3.0.

***

## สรุปคำสั่ง

| คำสั่ง | วัตถุประสงค์ |
| --- | --- |
| `daq pool-connect [--port P \| --ble \| --mac M \| --eth \| --eth-host H] [-t MS] [-f N] [--no-ae] [--no-stream] [--cap-id CAP]` | เปิดเซ็นเซอร์ในพูลแบ็กเอนด์ |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | สแกนหาเซ็นเซอร์โดยไม่เชื่อมต่อ (ค้นหา BLE MAC ของ DAQ-M) |
| `daq pool-list` | แสดงเซ็นเซอร์ทุกตัวในพูลพร้อมค่า `sensor_id` ของมัน |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | เฟรมสเปกตรัมที่ได้รับการปรับเทียบล่าสุด N เฟรม |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | ดำเนินการสตรีมต่อ / หยุดชั่วคราว |
| `daq pool-record --sensor-id ID [-d SEC] [-o DIR] [--device-name NAME] [--confirm-after SEC] [--heartbeat SEC] [--stop]` | เริ่ม / หยุดการบันทึก `.daq` (ด้านแบ็กเอนด์) |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | เปลี่ยนโปรไฟล์การปรับค่าแคปในช่วงการทำงาน |
| `daq pool-disconnect --sensor-id ID [--all]` | ปล่อยเซ็นเซอร์หนึ่งตัว หรือทั้งหมด |
| `daq pool-enable-ptp --sensor-id ID` | เปิด PTP กลับสำหรับ DAQ-E ที่ PTP ถูกปิด |
| `daq pool-info --sensor-id ID [--json]` | เฟิร์มแวร์, ที่อยู่, การตั้งค่าเครือข่ายที่บันทึกไว้, การสตรีมเมื่อเปิดเครื่อง และ PTP ของ DAQ-E |
| `daq pool-set-network --sensor-id ID (--auto \| --static IP [--mask M] [--gateway G]) [--no-reboot] [--force]` | DHCP หรือที่อยู่แบบคงที่; รีสตาร์ทและเชื่อมต่อเซ็นเซอร์ใหม่ |
| `daq pool-set-auto-stream --sensor-id ID (--on \| --off)` | ส่งข้อมูลแบบสตรีมเมื่อเปิดเครื่อง |
| `daq pool-reboot --sensor-id ID` | รีสตาร์ท DAQ-E และเชื่อมต่อใหม่ |
| `daq pool-update-firmware --sensor-id ID` | ติดตั้งเฟิร์มแวร์ DAQ-E ของเวอร์ชันนี้ลงบนเซ็นเซอร์รุ่นเก่า |

***

## การแก้ไขปัญหาการเชื่อมต่อ DAQ-E ครั้งแรก

1. DAQ-E ไม่มีไฟ LED แสดงสถานะ — ตรวจสอบการจ่ายไฟผ่านตัวบ่งชี้ PoE/link บนสวิตช์หรือพอร์ตอินเจกเตอร์ และรอสักครู่หลังจากเปิดเครื่องเพื่อให้มันบูตและเข้าร่วมเครือข่าย
2. เครื่องเซิร์ฟเวอร์ต้องอยู่ใน **โดเมนการแพร่กระจายเดียวกัน** กับเซ็นเซอร์ — mDNS ไม่สามารถผ่านเราเตอร์ได้
3. ไฟร์วอลล์ต้องอนุญาตการผ่านของ mDNS (UDP 5353), ข้อมูล DAQ-E (UDP 5002) และ PTP (UDP 319/320) โปรแกรมติดตั้งWindows จะเพิ่มกฎสำหรับเรื่องนี้ และ `.deb` สำหรับLinux จะเปิดช่องเหล่านี้ใน `ufw` / `firewalld` ที่กำลังทำงานอยู่ขณะติดตั้ง; ไฟร์วอลล์ของบุคคลที่สาม หรือไฟร์วอลล์ที่ถูกเปิดใช้งานในภายหลัง ต้องอนุญาตให้พอร์ตเหล่านี้ผ่านได้
4. ยังไม่มีสัญญาณจาก `--eth`? ใช้ `--eth-host` พร้อมชื่อโฮสต์ (`daq-e-<id>.local`) หรือ IP ของอุปกรณ์ ซึ่งจะข้ามขั้นตอนการค้นหา

***
{% hint style="info" %}
**เคล็ดลับสำหรับผู้ช่วย AI** ทุกหน้าในคู่มือนี้ถูกส่งในรูปแบบ Markdown ดิบ — ให้เพิ่ม `.md` ไปยัง slug URL ตัวพิมพ์เล็กของหน้า (หน้านี้: `https://mapir.gitbook.io/chloros/daq/cli-quick-start.md`); ดัชนีที่เครื่องอ่านได้คือ `https://mapir.gitbook.io/chloros/llms.txt`. เพื่อดูเอกสารระดับแฟล็กที่ครบถ้วนของ `chloros-cli daq` และกลุ่มคำสั่งอื่น ๆ ทั้งหมด ให้ดาวน์โหลด [คู่มืออ้างอิง CLI](../reference/cli-reference.md) (`https://mapir.gitbook.io/chloros/reference/cli-reference.md`); เส้นทาง Python คือ `chloros_sdk.connect_daq_sensor()` ใน [SDK Reference](../reference/sdk-reference.md).
{% endhint %}
</version\>
