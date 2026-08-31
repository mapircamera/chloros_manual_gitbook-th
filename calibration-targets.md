---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# เป้าหมายการปรับเทียบ

MAPIRเสนอเป้าหมายการปรับเทียบหลากหลายแบบเพื่อรองรับการใช้งานในหลายด้าน T4-R50 ที่มีขนาดกะทัดรัดดังที่เห็นด้านล่างนี้ประกอบด้วยแผง 4 แผ่น ซึ่งได้รับการวัดค่าการสะท้อนแสงในช่วงความยาวคลื่น 250 - 2,500 นาโนเมตร

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>เป้าหมายอ้างอิงการสะท้อนแบบกระจาย T4 มีเส้นโค้งการสะท้อนดังต่อไปนี้, [ดาวน์โหลดข้อมูลที่นี่](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR การสะท้อนแสง T4 :: 250-2,500 นาโนเมตร</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR การสะท้อนแสง T4 :: 400-1,000 นาโนเมตร</p></figcaption></figure>เป้าหมายอ้างอิงแบบกระจายแสง T4P มีเส้นโค้งการสะท้อนแสงดังต่อไปนี้ [ดาวน์โหลดข้อมูลที่นี่](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 350-2500nm.jpg" alt=""><figcaption><p>MAPIR การสะท้อนแสง T4P :: 250-2500nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 400-1000nm.jpg" alt=""><figcaption><p>MAPIR ค่าสะท้อนแสง T4P :: 400-1000nm</p></figcaption></figure>เมื่อดูกราฟการสะท้อนแสง คุณจะเห็นว่าค่าต่างๆ แสดงความสัมพันธ์ระหว่างความยาวคลื่น (แกน x) กับเปอร์เซ็นต์การสะท้อนแสง (แกน y) เมื่อเราถ่ายภาพเป้าหมายการปรับเทียบ เราจะสร้างความสัมพันธ์ระหว่างค่าพิกเซลกับเปอร์เซ็นต์การสะท้อนแสง ภายในสเปกตรัมที่แต่ละแถบเซ็นเซอร์ของกล้องมีความไว

ซึ่งหมายความว่า สำหรับทุกภาพที่ถ่ายด้วยกล้องของเรา คุณสามารถใช้ภาพของเป้าหมายการสะท้อนแสงของเรา เช่น [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) หรือ [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125) เพื่อปรับเทียบภาพตามค่าการสะท้อนแสง เมื่อปรับเทียบเสร็จแล้ว พิกเซลแต่ละจุดในภาพจะมีค่าเท่ากับเปอร์เซ็นต์การสะท้อนแสง

สำหรับ **Survey3** หากส่งออกภาพที่ปรับเทียบแล้วในChloros

เป็นรูปแบบ JPG หรือTIFF

แบบทั่วไป ค่าเปอร์เซ็นต์การสะท้อนแสงจะถูกคำนวณโดยการหารค่าพิกเซลด้วยความลึกของบิตของรูปแบบภาพ ดังนั้นสำหรับ JPG ให้หารด้วย 255 และสำหรับTIFF

ให้หารด้วย 65,535 คุณยังสามารถเลือกรูปแบบการส่งออกเป็น PERCENT ในChloros

ได้ ซึ่งแต่ละพิกเซลจะมีค่าเปอร์เซ็นต์อยู่ในช่วง 0.0 ถึง 1.0 (การสะท้อนแสง 0% ถึง 100%) แต่ควรจำไว้ว่าโปรแกรมประมวลผลภาพบางตัวไม่สามารถรับภาพในรูปแบบเปอร์เซ็นต์ (จุดลอยตัว) ได้ และภาพประเภทนี้มีขนาดไฟล์ที่ใหญ่เมื่อเก็บรักษา

{% hint style="info" %}
**ค่าการสะท้อนแสง LATTICE ใช้มาตราส่วนพิกเซลที่ต่างกัน** ค่าการสะท้อนแสง LATTICE ถูกเก็บไว้โดยใช้ DN 32768 = 100% (ไม่ใช่ 65535) และทุกไฟล์จะมีแท็ก XMP `Chloros:PixelScale` ที่ระบุมาตราส่วนของมัน อ่านแท็กและหารด้วยค่านั้นแทนที่จะสมมติว่าเป็นค่าคงที่ — ดู [รูปแบบภาพผลลัพธ์](output-image-formats.md).
{% endhint %}

## เป้าหมายการปรับเทียบกับกล้อง LATTICE

สำหรับกล้อง LATTICE เป้าหมายการปรับเทียบ **เป็นตัวเลือก** สำหรับค่าการสะท้อนแสง:Chloros

สามารถอ้างอิงค่าการสะท้อนแสงไปยังความเข้มแสงที่ส่องลง (downwelling irradiance) ที่วัดโดยเซ็นเซอร์แสง DAQ (ρ = π·L/E) แทนได้ การอ้างอิงนี้ถูกเลือกผ่านการตั้งค่าแหล่งที่มาของการสะท้อน (Project Settings ใน GUI; `--reflectance-source` ในCLI

; `reflectance_source` ในSDK

):

| ค่า | พฤติกรรม |
| --- | --- |
| `auto` *(ค่าเริ่มต้น)* | เป้าหมายในเฟรมที่ผ่านการตรวจสอบคุณภาพ (QA) จะถือเป็น **อ้างอิงสัมบูรณ์**; เมื่อไม่มีเป้าหมายหรือการตรวจสอบคุณภาพ (QA) ล้มเหลว ระบบจะกลับใช้การแบ่งสัญญาณลง (downwelling divide) ของ DAQ เป็นค่าอ้างอิงแทน |
| `target` | เฉพาะเป้าหมายอย่างเคร่งครัด — ไม่มีการแทนที่ด้วย DAQ |
| `daq` | DAQ มีอำนาจสูงสุด — การวัดลงเสมอเป็นอ้างอิง |

พฤติกรรมเป้าหมายเพิ่มเติมสำหรับ LATTICE:

* **รูปทรงเป้าหมาย** — รองรับทั้งแผงที่มีเครื่องหมาย ArUco, แผง ROI คงที่ และเป้าหมายแบบแถบ; รูปทรงมาจากกำหนดค่าเป้าหมายของโครงการ
* **ข้อมูลเป้าหมายที่วัดต่อหน่วย** — `--target-reflectance-dir DIR` ชี้ไปยังไดเรกทอรีของสแกนการสะท้อนแสงเป้าหมายที่วัดต่อหน่วย (`<serial>.csv`, ค้นหาตามหมายเลขซีเรียล/QR ของหน่วยเป้าหมาย) หากไม่พบเป้าหมาย (miss),Chloros

จะใช้สเปกตรัม T3/T4P ที่กำหนดไว้เป็นค่าเริ่มต้น
* **การยึดเวลา** — เป้าหมายที่ตรวจพบจะปรับเทียบเฟรมรอบๆ และถูกเก็บไว้ระหว่างการตรวจพบเป้าหมาย

ความหมายของธงทั้งหมดและตัวอย่างอยู่ใน [CLI

Reference](reference/cli-reference.md) (ดู &quot;Per-Product Export Toggles&quot;)

### F988

&quot;ค่าสะท้อนแสง F988 ได้รับการปรับเทียบโดยใช้แผงสะท้อนแสงในฉาก: เนื่องจากแถบนี้อยู่นอกช่วงที่ปรับเทียบของเซ็นเซอร์แสง DAQ ดังนั้นChloros

จึงใช้ข้อมูลการจับภาพแผงล่าสุดของคุณและเก็บไว้ระหว่างการตรวจจับแผง&quot;

หาก F988 ทำงานด้วยการปรับเทียบเฉพาะ DAQ,Chloros

จะปฏิเสธค่าการสะท้อนแสงที่อิงจาก DAQ สำหรับแถบนั้นและอธิบายเหตุผล (เหตุผลการข้าม `dls-uncalibrated-band-988`); กระบวนการทำงานของแผงสะท้อนแสงเป็นวิธีที่ได้รับการสนับสนุน

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
