# การใช้Chloros กับผู้ช่วย AI

คู่มือนี้เขียนขึ้นสำหรับสองกลุ่มผู้อ่าน: มนุษย์ และผู้ช่วย AI ที่มนุษย์ใช้ทำงานผ่านมากขึ้นเรื่อยๆ ทุกหน้าจะแสดงค่าที่แม่นยำ ค่าเริ่มต้น และคำสั่งที่สามารถคัดลอกและวางได้ เพื่อให้ผู้ช่วย (Claude, ChatGPT, Copilot, ตัวแทนการเขียนโค้ด, …) สามารถเขียนระบบอัตโนมัติChloros ที่ทำงานได้ตั้งแต่ครั้งแรก

Chloros เวอร์ชัน: **

1.3.0**. แพลตฟอร์ม CLI/SDK: Windows 10/11 x64 และ Linux (x86\_64 / Jetson aarch64).

## สิ่งที่ควรส่งให้ผู้ช่วย

| ทรัพยากร | URL | วัตถุประสงค์ |
| --- | --- | --- |
| **llms.txt** | `https://mapir.gitbook.io/chloros/llms.txt` | ดัชนีที่เครื่องอ่านได้ของทุกหน้าในคู่มือนี้ |
| **คู่มือ CLI** | `https://mapir.gitbook.io/chloros/reference/cli-reference` | พื้นผิวคำสั่ง `chloros-cli` แบบครบถ้วน: ทุกคำสั่ง, แฟล็ก, ค่าเริ่มต้น, รหัสออก และกฎของโฟลเดอร์ผลลัพธ์ เขียนขึ้นเพื่อการใช้งานโดย LLM |
| **คู่มืออ้างอิง SDK** | `https://mapir.gitbook.io/chloros/reference/sdk-reference` | Python API `chloros_sdk` แบบครบถ้วน: คลาส, ลายเซ็น, ข้อยกเว้น และตัวอย่างการใช้งานที่ทำงานได้จริง เขียนขึ้นเพื่อใช้กับ LLM |
| **หน้าใดก็ตามในรูปแบบ Markdown ดิบ** | เพิ่ม `.md` ลงใน URL ของหน้า | ตัวอย่างเช่น `https://mapir.gitbook.io/chloros/reference/sdk-reference.md` จะส่งคืนหน้าในรูปแบบ Markdown ดิบ — เหมาะสำหรับการวางลงในหน้าต่างบริบทหรือดึงข้อมูลจากเอเจนต์ |

ลิงก์ภายในคู่มือ: [คู่มือ CLI](reference/cli-reference.md) · [คู่มือ SDK](reference/sdk-reference.md).

{% hint style="info" %}
หน้าอ้างอิงทั้งสองหน้าเป็นเนื้อหาที่ครบถ้วนในตัวเอง: ผู้ช่วยที่อ่านหน้าใดหน้าหนึ่งแล้วไม่จำเป็นต้องใช้ส่วนอื่นของคู่มือเพื่อเขียนสคริปต์ที่ถูกต้อง
{% endhint %}

## ให้ผู้ช่วยของคุณเรียกใช้Chlorosโดยตรง: `chloros-mcp`

