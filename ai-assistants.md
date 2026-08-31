# การใช้ Chloros กับผู้ช่วย AI

คู่มือนี้เขียนขึ้นสำหรับสองกลุ่มผู้อ่าน: มนุษย์ และผู้ช่วย AI ที่มนุษย์ใช้ทำงานร่วมกันมากขึ้นเรื่อยๆ ทุกหน้าในคู่มือนี้ระบุค่าที่แม่นยำ ค่าเริ่มต้น และคำสั่งที่สามารถคัดลอกและวางได้ เพื่อให้ผู้ช่วย (Claude, ChatGPT, Copilot, ตัวแทนการเขียนโค้ด, …) สามารถเขียนระบบอัตโนมัติ Chloros ที่ทำงานได้ตั้งแต่ครั้งแรก

Chloros เวอร์ชัน: **

1.2.0**. CLI / SDK แพลตฟอร์ม: Windows 10/11 x64 และ Linux (x86\_64 / Jetson aarch64).

## สิ่งที่ควรส่งให้ผู้ช่วย

| ทรัพยากร | URL | วัตถุประสงค์ |
| --- | --- | --- |
| **llms.txt** | `https://mapir.gitbook.io/chloros/llms.txt` | ดัชนีที่เครื่องอ่านได้ของทุกหน้าในคู่มือนี้ |
| **คู่มืออ้างอิง CLI** | `https://mapir.gitbook.io/chloros/reference/cli-reference` | พื้นผิวคำสั่ง `chloros-cli` แบบครบถ้วน: ทุกคำสั่ง, แฟล็ก, ค่าเริ่มต้น, รหัสออก และกฎของโฟลเดอร์ผลลัพธ์ เขียนขึ้นเพื่อการใช้งานโดย LLM |
| **คู่มืออ้างอิง SDK** | `https://mapir.gitbook.io/chloros/reference/sdk-reference` | คู่มืออ้างอิง `chloros_sdk` Python API แบบครบถ้วน: คลาส, ซิกเนเจอร์, ข้อยกเว้น และตัวอย่างที่ทำงานได้ เขียนขึ้นเพื่อใช้กับ LLM |
| **หน้าใดก็ได้ในรูปแบบ Markdown ดิบ** | เพิ่ม `.md` ลงในหน้า URL | ตัวอย่างเช่น `https://mapir.gitbook.io/chloros/reference/sdk-reference.md` จะส่งคืนหน้าในรูปแบบ Markdown ดิบ — เหมาะสำหรับการวางลงในหน้าต่างบริบทหรือดึงข้อมูลจากเอเจนต์ |

ลิงก์ภายในคู่มือ: [CLI Reference](reference/cli-reference.md) · [SDK Reference](reference/sdk-reference.md).

{% hint style="info" %}
หน้าอ้างอิงทั้งสองหน้าเป็นเนื้อหาที่ครบถ้วนในตัวเอง: ผู้ช่วยที่อ่านหน้าใดหน้าหนึ่งแล้วไม่จำเป็นต้องใช้ส่วนอื่นของคู่มือเพื่อเขียนสคริปต์ที่ถูกต้อง
{% endhint %}

## ตัวอย่างคำสั่ง

คัดลอก, กรอกข้อมูลใน `<placeholders>`, แล้ววางลงในผู้ช่วยของคุณ

### 1. ประมวลผลโฟลเดอร์การบินเป็น NDVI

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md.
Then write a script for <Windows PowerShell | bash> that:
1. logs in with `chloros-cli login <email> '<password>'` (only needed once per machine),
2. processes the folder <path/to/flight_001> with reflectance and the NDVI index,
3. prints where each output product landed, using the reference's
   "Where the outputs land" folder rules.
```

### 2. ตรวจสอบไดเรกทอรี captures แบบแบทช์

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (sections
"Quickstart" and "Post-Run Summary & Hints"). Write a Python script that
watches <path/to/captures> for new flight subfolders and runs
chloros_sdk.process_folder() with indices=["NDVI"] on each new one.
After each run, print every hint from result["summary"]["hints"] and treat
a run with zero image products as a failure for that folder.
```

