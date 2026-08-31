---
description: This page lists some multispectral indices that Chloros uses
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/multispectral-index-formulas
---

# สูตรดัชนีมัลติสเปกตรัม

สูตรดัชนีต่อไปนี้ใช้การผสมผสานช่วงการส่งผ่านเฉลี่ยของตัวกรองSurvey3:

<table><thead><tr><th align="center">Survey3 สีของฟิลเตอร์</th><th width="196.199951171875" align="center">Survey3 ชื่อตัวกรอง</th><th width="159.800048828125" align="center">ช่วงการส่งผ่าน (FWHM)</th><th align="center">การส่งผ่านเฉลี่ย</th></tr></thead><tbody><tr><td align="center">Blue</td><td align="center">NGB - Blue</td><td align="center">468-483nm</td><td align="center">475 นาโนเมตร</td></tr><tr><td align="center">Cyan</td><td align="center">OCN- Cyan</td><td align="center">476-512 นาโนเมตร</td><td align="center">494 นาโนเมตร</td></tr><tr><td align="center">Green</td><td align="center">RGN | NGB - Green</td><td align="center">543-558 นาโนเมตร</td><td align="center">547 นาโนเมตร</td></tr><tr><td align="center">Orange</td><td align="center">OCN - Orange</td><td align="center">598-640 นาโนเมตร</td><td align="center">619 นาโนเมตร</td></tr><tr><td align="center">Red</td><td align="center">RGN - Red</td><td align="center">653-668 นาโนเมตร</td><td align="center">661 นาโนเมตร</td></tr><tr><td align="center">RedEdge</td><td align="center">Re - RedEdge</td><td align="center">712-735 นาโนเมตร</td><td align="center">724 นาโนเมตร</td></tr><tr><td align="center">NIR1</td><td align="center">OCN - NIR1</td><td align="center">798-848 นาโนเมตร</td><td align="center">823 นาโนเมตร</td></tr><tr><td align="center">NIR2</td><td align="center">RGN | NGB | NIR - NIR2</td><td align="center">835-865 นาโนเมตร</td><td align="center">850 นาโนเมตร</td></tr></tbody></table>

เมื่อใช้สูตรเหล่านี้ ชื่ออาจลงท้ายด้วย &quot;\_1&quot; หรือ &quot;\_2&quot; ซึ่งสอดคล้องกับตัวกรองNIR ที่ใช้ คือ NIR1 หรือ NIR2

สำหรับกล้อง LATTICE M3C (Bayer triple-bandpass) เครื่องยนต์ดัชนีเดียวกันจะใช้แถบกรอง M3C ดังนี้:

| ตัวกรอง M3C | แถบ 1 (ศูนย์กลาง/ FWHM) | แถบ 2 (ศูนย์กลาง/ FWHM) | แถบ 3 (ศูนย์กลาง/ FWHM) |
| --- | --- | --- | --- |
| FRGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | Red 625 nm / 30 nm |
| FRGN | Red 660 nm / 21 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |
| FOCN | Orange 615 nm / 21 nm | Cyan 490 nm / 38 nm | NIR 808 nm / 14 nm |
| FNGB | Blue 475 nm / 30 nm | Green 550 nm / 30 nm | NIR 850 nm / 30 nm |

กล้อง LATTICE M3M เป็นกล้องแบบแถบเดียว (มีฟิลเตอร์แถบแคบหนึ่งตัวต่อกล้อง)ดังนั้น ดัชนีหลายแถบจึงไม่ถูกคำนวณสำหรับภาพ M3M เดี่ยว เพื่อคำนวณดัชนีด้วย M3M ให้รวมกล้องสองตัวหรือมากกว่าเป็นชุดภาพหลายแถบที่จัดแนวแล้ว และใช้เครื่องมือคำนวณดัชนี LATTICE (`chloros-cli lattice index` หรือ เครื่องคำนวณดัชนีแบบเรียลไทม์ใน GUI)

***

## สถานที่ที่ชื่อดัชนีแต่ละชื่อสามารถใช้งานได้

Chloros มี **สาม** พื้นผิวดัชนี และรายการตั้งค่าล่วงหน้าของแต่ละพื้นผิวไม่เหมือนกัน ใช้ส่วนนี้เพื่อตรวจสอบว่าชื่อดัชนีนั้นจะทำงานได้หรือไม่ในสถานที่ที่คุณวางแผนจะใช้

| สถานที่ที่คุณอยู่ | รายการใดที่ใช้ | จำนวน |
| --- | --- | --- |
| การตั้งค่าโครงการ → ดัชนี → เพิ่มดัชนี (GUI) | พื้นผิว 1 | 27 |
| Image Viewer [Index/LUT Sandbox](../image-viewer-gui/index-lut-sandbox.md) (GUI) | พื้นผิว 1 | 27 |
| `chloros-cli process --indices NDVI,NDRE` | Surface 2 | 22 |
| SDK `process_folder(indices=[...])` | Surface 2 | 22 |
| `chloros-cli lattice index --preset` | Surface 3 | 22 (22 ที่ต่างกัน) |
| แท็บ Cameras เครื่องคำนวณดัชนีแบบเรียลไทม์ | Surface 3 | 22 (22 ที่ต่างกัน) |

Surface 1 และ 2 ทำงานกับ **ภาพหนึ่งภาพต่อครั้งจากกล้องหนึ่งตัว**โดยใช้ช่องสัญลักษณ์ `x`/`y`/`z`(/`a`) ที่ผูกกับช่องกรองของกล้องนั้น Surface 3 ทำงานกับ**ชุดภาพหลายแถบที่จัดแนวแล้ว** — กล้อง LATTICE หลายตัวที่จัดแนวร่วมกันเป็นหนึ่งลูกบาศก์ — และอ้างอิงช่องด้วยชื่อตัวพิมพ์เล็ก

### 1. การตั้งค่าโครงการ GUI / เมนูแบบเลื่อนลงของ Image Viewer sandbox — 27 สูตร