ทุกขั้นตอนข้างต้นทำให้ผู้ช่วยของคุณ *เขียน* สคริปต์หรือคำสั่ง ด้วย `chloros-mcp` ผู้ช่วยที่สนับสนุน [Model Context Protocol](https://modelcontextprotocol.io) (MCP) เช่น Claude Desktop, Claude Code หรือ Cursor จะควบคุมChloros ได้ด้วยตัวเอง คุณเพียงถามด้วยภาษาธรรมดา และมันจะเรียกเครื่องมือของเซิร์ฟเวอร์:

* **ประมวลผลภาพ:** สร้างโครงการ นำเข้าโฟลเดอร์ภาพที่ถ่ายไว้ เลือกการตั้งค่า ประมวลผล และรายงานผลลัพธ์ที่การรันส่งออก
* **ควบคุมอุปกรณ์:** เชื่อมต่อกล้อง LATTICE, อาร์เรย์ และเซ็นเซอร์แสง DAQ, ดูตัวอย่าง, ตั้งค่าการเปิดรับแสงและสี, ถ่ายภาพ, บันทึก และอัปเดตเฟิร์มแวร์

```text
You:  Process the flight in D:\Flights\2026-10-17 to reflectance and NDVI.

You:  Show me what the array sees, then capture reflectance every 10 seconds
      for 5 minutes.
```

เครื่องมือของเซิร์ฟเวอร์ครอบคลุมฟังก์ชันที่ [Python SDK](api-python-sdk.md) ทำได้ มันถูกติดตั้งพร้อมกับSDK ดังนั้นโดยทั่วไปไม่จำเป็นต้องติดตั้งอะไรเพิ่มเติม: บนWindows โปรแกรมติดตั้งChloros จะวาง `chloros-mcp.exe` ในโฟลเดอร์ `Scripts` ของPython ของคุณ, ส่วน `.deb` สำหรับLinux จะวาง `chloros-mcp` ใน `/usr/bin`

**Claude Code:**

```bash
claude mcp add chloros -- chloros-mcp
```

**Claude Desktop:**เพิ่มข้อมูลนี้ลงใน `claude_desktop_config.json` (**Settings &gt; Developer &gt; Edit Config**เพื่อเปิดไฟล์), แล้วรีสตาร์ท Claude Desktop**Cursor** ใช้ข้อมูลเดียวกันใน `~/.cursor/mcp.json`

```json
{
  "mcpServers": {
    "chloros": {"command": "chloros-mcp"}
  }
}
```

หากไคลเอนต์แจ้งว่าเซิร์ฟเวอร์ไม่สามารถเริ่มต้นได้ `chloros-mcp` อาจไม่ได้อยู่ใน `PATH` ที่ไคลเอนต์เห็น: ให้ระบุเส้นทางเต็มแทน (`where chloros-mcp` บน Windows, `which chloros-mcp` บน Linux)

คุณต้องติดตั้ง Chloros บนคอมพิวเตอร์เดียวกัน และลงชื่อเข้าใช้ที่นั่น ในแอป หรือผ่าน `chloros-cli login` บนแพ็กเกจ Chloros+ แบบชำระเงินใดก็ตาม (Copper หรือระดับที่สูงกว่า) เซิร์ฟเวอร์ไม่สามารถลงชื่อเข้าใช้แทนคุณได้

{% hint style="info" %}
การตั้งค่าไคลเอนต์สำหรับแต่ละแอป ตัวเลือก เครื่องมือทุกชนิด และวิธีการทำงานของกระบวนการ: [MCP Server (AI Assistants)](reference/sdk-reference.md#mcp-server-ai-assistants) ในคู่มืออ้างอิงSDK
{% endhint %}

## สูตรคำสั่ง

คัดลอก, กรอกข้อมูลใน `<placeholders>`, แล้ววางลงในผู้ช่วยของคุณ

### 1. แปลงโฟลเดอร์การบินเป็นNDVI

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
การเขียนสคริปต์ DAQ จากบรรทัดคำสั่งจะผ่านชุด `daq pool-*` เสมอ (`pool-connect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`, `pool-disconnect`). คำสั่งย่อย `daq` อื่นๆ ที่ผู้ช่วยของคุณอาจสร้างขึ้นนั้น ไม่มีอยู่ในเวอร์ชันที่ปล่อยออกและจะปิดตัวลงพร้อมข้อผิดพลาด
{% endhint %}

## เหตุผลที่สคริปต์ที่เขียนโดย AI ทำงานได้ดีกับ Chloros

แต่ละข้อนี้คือพฤติกรรมจริงที่ได้รับการตรวจสอบแล้วของ Chloros 1.3.0 — ซึ่งช่วยขจัดรูปแบบความล้มเหลวแบบคลาสสิกของระบบอัตโนมัติที่เขียนโดยเครื่อง:

* **ไม่ต้องผ่านขั้นตอนการตั้งค่าที่ซับซ้อน**ตัวช่วย smart-connect ของSDK (`connect_camera`, `connect_array`, `connect_daq_sensor`) และจุดเข้าการประมวลผล (`ChlorosLocal`, `process_folder`)**จะเริ่มต้นระบบหลังบ้านท้องถิ่นโดยอัตโนมัติ** สคริปต์ที่สร้างขึ้นไม่จำเป็นต้องเปิด GUI หรือเริ่มต้นเซิร์ฟเวอร์ด้วยมือ — จำเป็นต้องติดตั้งแพ็กเกจเดสก์ท็อป/CLI เท่านั้น
* **ทั้งกระบวนการเป็นเพียงการเรียกใช้ครั้งเดียว** `chloros_sdk.process_folder("path", indices=["NDVI"])` ดำเนินการตั้งแต่การนำเข้า → การปรับเทียบ → การวัดการสะท้อน → การส่งออกดัชนีแบบครบวงจร พื้นที่ผิวที่น้อยลง ทำให้สคริปต์ที่สร้างขึ้นมีโอกาสเกิดข้อผิดพลาดน้อยลง
* **การรันที่ไม่มีผลลัพธ์จะทำการวินิจฉัยตัวเอง** หลังจาก `process()` สรุปผลการรันจะถูกแนบไปกับผลลัพธ์ และทุกคำแนะนำในการประมวลผล (เช่น *เหตุผล* ที่การรันไม่สร้างผลลัพธ์) จะถูกส่งออกมาใหม่ในรูปแบบสคริปต์Python `UserWarning` — ดังนั้น แม้สคริปต์ที่ไม่ตรวจสอบผลลัพธ์ dict ก็ยังแสดงผลการวินิจฉัยได้
* **CLI จะแสดงข้อผิดพลาดอย่างชัดเจน**การรัน `chloros-cli process` ที่ขอผลิตภัณฑ์แต่ไม่เขียนอะไรเลย จะพิมพ์ `Processing finished but wrote no image products.` และ**ออกด้วยค่าที่ไม่ใช่ศูนย์** ทำให้สคริปต์เชลล์และ CI สามารถตรวจจับได้ด้วยการตรวจสอบรหัสออกอย่างง่าย การรันที่สำเร็จจะรายงาน `Image products written: N` และ `(plus M clamp masks)` เมื่อมีการเขียนมาสก์ คำสั่ง capture จะระบุชื่อไฟล์ทุกไฟล์ที่ไม่สามารถเขียนได้และออกด้วยรหัส `1`
* **เส้นทางสัมพัทธ์มีความหมายตามที่สคริปต์คาดหวัง** เมื่อใช้แบ็กเอนด์ท้องถิ่น เส้นทางผลลัพธ์สัมพัทธ์ (`-o out/` ใน CLI, `output_dir="out/"` ใน SDK) จะถูกกำหนดโดยอ้างอิงจากโฟลเดอร์ที่คำสั่งหรือสคริปต์กำลังทำงานอยู่

ความไม่สมมาตรหนึ่งที่ผู้ช่วยควรทราบ: `process()` ของ SDK **ไม่** สร้างข้อผิดพลาดโดยเจตนาเมื่อการรันไม่มีผลลัพธ์ — แต่จะรายงานผ่านส่วนสรุป/คำแนะนำแทน หาก pipeline Python ต้องหยุดเมื่อการรันไม่มีผลลัพธ์ ให้ตรวจสอบสรุป (สูตรที่ 2 ทำเช่นนี้)

## ข้อควรระวัง

* **ต้องเข้าสู่ระบบ Chloros+**CLI และ SDK ต้องการ**ระดับสมาชิก**Chloros+ แบบ**เสียเงิน** ซึ่งถูกบังคับใช้ด้านเซิร์ฟเวอร์: คำขอจะล้มเหลวด้วยรหัสข้อผิดพลาด `401 AUTH_REQUIRED` เมื่อไม่เข้าสู่ระบบ และ `403 PLAN_UPGRADE_REQUIRED` สำหรับระดับสมาชิกฟรี เรียกใช้ `chloros-cli login` หนึ่งครั้งต่อเครื่องก่อนที่จะเรียกใช้สคริปต์ที่สร้างขึ้น ดู [Chloros+ Login](chloros+-login.md).
* **คำสั่ง Capture ควบคุมอุปกรณ์ฮาร์ดแวร์จริง** `lattice` / `daq` / `project`, อובייジェクトเซสชันSDK และเครื่องมือของ `chloros-mcp` จะเชื่อมต่อ ส่งข้อมูลแบบสตรีม และกระตุ้นกล้องและเซ็นเซอร์ทางกายภาพ ตรวจสอบสคริปต์ที่สร้างขึ้นก่อนการรันครั้งแรก และรันสคริปต์ดังกล่าวโดยมีผู้ดูแลอุปกรณ์อยู่ด้วย
* **ตรวจสอบผลลัพธ์แบบสุ่ม** ตรวจสอบโฟลเดอร์ผลิตภัณฑ์และค่าพิกเซลบางค่าก่อนเผยแพร่ผลลัพธ์ โดยเฉพาะอย่างยิ่ง ไฟล์ TIFF ของค่าสะท้อนแสงจะถูกปรับขนาดตามแหล่งที่มา — อ่านแท็ก XMP ของ `Chloros:PixelScale` (ไฟล์ LATTICE มีแท็กนี้: 32768 = 1.0 ค่าสะท้อนแสง; ไฟล์ Survey3 ไม่มี และใช้ 65535 = 1.0) แทนที่จะสมมติตัวหาร ทั้งสองหน้าเอกสารอ้างอิงได้บันทึกข้อมูลนี้ไว้ภายใต้หัวข้อ &quot;Reading reflectance pixels&quot;
* **ข้อควรระวังเล็กๆ ที่อาจทำให้โค้ดที่สร้างขึ้นเกิดข้อผิดพลาด:**`pool-record` เขียนข้อมูลลงระบบไฟล์ของ**เครื่องโฮสต์แบ็กเอนด์** (ค่าเริ่มต้นคือ `~/Documents/DAQ Live View/`); บนเครื่องที่มีอินเทอร์เฟซเครือข่ายหลายตัว ให้เลือก `daq pool-connect --eth-host <ip-or-hostname>` แทนการค้นหาอัตโนมัติ; ใช้ `http://127.0.0.1:5000` (อย่าใช้ `localhost`) ในทุกที่ที่มีURL ของระบบหลัง; สคริปต์ที่เดินผ่านโครงสร้างไฟล์ผลลัพธ์ของโครงการควรข้ามโฟลเดอร์ `clamp_masks/` (ไฟล์ `*_clampmask.tif` ในโฟลเดอร์เหล่านั้นเป็นมาสก์ ไม่ใช่ภาพ — ดู [Output Image Formats](output-image-formats.md#clamp-masks)); และบนWindows ที่ปิดการใช้งานเส้นทางยาว (long paths) ให้ตั้งชื่อโฟลเดอร์โครงการให้สั้น — โฟลเดอร์โครงการที่มีชื่อยาวกว่าประมาณ 130 ตัวอักษรจะถูกปฏิเสธก่อนที่การประมวลผลจะเริ่มขึ้น