### 3. เชื่อมต่ออาร์เรย์ LATTICE และบันทึกข้อมูล

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (section
"connect_array"). Write a Python script that connects my LATTICE cameras
with serials <213800234, 214000533, ...> as one synchronized array, captures
a reflectance image set into <output/> every 10 seconds for one hour, and
disconnects cleanly when done (use the context-manager form).
```

### 4. บันทึกสเปกตรัมของเซ็นเซอร์แสง DAQ

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md (section
"chloros-cli daq" — use only the pool-* commands). Write a script that:
1. connects my DAQ-E sensor with `chloros-cli daq pool-connect --eth-host <daq-e-xxxxxx.local>`,
2. lists the pool with `pool-list` to get the sensor id,
3. records a 10-minute calibrated .daq file named "<field-A>" with `pool-record`,
4. disconnects with `pool-disconnect`.
```

{% hint style="warning" %}
การเขียนสคริปต์ DAQ จากบรรทัดคำสั่งจะผ่านชุด `daq pool-*` เสมอ (`pool-connect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`, `pool-disconnect`) คำสั่งย่อย `daq` อื่นๆ ที่ผู้ช่วยของคุณอาจสร้างขึ้นนั้น ไม่มีอยู่ในเวอร์ชันที่จัดส่งและจะปิดตัวลงพร้อมข้อผิดพลาด
{% endhint %}

## เหตุผลที่สคริปต์ที่เขียนโดย AI ทำงานได้ดีกับ Chloros

แต่ละข้อนี้เป็นพฤติกรรมจริงที่ได้รับการตรวจสอบแล้วของ Chloros 1.2.0 — ซึ่งช่วยขจัดรูปแบบความล้มเหลวแบบคลาสสิกของระบบอัตโนมัติที่เขียนโดยเครื่อง:

* **ไม่ต้องผ่านขั้นตอนการตั้งค่าที่ซับซ้อน**ตัวช่วย smart-connect ของ SDK (`connect_camera`, `connect_array`, `connect_daq_sensor`) และจุดเข้าการประมวลผล (`ChlorosLocal`, `process_folder`)**จะเริ่มต้นระบบหลังบ้าน (backend) ท้องถิ่นโดยอัตโนมัติ** สคริปต์ที่สร้างขึ้นไม่จำเป็นต้องเปิด GUI หรือเริ่มต้นเซิร์ฟเวอร์ด้วยมือ — จำเป็นต้องติดตั้งแพ็กเกจ desktop/CLI เท่านั้น
* **ท่อประมวลผลทั้งหมดเป็นเพียงการเรียกใช้ครั้งเดียว** `chloros_sdk.process_folder("path", indices=["NDVI"])` ดำเนินการนำเข้า → การปรับเทียบ → การวัดการสะท้อน → การส่งออกดัชนี จากต้นจนจบ ด้วยพื้นที่ผิวที่น้อยลง ทำให้จุดที่สคริปต์ที่สร้างขึ้นอาจเกิดข้อผิดพลาดลดลง
* **การรันที่ไม่มีผลลัพธ์จะทำการวินิจฉัยตัวเอง** หลังจาก `process()` สรุปผลการรันจะถูกแนบไปกับผลลัพธ์ และทุกคำแนะนำในการประมวลผล (เช่น *เหตุผล* ที่การรันไม่สร้างผลลัพธ์) จะถูกส่งออกมาใหม่ในรูปแบบของ Python `UserWarning` — ดังนั้น แม้แต่สคริปต์ที่ไม่ตรวจสอบผลลัพธ์เลย ก็จะแสดงผลการวินิจฉัยออกมา
* **CLI จะล้มเหลวอย่างชัดเจน**การรัน `chloros-cli process` ที่ขอผลิตภัณฑ์แต่ไม่เขียนอะไรเลย จะพิมพ์ `Processing finished but wrote no image products.` และ**ออกด้วยค่าที่ไม่เท่ากับศูนย์** ดังนั้นสคริปต์เชลล์และ CI สามารถตรวจจับได้ด้วยการตรวจสอบรหัสออกอย่างง่าย การรันที่สำเร็จจะรายงาน `Image products written: N`.