เมนูแบบเลื่อนลงแสดงรายการตามลำดับนี้ (เป็นลำดับการแทรก ไม่ใช่ตามตัวอักษร):

`NDVI, GNDVI, CVI, ENDVI, EVI, MSR, OSAVI, TDVI, LAI, FCI1, FCI2, GARI, GCI, GEMI, GLI, GOSAVI, GRVI, GSAVI, LCI, MNLI, MSAVI2, NDRE, NLI, RDVI, SAVI, VARI, WDRVI`

ใน GUI คุณสามารถลากช่องกรองของกล้องของคุณไปยังช่องแถบของสูตรได้ ดังนั้นสูตรใดก็ตามสามารถใช้กับการกำหนดแถบใดก็ตามที่กล้องของคุณรองรับ สูตรที่คุณบันทึกไว้จะถูกเพิ่มต่อท้ายรายการนี้

**สูตร 5 สูตรที่ใช้ได้เฉพาะใน GUI** — สูตรที่รายการ CLI / SDK `--indices` ไม่รองรับ — ถูกนำไปใช้ดังนี้:

| การตั้งค่าล่วงหน้าเฉพาะ GUI | สูตร (ตามที่นำไปใช้) | ช่อง |
| --- | --- | --- |
| FCI1 | `x*y` | x, y |
| FCI2 | `x*y` | x, y |
| GARI | `(y-(x-1.7*(z-a)))/(y+(x-1.7*(z-a)))` | x, y, z, a (สี่ช่อง) |
| GEMI | `((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5))*(1-0.25*((2*(y*y-x*x)+1.5*y+0.5*x)/(y+x+0.5)))-((x-0.125)/(1-x))` | x, y |
| LCI | `(y-x)/(y+z)` | x, y, z |

การแมปปิ้งที่ตั้งใจไว้สำหรับแต่ละสูตรจะระบุไว้ในส่วนเฉพาะของมันเองด้านล่างหน้านี้ (ตัวอย่างเช่น GARI คาดหวัง x= Green, y= NIR, z= Blue, a= Red). GARI เป็นสูตรเดียวใน Chloros ที่ใช้ช่องที่สี่

### 2. CLI / SDK การขยายชื่อ `--indices` — 22 ค่ากำหนดล่วงหน้า

ตัวเลือก `chloros-cli process --indices` (และพารามิเตอร์ SDK `indices`) รับชื่อค่ากำหนดล่วงหน้าต่อไปนี้:

`NDVI, GNDVI, NDRE, OSAVI, SAVI, MSAVI2, EVI, MSR, TDVI, LAI, GCI, GRVI, GSAVI, GOSAVI, NLI, MNLI, RDVI, WDRVI, CVI, ENDVI, GLI, VARI`

{% hint style="warning" %}
**ชื่อดัชนีที่ไม่รู้จักจะถูกข้ามไปโดยไม่แจ้งเตือน** ชื่อที่อยู่นอกรายการนี้ (รวมถึงสูตร 5 สูตรที่ใช้เฉพาะใน GUI คือ `FCI1`, `FCI2`, `GARI`, `GEMI`, `LCI` และสูตรที่กำหนดเองใดๆ ที่คุณบันทึกไว้ใน GUI) จะถูกละเว้นโดยมีเพียงข้อความแจ้งเตือนในบันทึก — การรันจะดำเนินต่อไปโดยไม่มีดัชนีนั้น และการรันเองยังคงรายงานว่าสำเร็จ ข้อความแจ้งเตือนจะแสดงเป็น:

```
[INDEX_EXPAND] skipping unknown preset 'LCI'; known: ['CVI', 'ENDVI', 'EVI', ...]
```

ชื่อจะถูกเปรียบเทียบโดยไม่คำนึงถึงตัวพิมพ์ใหญ่-เล็ก หลังจากตัดช่องว่างออก ดังนั้น `ndvi`, `NDVI` และ ` NDVI ` จึงถือเป็นค่าตั้งล่วงหน้าเดียวกัน การตั้งค่าล่วงหน้าจะถูกข้ามไปหากต้องการช่วงความถี่ที่ตัวกรองของกล้องของคุณไม่รองรับ
{% endhint %}

สูตรที่นำไปใช้จริง (สัญลักษณ์ `x`/`y`/`z` เป็นช่องแถบความถี่; การกำหนดค่าเริ่มต้นแสดงตามแต่ละค่าตั้งล่วงหน้า):

| Preset | สูตร (ตามที่นำไปใช้) | ฟิลเตอร์เริ่มต้น | ช่อง (x, y, z) |
| --- | --- | --- | --- |
| NDVI | `(y-x)/(y+x)` | RGN | Red, NIR |
| GNDVI | `(y-x)/(y+x)` | RGN | Green, NIR |
| NDRE | `(y-x)/(y+x)` | RE | RE, NIR |
| OSAVI | `(y-x)/(y+x+0.16)` | RGN | Red, NIR |
| SAVI | `1.5*(y-x)/(y+x+0.5)` | RGN | Red, NIR |
| MSAVI2 | `(2*y+1-sqrt((2*y+1)*(2*y+1)-8*(y-x)))/2` | RGN | Red, NIR |
| EVI | `2.5*(y-x)/(y+6*x-7.5*z+1)` | RGN | Red, NIR, Blue |
| MSR | `((y/x)-1)/(sqrt(y/x)+1)` | RGN | Red, NIR |
| TDVI | `1.5*(y-x)/sqrt(y*y+x+0.5)` | RGN | Red, NIR |
| LAI | `3.618*(2.5*(y-x)/(y+6*x-7.5*z+1))-0.118` | RGN | Red, NIR, Blue |
| GCI | `(y/x)-1` | RGN | Green, NIR |
| GRVI | `y/x` | RGN | Green, NIR |
| GSAVI | `1.5*(y-x)/(y+x+0.5)` | RGN | Green, NIR |
| GOSAVI | `(y-x)/(y+x+0.16)` | RGN | Green, NIR |
| NLI | `((y*y)-x)/((y*y)+x)` | RGN | Red, NIR |
| MNLI | `((y*y-x)*(1+0.5))/((y*y)+x+0.5)` | RGN | Red, NIR |
| RDVI | `(y-x)/sqrt(y+x)` | RGN | Red, NIR |
| WDRVI | `(0.2*y-x)/(0.2*y+x)` | RGN | Red, NIR |
| CVI | `(z/y)/(x/y)` | RGB | Red, Green, Blue |
| ENDVI | `((x+y)-(2*z))/((x+y)+(2*z))` | RGB | Red, Green, Blue |
| GLI | `((y-x)+(y-z))/((2*y)+x+z)` | RGB | Red, Green, Blue |
| VARI | `(y-x)/(y+x-z)` | RGB | Red, Green, Blue |

