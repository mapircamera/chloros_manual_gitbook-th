---
metaLinks: {}
---

#สวนสาธารณะ

<div data-full-width="false"><figure><img src=".gitbook/assets/chloros_logo_transparent.png" alt=""><figcaption></figcaption></figure></div>

Chloros เป็นแอพพลิเคชั่นจาก [MAPIR](https://www.mapir.camera) เพื่อที่จะใส่ภาพและข้อมูลอื่นๆ

***{% hint style="success" %}**ใน Chloros 1.1.0**: รองรับ Native Linux (amd64 และ arm64), ไดรฟ์ NVIDIA Jetson edge, Dynamic Compute Adaptation, ไปป์ไลน์ดู 4 ฟัง, คำสั่งและส่วน CLI ย้อนหลัง ดู [ดาวน์โหลด](download.md) สำหรับบันทึกการเปลี่ยนแปลงฉบับเต็ม
{% endhint %}

Chloros มี 3 วิธีการใช้งาน:

## Chloros: แอปพลิเคชัน GUI บนแพลตฟอร์ม

และแยกแบบสแตนด์อโลนพร้อมคุณสมบัติทั้งหมด _วินโดวส์เท่านั้น_

## [Chloros CLI: คุณสามารถใช้บรรทัดคำสั่ง](CLI.md)

หม้อแบตช์บรรทัดคำสั่งการทำงานของอัตโนมัติหัวคำบรรยายและการทำงานแบบไม่มีอยู่ใน **Windows, Linux amd64 และ Linux arm64 (NVIDIA Jetson)** _CLI ต้องมี Chloros+ เพื่อเข้าถึง_

## [Chloros API: Python SDK](api-python-sdk.md)

Python แบบโปรแกรมสำหรับระบบอัตโนมัติและสตาร์ทโฟลว์ในการเยี่ยมชมไลน์การวิจัยการล่องเรือของแอปพลิเคชัน Python และมีประสิทธิภาพและประสิทธิภาพของเครื่องมือที่สามารถใช้งานบน **ทุกแพลตฟอร์ม**ผ่าน `pip install chloros-sdk` _API ต้องมี Chloros+ เพื่อเข้าถึง_***

## แพลตฟอร์มที่รองรับ

| แพลตฟอร์ม | กุย | CLI | Python SDK |
| --- | --- | --- | --- |
| **Windows 10/11** | ใช่แล้ว | ใช่แล้ว | ใช่แล้ว |
| **Linux amd64 (x86_64)** | | ไม่ ใช่แล้ว | ใช่แล้ว |
| **Linux arm64 (NVIDIA Jetson)** | | ไม่ ใช่แล้ว | ใช่แล้ว |

สำหรับคำแนะนำในการเชื่อมต่อ Linux ส่วนส่วน [Linux & Edge Computing](linux/linux-overview.md)

***

## Chloros+

ฟังก์ชั่น Chloros จะใช้งานได้ฟรีๆ เลย แต่อาจจะพบส่วนประกอบต่างๆ มากมายที่แบบชำระเงินสำหรับ Chloros+ จะเป็นประโยชน์ต่อคุณด้วย Chloros+ เพื่อที่จะเรียนรู้คุณสมบัติใหม่ๆ เช่น:

* **การควบคุมแบบมัลติฟังก์ชั่น**: เพิ่มความรวดเร็วในประสิทธิภาพของภาพสำหรับโปรเจ็กต์ใหญ่โดยฮาร์ดแวร์ภาพผ่านแพทไลน์ไปพร้อมๆ กัน
* **การที่ GPU (CUDA)**: คำอธิบายโดยละเอียดของ GPU ที่เกิดขึ้นเพื่อเร่งขั้นตอนการทำงานของภาพให้ความเห็นของเราแนะนำ VRAM 4GB เพิ่มเติมเพื่อผลลัพธ์ที่ดีที่สุด
* **Chloros+**[**CLI**](CLI.md)**ไม่จำเป็น**: ขอรับ Chloros+ จากบรรทัดคำสั่งของระบบอัตโนมัติและรวมซอฟต์แวร์ทั้งหมด
* **Chloros+**[**API**](api-python-sdk.md)**สำหรับ:** แสวงหา Chloros+ จาก Python สำหรับการวิจัยแบบเป็นโปรแกรมสามารถทบทวนไปเพดานไลน์วิจัยของคุณ ประวัติของโฟลว์หาข้อมูลและแอปพลิเคชันอย่างเป็นทางการ
* **การใช้งานหลายอุปกรณ์**: บางครั้ง Chloros+ หลังจากนั้นให้ลงทะเบียนอุปกรณ์ได้มากกว่า 2 เครื่องใช้บัญชี MAPIR Cloud ของคุณเพื่อจัดการอุปกรณ์ที่ลงทะเบียนอย่างต่อเนื่องและเริ่มดำเนินการเพิ่มเติมอีกครั้ง Chloros+ ของคุณ
* **วิธีการ Debayer แบบ Aware ขั้นสูง:** การดาวน์โหลด Debayer แบบ Edge-Aware หัวหน้าโมดูลการควบคุมสัญญาณรบกวน AI/ML ฟังก์ชั่นขจัดสัญญาณรบกวนการ Debayer ถาม&#x20;
* **สูตรดัชนีหลายตัวดังกล่าวเป็นตัวอย่าง:** ใส่ดัชนีหลายตัวเพื่อตรวจสอบในเครื่องคำนวณแรสเตอร์ Chloros สำหรับความถี่และเซนด์บ็อกซ์ตรวจดู
* **Linux & Edge Computing:** รัน Chloros บนแพลตฟอร์ม Linux x86\_64 และ ARM64 และ NVIDIA Jetson สำหรับพอร์ตภาคสนามและ Edge ในปัจจุบัน [ นอกจากนี้ Linux](linux/linux-overview.md)

<p align="center"><a href="https://cloud.mapir.camera/pricing" class="button primary" data-icon="envira">Chloros+ ราคา &#x26; ลงทะเบียน</a></p>

<รูป><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

<รูป><img src=".gitbook/assets/chloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>

<รูป><img src=".gitbook/assets/chloros_grid_mode.gif" alt=""><figcaption></figcaption></figure>

<รูป><img src=".gitbook/assets/chloros_grid_meta.gif" alt=""><figcaption></figcaption></figure>

<รูป><img src=".gitbook/assets/chloros_map_markers.gif" alt=""><figcaption></figcaption></figure>

<รูป><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>