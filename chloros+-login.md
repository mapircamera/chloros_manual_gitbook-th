# คลอรอส+ เข้าสู่ระบบ

## คลอรอสและคลอรอส (เบราว์เซอร์) เข้าสู่ระบบ

เมนูแถบด้านข้างของผู้ใช้ <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> ช่วยให้คุณสามารถลงชื่อเข้าใช้บัญชี Chloros+ และปลดล็อกคุณสมบัติเพิ่มเติมได้

เมื่อเข้าสู่ระบบรายละเอียดบัญชีของคุณจะแสดง:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>

## เข้าสู่ระบบ CLI

เข้าสู่ระบบด้วยข้อมูลรับรอง Chloros+ ของคุณเพื่อเปิดใช้งานการประมวลผล CLI

**ไวยากรณ์:**

```bash
chloros-cli login <email> <password>
```

**ตัวอย่าง:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% คำใบ้สไตล์ = "คำเตือน" %}
**อักขระพิเศษ**: ใช้เครื่องหมายคำพูดเดี่ยวรอบรหัสผ่านที่มีอักขระ เช่น `$`, `!` หรือช่องว่าง
{% คำแนะนำสุดท้าย %}

**ผลลัพธ์:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>

### แผนหมดอายุ

การหมดอายุของแผนใน GUI จะแสดงเมื่อใบอนุญาตของคุณจะไม่ถูกต้อง สำหรับการสมัครสมาชิกรายเดือนที่เกิดซ้ำ วันหมดอายุคือช่วงสิ้นเดือน สำหรับการสมัครสมาชิกรายปีคือหนึ่งปีหลังจากที่คุณเริ่มสมัครสมาชิก การตรวจสอบใบอนุญาตต้องใช้การเชื่อมต่ออินเทอร์เน็ตทุกเดือนเพื่อยืนยัน โดยมีระยะเวลาผ่อนผัน 30 วัน

### ขีดจำกัดอุปกรณ์

แผนคลอรอส+ แต่ละแผนเสนออุปกรณ์ที่ลงทะเบียนในจำนวนที่แตกต่างกัน อุปกรณ์แต่ละเครื่องที่คุณเข้าสู่ระบบด้วยบัญชี Chloros+ จะนับรวมในจำนวนอุปกรณ์ที่ลงทะเบียนของคุณ คุณสามารถเปลี่ยนชื่อและลบอุปกรณ์บนหน้าบัญชี MAPIR Cloud ของคุณได้

<table><thead><tr><th width="168.5999755859375" align="right">Chloros+ Plan</th><th align="center">COPPER</th><th align="center">BRONZE</th><th align="center">SILVER</th><th align="center">GOLD</th></tr></thead><tbody><tr><td align="right">Devices Supported</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>