#### วิธีที่ชื่อ preset กลายเป็นตำแหน่งในแบนด์

เมื่อคุณส่งชื่อเปล่า เช่น `NDVI`, Chloros ต้องตัดสินใจว่าสัญลักษณ์แต่ละตัวอ่านจากช่องใดของไฟล์ใด มันใช้ตารางนี้ ซึ่งแมปรหัสตัวกรองไปยังตำแหน่งในอาร์เรย์ของแต่ละช่อง:

| รหัสตัวกรอง | ช่อง → ดัชนีอาร์เรย์ |
| --- | --- |
| OCN | Orange 0, Cyan 1, NIR 2 (`Red` ถูกรับเป็นชื่อแทนสำหรับ Orange, ซึ่งก็คือ 0 เช่นกัน) |
| RGN | Red 0, Green 1, NIR 2 |
| NGB | NIR 0, Green 1, Blue 2 |
| RGB | Red 0, Green 1, Blue 2 |
| RE | RE 0 |
| NIR | NIR 0 |

**ตัวกรองเริ่มต้น** ของการตั้งค่าล่วงหน้า (คอลัมน์ &quot;Default filter&quot; ข้างต้น) จะถูกใช้เมื่อโครงการมีภาพที่ใช้ตัวกรองนั้น หากไม่มี Chloros จะสแกนตัวกรองที่มีอยู่ในโครงการตามลำดับ `RGN, OCN, NGB, RGB, RE, NIR` และเลือกตัวกรองแรกที่สามารถจัดหาทุกช่องสัญญาณที่พรีเซ็ตต้องการได้ หากไม่มีตัวกรองใดที่สามารถทำได้ การตั้งค่าล่วงหน้าจะถูกยกเลิกสำหรับการรันนั้น นี่คือเหตุผลที่ `NDVI` ที่ถูกเรียกใช้บนชุดข้อมูลที่มีเฉพาะ OCN ยังคงให้ผลลัพธ์ที่สมเหตุสมผล — มันผูกกับตำแหน่ง Orange และ NIR ของ OCN

สตริงแบบจำลอง LATTICE M3C นำตัวกรองมาพร้อมกับคำนำหน้า `F` (`LATT-M3C-L41-FRGN`), แต่คำนำหน้านี้จะถูกลบออกเมื่ออ่านรหัสตัวกรองจากภาพ ดังนั้นกล้อง FRGN จะทำการวิเคราะห์ผ่านแถว `RGN` ด้านบน และไม่จำเป็นต้องมีการจัดการพิเศษ

### 3. เครื่องยนต์ดัชนี LATTICE (`lattice index --preset`, เครื่องคำนวณดัชนีแบบเรียลไทม์) — 22 ค่าตั้งล่วงหน้า

เครื่องยนต์ LATTICE ทำงานกับชุดภาพหลายแถบที่จัดแนวแล้ว (อาร์เรย์แบบเรียลไทม์หรือไฟล์ TIFF หลายแถบที่ส่งออก) และใช้ชื่อช่องสัญญาณตัวพิมพ์เล็ก (`red`, `green`, `blue`, `red_edge`, `nir`). รายการตั้งค่าล่วงหน้าของมันแตกต่างจากสองตัวข้างต้น:

| การตั้งค่าล่วงหน้า | สูตร | ช่อง |
| --- | --- | --- |
| NDVI | `(nir - red) / (nir + red)` | red, nir |
| GNDVI | `(nir - green) / (nir + green)` | green, nir |
| BNDVI | `(nir - blue) / (nir + blue)` | blue, nir |
| NDRE | `(nir - red_edge) / (nir + red_edge)` | red\_edge, nir |
| ENDVI | `((nir + green) - 2*blue) / ((nir + green) + 2*blue)` | blue, green, nir |
| SAVI | `1.5 * (nir - red) / (nir + red + 0.5)` | แดง, nir |
| OSAVI | `1.5 * (nir - red) / (nir + red + 0.16)` | แดง, nir |
| MSAVI | `(2*nir + 1 - sqrt((2*nir + 1)**2 - 8*(nir - red))) / 2` | แดง, nir |
| EVI | `2.5 * (nir - red) / (nir + 6*red - 7.5*blue + 1)` | สีฟ้า, สีแดง, นีร์ |
| EVI2 | `2.5 * (nir - red) / (nir + 2.4*red + 1)` | สีแดง, นีร์ |
| CVI | `(nir / green) - (red / green)` | แดง, เขียว, นีร์ |
| MSR | `((nir/red) - 1) / (sqrt(nir/red) + 1)` | แดง, นีร์ |
| TDVI | `sqrt((nir - red) / (nir + red) + 0.5)` | แดง, นีร์ |
| LAI | `3.618 * ((nir - red) / (nir + 6*red - 7.5*green + 1)) - 0.118` | แดง, เขียว, นีร์ |
| GLI | `(2*green - red - blue) / (2*green + red + blue)` | แดง, เขียว, น้ำเงิน |
| NGRDI | `(green - red) / (green + red)` | แดง, เขียว |
| VARI | `(green - red) / (green + red - blue)` | แดง, เขียว, น้ำเงิน |
| TGI | `green - 0.39*red - 0.61*blue` | แดง, เขียว, น้ำเงิน |
| EXG | `2*green - red - blue` | แดง, เขียว, น้ำเงิน |
| CIRE | `(nir / red_edge) - 1` | แดง\_ขอบ, นีร์ |
| CIGREEN | `(nir / green) - 1` | เขียว, nir |
| NDWI | `(green - nir) / (green + nir)` | เขียว, nir |