ความไม่สมมาตรหนึ่งที่ผู้ช่วยควรรู้: `process()` ของ SDK จะ **ไม่** แจ้งข้อผิดพลาดโดยเจตนาเมื่อการรันไม่มีผลิตภัณฑ์ — แต่จะรายงานผ่านส่วนสรุป/คำแนะนำแทน หาก pipeline ของ Python ต้องหยุดเมื่อการรันว่างเปล่า ให้ตรวจสอบส่วนสรุป (สูตร 2 ทำเช่นนี้)

## ข้อควรระวัง

* **Chloros+ ต้องเข้าสู่ระบบ**CLI และ SDK ต้องการ**แพ็กเกจแบบเสียค่าใช้จ่าย** Chloros+ ซึ่งถูกบังคับใช้จากฝั่งเซิร์ฟเวอร์: คำขอจะล้มเหลวด้วยรหัสข้อผิดพลาด `401 AUTH_REQUIRED` เมื่อยังไม่ได้เข้าสู่ระบบ และ `403 PLAN_UPGRADE_REQUIRED` สำหรับแพ็กเกจฟรี รัน `chloros-cli login` ครั้งเดียวต่อเครื่องก่อนที่จะรันสคริปต์ที่สร้างขึ้น ดู [Chloros+ Login](chloros+-login.md).
* **คำสั่ง Capture ควบคุมอุปกรณ์ฮาร์ดแวร์จริง** คำสั่ง `lattice` / `daq` / `project` และวัตถุเซสชัน SDK ใช้เพื่อเชื่อมต่อ ส่งข้อมูลแบบสตรีม และสั่งให้กล้องและเซ็นเซอร์ทางกายภาพทำงาน ตรวจสอบสคริปต์ที่สร้างขึ้นก่อนการรันครั้งแรก และรันสคริปต์นั้นโดยมีผู้ดูแลอุปกรณ์อยู่ด้วย
* **ตรวจสอบผลลัพธ์แบบสุ่ม** ตรวจสอบโฟลเดอร์ผลิตภัณฑ์และค่าพิกเซลบางค่าก่อนเผยแพร่ผลลัพธ์ โดยเฉพาะอย่างยิ่ง ไฟล์ TIFF ของค่าการสะท้อนแสงจะถูกปรับขนาดตามแหล่งที่มา — อ่านแท็ก XMP ของ `Chloros:PixelScale` (LATTICE: 32768 = 1.0 reflectance; Survey3: 65535) แทนที่จะสมมติตัวหาร ทั้งสองหน้าเอกสารอ้างอิงได้บันทึกข้อมูลนี้ไว้ภายใต้หัวข้อ &quot;Reading reflectance pixels&quot;
* **ข้อควรระวังเล็กๆ ที่อาจทำให้โค้ดที่สร้างขึ้นเกิดข้อผิดพลาด:**`pool-record` เขียนข้อมูลลงในระบบไฟล์ของ**เครื่องโฮสต์แบ็กเอนด์** (ค่าเริ่มต้นคือ `~/Documents/DAQ Live View/`); บนเครื่องที่มีอินเทอร์เฟซเครือข่ายหลายตัว ให้เลือกใช้ `daq pool-connect --eth-host <ip-or-hostname>` แทนการค้นพบอัตโนมัติ; และใช้ `http://127.0.0.1:5000` (อย่าใช้ `localhost`) ในทุกที่ที่ปรากฏ URL ของระบบโฮสต์แบ็กเอนด์