เรียกใช้ `chloros-cli lattice index --list-presets` เพื่อพิมพ์ตารางนี้จากเวอร์ชันที่ติดตั้งไว้ และ `--list-gradients` เพื่อดูเฉดสีที่มีอยู่ สัญลักษณ์ช่องมีความไวต่อตัวพิมพ์ใหญ่-เล็ก และต้องตรงกับชื่อตัวพิมพ์เล็กของค่ากำหนดล่วงหน้า (เช่น `--channel red=Red_660 --channel nir=NIR_850`)

***

## CVI

ตามที่นำไปใช้ใน GUI และในรายการตั้งค่าล่วงหน้า CLI / SDK, CVI คือสูตรอัตราส่วนของอัตราส่วน:

$$
CVI = {(z / y) \over (x / y)}
$$

พร้อมการแมปช่องสัญญาณเริ่มต้นของ RGB คือ x= Red, y= Green, z= Blue ใน GUI คุณสามารถลากช่องสัญญาณใดก็ได้จากกล้องของคุณไปยังช่อง x/y/z ได้ โปรดทราบว่า การตั้งค่าล่วงหน้า `CVI` ของเครื่องยนต์ดัชนี LATTICE ใช้สูตรที่ต่างออกไป คือ `(NIR / Green) - (Red / Green)` — โปรดตรวจสอบตารางข้างต้นเพื่อหาค่าที่เหมาะสมกับพื้นผิวที่คุณกำลังใช้

***

## ENDVI - ดัชนีความแตกต่างของพืชพรรณที่ปรับมาตรฐานแล้วแบบปรับปรุง

ดัชนีนี้ใช้ช่องสีน้ำเงินร่วมกับNIRและสีเขียว และเป็นที่นิยมกับกล้องที่กรองด้วยNGB ซึ่งแถบสีน้ำเงินแทนที่แถบสีแดง

$$
ENDVI = {(NIR + Green) - (2 * Blue) \over (NIR + Green) + (2 * Blue)}
$$

การนำไปใช้คือสูตรสัญลักษณ์ `((x+y)-(2*z))/((x+y)+(2*z))` — กำหนดช่องสัญญาณ NIR และ Green ของกล้องของคุณไปยังช่อง x/y และ Blue ไปยังช่อง z (สำหรับกล้อง NGB: x= NIR, y= Green, z= Blue).

***

## EVI - ดัชนีพืชพรรณที่ปรับปรุงแล้ว

ดัชนีนี้ถูกพัฒนาขึ้นเดิมเพื่อใช้กับข้อมูล MODIS เพื่อปรับปรุงจาก NDVI โดยการเพิ่มประสิทธิภาพสัญญาณพืชพรรณในพื้นที่ที่มีดัชนีพื้นที่ใบสูง (LAI) ดัชนีนี้มีประโยชน์มากที่สุดในพื้นที่ที่มี LAI สูง ซึ่ง NDVI อาจเกิดการอิ่มตัว ดัชนีนี้ใช้ช่วงการสะท้อนแสงสีฟ้าเพื่อแก้ไขสัญญาณพื้นหลังของดินและลดอิทธิพลของบรรยากาศ รวมถึงการกระเจิงของแอโรซอล

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

ค่า EVI สำหรับพิกเซลพืชพรรณควรอยู่ในช่วง 0 ถึง 1 ลักษณะที่สว่าง เช่น เมฆและอาคารสีขาว รวมถึงลักษณะที่มืด เช่น น้ำ อาจก่อให้เกิดค่าพิกเซลที่ผิดปกติในภาพ EVI ก่อนสร้างภาพ EVI คุณควรใช้มาสก์เพื่อกำจัดเมฆและลักษณะที่สว่างออกจากภาพการสะท้อนแสง และอาจกำหนดค่าเกณฑ์ (threshold) ให้ค่าพิกเซลอยู่ในช่วง 0 ถึง 1

_อ้างอิง: Huete, A., et al. &quot;Overview of the Radiometric and Biophysical Performance of the MODIS Vegetation Indices.&quot; Remote Sensing of Environment 83 (2002):195–213._

***

## FCI1 - ดัชนีความปกคลุมของป่า 1

_ใช้ได้เฉพาะใน GUI — ไม่มีให้ใช้เป็นค่าตั้งล่วงหน้า (CLI / SDK `--indices`)_

ดัชนีนี้แยกแยะเรือนยอดป่าจากพืชพันธุ์ประเภทอื่นโดยใช้ภาพสะท้อนแสงแบบมัลติสเปกตรัมที่รวมแถบขอบสีแดง (red edge band)

$$
FCI1 = Red * RedEdge
$$

พื้นที่ป่าจะมีค่าFCI1ที่ต่ำกว่า เนื่องจากความสะท้อนแสงของต้นไม้ที่ต่ำลงและการมีเงาภายในชั้นเรือนยอด

_อ้างอิง: Becker, Sarah J., Craig S.T. Daughtry, and Andrew L. Russ. &quot;Robust forest cover indices for multispectral images.&quot; Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505-512._

***

## FCI2 - ดัชนีความปกคลุมของป่า 2

_ใช้ได้เฉพาะใน GUI — ไม่มีให้ใช้ในรูปแบบการตั้งค่าล่วงหน้า (CLI / SDK `--indices`)._

ดัชนีนี้แยกความแตกต่างระหว่างเรือนยอดป่ากับพืชพันธุ์ประเภทอื่น โดยใช้ภาพสะท้อนแสงมัลติสเปกตรัมที่ไม่มีแถบขอบสีแดง (red edge band).

$$
FCI2 = Red * NIR
$$

พื้นที่ป่าจะมีค่าFCI2 ที่ต่ำกว่า เนื่องจากค่าการสะท้อนแสงของต้นไม้ที่ต่ำลง และการมีเงาภายในชั้นเรือนยอด

_อ้างอิง: Becker, Sarah J., Craig S.T. Daughtry, and Andrew L. Russ. &quot;ดัชนีความปกคลุมของป่าที่เชื่อถือได้สำหรับภาพมัลติสเปกตรัม.&quot; Photogrammetric Engineering &amp; Remote Sensing 84.8 (2018): 505-512._

***

## GEMI - ดัชนีการติดตามสิ่งแวดล้อมระดับโลก

_ใช้ได้เฉพาะใน GUI — ไม่มีให้ใช้เป็นค่าตั้งล่วงหน้า (preset) `--indices` ใน CLI / SDK._

ดัชนีพืชพรรณแบบไม่เชิงเส้นนี้ใช้สำหรับการติดตามสิ่งแวดล้อมระดับโลกจากภาพถ่ายดาวเทียม และพยายามปรับแก้ผลกระทบจากชั้นบรรยากาศ มันคล้ายกับ NDVI แต่มีความไวต่อผลกระทบจากชั้นบรรยากาศน้อยกว่า ดัชนีนี้ได้รับผลกระทบจากดินเปลือย ดังนั้นจึงไม่แนะนำให้ใช้ในพื้นที่ที่มีพืชพรรณน้อยหรือมีความหนาแน่นปานกลาง

$$
GEMI = eta (1 - 0.25 * eta) - {Red - 0.125 \over 1 - Red}
$$

ที่:

$$
eta = {2(NIR^{2}-Red^{2}) + 1.5 * NIR + 0.5 *  Red \over NIR + Red + 0.5}
$$

_อ้างอิง: Pinty, B., และ M. Verstraete. GEMI: ดัชนีแบบไม่เชิงเส้นเพื่อติดตามพืชพรรณทั่วโลกจากดาวเทียม. Vegetation 101 (1992): 15-20._

***

## GARI - Green ดัชนีที่ต้านทานผลกระทบจากชั้นบรรยากาศ

_เฉพาะ GUI — ไม่มีให้ใช้เป็นค่าตั้งล่วงหน้าCLI / SDK `--indices`._

ดัชนีนี้มีความไวต่อช่วงความเข้มข้นของคลอโรฟิลล์ที่กว้างกว่า และมีความไวต่อผลกระทบจากบรรยากาศน้อยกว่าดัชนี NDVI

$$
GARI = {NIR - [Green - \gamma(Blue - Red)] \over NIR + [Green - \gamma(Blue - Red)]   }
$$

ค่าคงที่แกมมา (gamma constant) เป็นฟังก์ชันการถ่วงน้ำหนักที่ขึ้นอยู่กับสภาพของแอโรซอลในบรรยากาศ ENVI ใช้ค่า 1.7 ซึ่งเป็นค่าที่แนะนำโดย Gitelson, Kaufman และ Merzylak (1996, หน้า 296)

_อ้างอิง: Gitelson, A., Y. Kaufman และ M. Merzylak. &quot;การใช้ช่องสัญญาณGreenในการตรวจวัดพืชพรรณทั่วโลกจาก EOS-MODIS&quot; Remote Sensing of Environment 58 (1996): 289-298._

***

## GCI - Green ดัชนีคลอโรฟิลล์

ดัชนีนี้ใช้เพื่อประมาณปริมาณคลอโรฟิลล์ในใบของพืชหลายชนิด

$$
GCI = {NIR \over Green} - 1
$$

การมีช่วงความยาวคลื่นที่กว้างNIR และสีเขียวช่วยให้การคาดการณ์ปริมาณคลอโรฟิลล์แม่นยำยิ่งขึ้น พร้อมทั้งเพิ่มความไวและอัตราส่วนสัญญาณต่อสัญญาณรบกวนที่สูงขึ้น

_อ้างอิง: Gitelson, A., Y. Gritz, and M. Merzlyak. &quot;Relationships Between Leaf Chlorophyll Content and Spectral Reflectance and Algorithms for Non-Destructive Chlorophyll Assessment in Higher Plant Leaves.&quot; Journal of Plant Physiology 160 (2003): 271-282._

***

## ดัชนีใบพืช (GLI) - Green Leaf Index

ดัชนีนี้ถูกออกแบบมาเพื่อใช้กับกล้องดิจิทัลแบบRGBในการวัดความปกคลุมของข้าวสาลี โดยค่าตัวเลขดิจิทัล (DNs) ของสีแดง สีเขียว และสีน้ำเงินมีช่วงตั้งแต่ 0 ถึง 255.

$$
GLI = {(Green - Red) + (Green - Blue)  \over (2 * Green) + Red + Blue }
$$

ค่า GLI มีช่วงตั้งแต่ -1 ถึง +1 ค่าลบแสดงถึงดินและองค์ประกอบที่ไม่ใช่สิ่งมีชีวิต ส่วนค่าบวกแสดงถึงใบและลำต้นสีเขียว

_อ้างอิง: Louhaichi, M., M. Borman, และ D. Johnson. &quot;Spatially Located Platform and Aerial Photography for Documentation of Grazing Impacts on Wheat.&quot; Geocarto International 16, No. 1 (2001): 65-70._

***

## GNDVI - Green ดัชนีความแตกต่างของพืชที่ปรับมาตรฐาน (Normalized Difference Vegetation Index)

ดัชนีนี้คล้ายกับ NDVI แต่ต่างกันที่มันวัดสเปกตรัมสีเขียวในช่วง 540 ถึง 570 นาโนเมตร แทนที่จะวัดสเปกตรัมสีแดง ดัชนีนี้มีความไวต่อความเข้มข้นของคลอโรฟิลล์มากกว่า NDVI

$$
GNDVI = {(NIR - Green) \over (NIR + Green)  }
$$

_อ้างอิง: Gitelson, A., and M. Merzlyak. &quot;Remote Sensing of Chlorophyll Concentration in Higher Plant Leaves.&quot; Advances in Space Research 22 (1998): 689-692._

***

## GOSAVI - ดัชนีพืชพรรณที่ปรับให้เหมาะสมกับดินแบบGreen

ดัชนีนี้ถูกออกแบบขึ้นเดิมเพื่อใช้ร่วมกับภาพถ่ายสี-อินฟราเรดในการคาดการณ์ความต้องการไนโตรเจนของข้าวโพด มันคล้ายกับดัชนีพืชพรรณที่ปรับให้เหมาะสมกับดินแบบOSAVI แต่ใช้แถบสีแดงแทนแถบสีเขียว

$$
GOSAVI = {NIR - Green \over NIR + Green + 0.16)  }
$$

_อ้างอิง: Sripada, R., et al. &quot;Determining In-Season Nitrogen Requirements for Corn Using Aerial Color-Infrared Photography.&quot; วิทยานิพนธ์ระดับปริญญาเอก, มหาวิทยาลัยรัฐนอร์ทแคโรไลนา, 2005._

***

## GRVI - ดัชนีพืชพรรณตามอัตราส่วนGreen

ดัชนีนี้มีความไวต่ออัตราการสังเคราะห์แสงในชั้นเรือนยอดของป่า เนื่องจากค่าการสะท้อนแสงสีเขียวและสีแดงได้รับอิทธิพลอย่างมากจากความเปลี่ยนแปลงของเม็ดสีในใบไม้

$$
GRVI = {NIR \over Green }
$$

_อ้างอิง: Sripada, R., et al. &quot;Aerial Color Infrared Photography for Determining Early In-season Nitrogen Requirements in Corn.&quot; Agronomy Journal 98 (2006): 968-977._

***

## GSAVI - ดัชนีพืชพรรณที่ปรับตามดินแบบGreen

ดัชนีนี้ถูกออกแบบขึ้นเดิมเพื่อใช้ร่วมกับภาพถ่ายสี-อินฟราเรดในการคาดการณ์ความต้องการไนโตรเจนของข้าวโพด มันคล้ายกับดัชนีSAVI แต่ใช้แถบสีเขียวแทนแถบสีแดง

$$
GSAVI = 1.5 * {(NIR - Green) \over (NIR + Green + 0.5)  }
$$

_อ้างอิง: Sripada, R., et al. &quot;Determining In-Season Nitrogen Requirements for Corn Using Aerial Color-Infrared Photography.&quot; วิทยานิพนธ์ระดับปริญญาเอก, มหาวิทยาลัยรัฐนอร์ทแคโรไลนา, 2005._

***

## LAI - ดัชนีพื้นที่ใบ (Leaf Area Index)

ดัชนีนี้ใช้เพื่อประมาณการความปกคลุมของใบไม้ และคาดการณ์การเจริญเติบโตและผลผลิตของพืช ENVI คำนวณดัชนีพื้นที่ใบสีเขียว (LAI) โดยใช้สูตรเชิงประจักษ์ต่อไปนี้จาก Boegh et al (2002):

$$
LAI = 3.618 * EVI - 0.118
$$

โดยที่EVI คือ:

$$
EVI = 2.5 *  {(NIR - Red) \over (NIR + 6 * Red - 7.5 * Blue + 1)}
$$

ค่าLAI ที่สูงมักอยู่ในช่วงประมาณ 0 ถึง 3.5 อย่างไรก็ตาม เมื่อภาพมีเมฆและลักษณะสว่างอื่น ๆ ที่ก่อให้เกิดพิกเซลอิ่มตัว ค่าLAI อาจเกิน 3.5 ได้ ควรทำการปิดบังเมฆและองค์ประกอบที่สว่างจากภาพก่อนที่จะสร้างภาพ LAI

_อ้างอิง: Boegh, E., H. Soegaard, N. Broge, C. Hasager, N. Jensen, K. Schelde, และ A. Thomsen. &quot;ข้อมูลมัลติสเปกตรัมจากอากาศยานสำหรับการวัดดัชนีพื้นที่ใบ (LAI), ความเข้มข้นของไนโตรเจน และประสิทธิภาพการสังเคราะห์แสงในภาคเกษตรกรรม.&quot; Remote Sensing of Environment 81, ฉบับที่ 2-3 (2002): 179-193._

***

## LCI - ดัชนีคลอโรฟิลล์ของใบ

_ใช้ได้เฉพาะใน GUI — ไม่มีให้ใช้ในรูปแบบการตั้งค่าล่วงหน้า (CLI) / SDK `--indices`._

ดัชนีนี้ใช้เพื่อประมาณปริมาณคลอโรฟิลล์ในพืชชั้นสูง ซึ่งไวต่อการเปลี่ยนแปลงของค่าการสะท้อนแสงที่เกิดจากการดูดซับคลอโรฟิลล์

$$
LCI = {NIR2 - RedEdge \over NIR2 + Red}
$$

_อ้างอิง: Datt, B. &quot;Remote Sensing of Water Content in Eucalyptus Leaves.&quot; Journal of Plant Physiology 154, no. 1 (1999): 30-36._

***

## MNLI - ดัชนีไม่เชิงเส้นที่ปรับปรุงแล้ว

ดัชนีนี้เป็นการปรับปรุงจากดัชนีไม่เชิงเส้น (NLI) โดยรวมดัชนีพืชที่ปรับตามดิน (SAVI) เพื่อคำนึงถึงพื้นหลังของดิน ENVI ใช้ค่าปัจจัยปรับพื้นหลังของเรือนยอด (_L_) เท่ากับ 0.5.

$$
MNLI = {(NIR^{2} - Red) * (1 + L) \over (NIR^{2} + Red + L)  }
$$

_อ้างอิง: Yang, Z., P. Willis, และ R. Mueller. &quot;ผลกระทบของภาพ AWIFS ที่ปรับปรุงด้วยอัตราส่วนช่องสัญญาณต่อความแม่นยำในการจำแนกพืชผล.&quot; Proceedings of the Pecora 17 Remote Sensing Symposium (2008), Denver, CO._

***

## MSAVI2 - ดัชนีพืชพรรณที่ปรับตามดินแบบปรับปรุง 2

ดัชนีนี้เป็นเวอร์ชันที่เรียบง่ายกว่าของดัชนีMSAVIที่เสนอโดย Qi และคณะ (1994) ซึ่งปรับปรุงจากดัชนี Soil Adjusted Vegetation Index (SAVI) โดยช่วยลดสัญญาณรบกวนจากดินและเพิ่มช่วงไดนามิกของสัญญาณพืชพันธุ์ MSAVI2 ใช้หลักการแบบอุปนัยที่ไม่ใช้ค่า _L_ คงที่ (เช่นเดียวกับ SAVI) เพื่อเน้นให้เห็นพืชพรรณที่สุขภาพดี

$$
MSAVI2 = {2 * NIR + 1 - \sqrt{(2 * NIR + 1)^{2} - 8(NIR - Red)} \over 2}
$$

_อ้างอิง: Qi, J., A. Chehbouni, A. Huete, Y. Kerr, และ S. Sorooshian. &quot;A Modified Soil Adjusted Vegetation Index.&quot; Remote Sensing of Environment 48 (1994): 119-126._

***

## MSR - Modified Simple Ratio

ดัชนีนี้เป็นการปรับปรุงจากอัตราส่วนแบบง่าย NIR / Red ที่ออกแบบมาเพื่อทำให้ความสัมพันธ์กับพารามิเตอร์ทางชีวฟิสิกส์เป็นเชิงเส้น และมีความไวมากกว่า NDVI เมื่อความหนาแน่นของพืชพรรณสูงขึ้น

$$
MSR = {(NIR / Red) - 1 \over \sqrt{NIR / Red} + 1}
$$

_อ้างอิง: Chen, J. &quot;Evaluation of Vegetation Indices and a Modified Simple Ratio for Boreal Applications.&quot; Canadian Journal of Remote Sensing 22 (1996): 229-242._

***

## NDRE - Normalized Difference RedEdge

ดัชนีนี้คล้ายกับ NDVI แต่เปรียบเทียบความต่างระหว่าง NIR กับ RedEdge แทนที่จะใช้ Red ซึ่งมักตรวจพบความเครียดของพืชได้เร็วกว่า

$$
NDRE = {NIR - RedEdge \over NIR + RedEdge  }
$$

***

## NDVI - ดัชนีความแตกต่างที่ปรับมาตรฐานของพืช (Normalized Difference Vegetation Index)

ดัชนีนี้ใช้เพื่อวัดสภาพพืชที่เขียวและสุขภาพดี การผสมผสานระหว่างสูตรความแตกต่างที่ปรับมาตรฐานและการใช้ช่วงการดูดซับและการสะท้อนแสงสูงสุดของคลอโรฟิลล์ ทำให้ดัชนีนี้มีความเสถียรในสภาพต่าง ๆ ที่หลากหลาย อย่างไรก็ตาม ดัชนีนี้อาจเกิดการอิ่มตัวในสภาพพืชที่หนาแน่นเมื่อค่าLAI สูงขึ้น

$$
NDVI = {NIR - Red \over NIR + Red  }
$$

ค่าของดัชนีนี้อยู่ในช่วง -1 ถึง 1 ช่วงค่าทั่วไปสำหรับพืชสีเขียวคือ 0.2 ถึง 0.8

_อ้างอิง: Rouse, J., R. Haas, J. Schell, and D. Deering. Monitoring Vegetation Systems in the Great Plains with ERTS. Third ERTS Symposium, NASA (1973): 309-317._

***

## NLI - ดัชนีแบบไม่เชิงเส้น

ดัชนีนี้สมมติว่าความสัมพันธ์ระหว่างดัชนีพืชพรรณหลายชนิดกับพารามิเตอร์ชีวฟิสิกส์ของพื้นผิวเป็นแบบไม่เชิงเส้น ดัชนีนี้ทำให้ความสัมพันธ์กับพารามิเตอร์พื้นผิวที่มักเป็นแบบไม่เชิงเส้นกลายเป็นแบบเชิงเส้น

$$
NLI = {NIR^{2} - Red \over NIR^{2} + Red  }
$$

_อ้างอิง: Goel, N., และ W. Qin. &quot;Influences of Canopy Architecture on Relationships Between Various Vegetation Indices and LAI and Fpar: A Computer Simulation.&quot; Remote Sensing Reviews 10 (1994): 309-347._

***

## OSAVI - ดัชนีพืชพรรณที่ปรับตามดินแบบเพิ่มประสิทธิภาพ (Optimized Soil Adjusted Vegetation Index)

ดัชนีนี้อิงจากดัชนีพืชพรรณที่ปรับตามดิน (Soil Adjusted Vegetation Index: SAVI) โดยใช้ค่ามาตรฐาน 0.16 สำหรับปัจจัยปรับพื้นหลังของเรือนยอด Rondeaux (1996) กำหนดว่าค่านี้ให้ความแปรปรวนของดินมากกว่า SAVI สำหรับพื้นที่ที่มีพืชพรรณปกคลุมน้อย ในขณะที่แสดงความไวที่เพิ่มขึ้นต่อพื้นที่ที่มีพืชพรรณปกคลุมมากกว่า 50% ดัชนีนี้เหมาะที่สุดสำหรับการใช้ในพื้นที่ที่มีพืชพรรณค่อนข้างเบาบาง ซึ่งดินสามารถมองเห็นได้ผ่านชั้นใบไม้

$$
OSAVI = {(NIR - Red) \over (NIR + Red + 0.16)  }
$$

_อ้างอิง: Rondeaux, G., M. Steven, และ F. Baret. &quot;Optimization of Soil-Adjusted Vegetation Indices.&quot; Remote Sensing of Environment 55 (1996): 95-107._

***

## RDVI - ดัชนีความแตกต่างของพืชพรรณที่ปรับค่าใหม่ (Renormalized Difference Vegetation Index)

ดัชนีนี้ใช้ความแตกต่างระหว่างความยาวคลื่นใกล้อินฟราเรดและสีแดง ร่วมกับดัชนีความแตกต่างของพืชพรรณ (NDVI) เพื่อเน้นให้เห็นพืชพรรณที่แข็งแรง ดัชนีนี้ไม่ไวต่อผลกระทบของดินและมุมการรับแสงจากดวงอาทิตย์

$$
RDVI = {(NIR- Red) \over \sqrt{(NIR + Red)}  }
$$

_อ้างอิง: Roujean, J., and F. Breon. &quot;Estimating PAR Absorbed by Vegetation from Bidirectional Reflectance Measurements.&quot; Remote Sensing of Environment 51 (1995): 375-384._

***

## SAVI - ดัชนีพืชพรรณที่ปรับให้เหมาะสมกับดิน (Soil Adjusted Vegetation Index)

ดัชนีนี้คล้ายกับ NDVI แต่ช่วยลดผลกระทบจากพิกเซลดิน โดยใช้ปัจจัยปรับพื้นหลังของชั้นใบไม้ (canopy background adjustment factor) คือ _L_ ซึ่งเป็นฟังก์ชันของความหนาแน่นของพืชพรรณ และมักต้องการข้อมูลล่วงหน้าเกี่ยวกับปริมาณพืชพรรณ Huete (1988) แนะนำค่าที่เหมาะสมของ _L_=0.5 เพื่อพิจารณาความแปรปรวนของพื้นหลังดินระดับแรก ดัชนีนี้เหมาะที่สุดสำหรับพื้นที่ที่มีพืชพรรณค่อนข้างเบาบาง ซึ่งดินสามารถมองเห็นได้ผ่านชั้นใบไม้

$$
SAVI = {1.5 * (NIR- Red) \over (NIR + Red + 0.5)  }
$$

_อ้างอิง: Huete, A. &quot;A Soil-Adjusted Vegetation Index (SAVI).&quot; Remote Sensing of Environment 25 (1988): 295-309._

***

## TDVI - ดัชนีความแตกต่างของพืชพรรณที่แปลงแล้ว

ดัชนีนี้มีประโยชน์ในการติดตามความปกคลุมของพืชพรรณในสภาพแวดล้อมเมือง มันไม่เกิดการอิ่มตัวเหมือนดัชนีความแตกต่างของพืชพรรณแบบปรับค่า (NDVI) และดัชนีความแตกต่างของพืชพรรณแบบปรับค่า (SAVI)

$$
TDVI = 1.5 * {(NIR- Red) \over \sqrt{NIR^{2} + Red + 0.5}  }
$$

_อ้างอิง: Bannari, A., H. Asalhi, และ P. Teillet. &quot;Transformed Difference Vegetation Index (TDVI) for Vegetation Cover Mapping&quot; ใน Proceedings of the Geoscience and Remote Sensing Symposium, IGARSS &#x27;02, IEEE International, Volume 5 (2002)._

***

## VARI - ดัชนีที่ทนต่ออิทธิพลของชั้นบรรยากาศในช่วงแสงที่มองเห็นได้

ดัชนีนี้อิงจากดัชนีความแตกต่างของพืชพรรณที่แปลงแล้ว (ARVI) และใช้เพื่อประมาณสัดส่วนของพืชพรรณในภาพที่มีความไวต่ออิทธิพลของชั้นบรรยากาศต่ำ

$$
VARI = {Green - Red \over Green + Red - Blue  }
$$

_อ้างอิง: Gitelson, A., et al. &quot;Vegetation and Soil Lines in Visible Spectral Space: A Concept and Technique for Remote Estimation of Vegetation Fraction. International Journal of Remote Sensing 23 (2002): 2537−2562._

***

## WDRVI - ดัชนีพืชพรรณช่วงไดนามิกกว้าง (Wide Dynamic Range Vegetation Index)

ดัชนีนี้คล้ายกับ NDVI แต่ใช้สัมประสิทธิ์น้ำหนัก (_a_) เพื่อลดความแตกต่างระหว่างการมีส่วนร่วมของสัญญาณใกล้-อินฟราเรดและสัญญาณสีแดงต่อ NDVI ดัชนีพืชพรรณช่วงไดนามิกกว้าง (WDRVI) มีประสิทธิภาพเป็นพิเศษในฉากที่มีความหนาแน่นของพืชพรรณระดับปานกลางถึงสูง เมื่อค่า NDVI เกิน 0.6. NDVI มีแนวโน้มที่จะคงที่เมื่อสัดส่วนพืชพรรณและดัชนีพื้นที่ใบ (LAI) เพิ่มขึ้น ในขณะที่ WDRVI มีความไวต่อช่วงสัดส่วนพืชพรรณที่กว้างขึ้นและต่อการเปลี่ยนแปลงใน LAI

$$
WDRVI = {(\alpha * NIR- Red) \over (\alpha * NIR + Red)}
$$

สัมประสิทธิ์น้ำหนัก (_a_) สามารถอยู่ในช่วงระหว่าง 0.1 ถึง 0.2 ค่า 0.2 ได้รับการแนะนำโดย Henebry, Viña และ Gitelson (2004) แนะนำให้ใช้ค่า 0.2

_อ้างอิง_

_Gitelson, A. &quot;Wide Dynamic Range Vegetation Index for Remote Quantification of Biophysical Characteristics of Vegetation.&quot; Journal of Plant Physiology 161, No. 2 (2004): 165-173._

_Henebry, G., A. Viña, และ A. Gitelson. &quot;ดัชนีพืชพรรณช่วงไดนามิกกว้างและประโยชน์ที่อาจมีในการวิเคราะห์ช่องว่าง.&quot; Gap Analysis Bulletin 12: 50-56._
