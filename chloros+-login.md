# Chloros+ เข้าสู่ระบบ

## Chloros และ Chloros (เบราว์เซอร์) เข้าสู่ระบบ

เมนูแถบด้านข้างของผู้ใช้ <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> ช่วยให้คุณสามารถเข้าสู่ระบบบัญชี Chloros+ ของคุณและปลดล็อคคุณสมบัติเพิ่มเติมได้

เมื่อเข้าสู่ระบบรายละเอียดบัญชีของคุณจะแสดง:

<รูป><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>

## CLI เข้าสู่ระบบ

เข้าสู่ระบบด้วยข้อมูลประจำตัว Chloros+ ของคุณเพื่อเปิดใช้งานการประมวลผล CLI บน Linux (ไม่มี GUI) นี่เป็นวิธีเดียวในการเปิดใช้งานใบอนุญาตของคุณ

**ไวยากรณ์:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**ผู้ใช้ SDK**: Python SDK ยังมีวิธี `logout()` แบบเป็นโปรแกรมเพื่อล้างข้อมูลประจำตัวที่แคชไว้ ดู [Python เอกสารประกอบ SDK](api-python-sdk.md#logout) สำหรับรายละเอียด
{% endhint %}

**ตัวอย่าง:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**อักขระพิเศษ**: ใช้เครื่องหมายคำพูดเดี่ยวรอบรหัสผ่านที่มีอักขระ เช่น `$`, `!` หรือช่องว่าง
{% endhint %}

**ผลลัพธ์:**

<รูป><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>

### ที่เก็บข้อมูลรับรอง

ข้อมูลรับรองแคชจะถูกจัดเก็บไว้ในตำแหน่งเฉพาะแพลตฟอร์ม:

| แพลตฟอร์ม | เส้นทางแคชข้อมูลประจำตัว |
| --- | --- |
| **Windows** | `%APPDATA%\Chloros\cache\` |
| **Linux** | `~/.cache/chloros/` |

### แผนหมดอายุ

การหมดอายุของแผนใน GUI จะแสดงเมื่อใบอนุญาตของคุณจะไม่ถูกต้อง สำหรับการสมัครสมาชิกรายเดือนที่เกิดซ้ำ วันหมดอายุคือช่วงสิ้นเดือน สำหรับการสมัครสมาชิกรายปีคือหนึ่งปีหลังจากที่คุณเริ่มสมัครสมาชิก การตรวจสอบใบอนุญาตต้องใช้การเชื่อมต่ออินเทอร์เน็ตทุกเดือนเพื่อยืนยัน โดยมีระยะเวลาผ่อนผัน 30 วัน

### ขีดจำกัดอุปกรณ์

แผน Chloros+ แต่ละแผนเสนออุปกรณ์ที่ลงทะเบียนในจำนวนที่แตกต่างกัน อุปกรณ์แต่ละเครื่องที่คุณเข้าสู่ระบบด้วยบัญชี Chloros+ จะนับรวมในจำนวนอุปกรณ์ที่ลงทะเบียนของคุณ คุณสามารถเปลี่ยนชื่อและลบอุปกรณ์บนหน้าบัญชี MAPIR Cloud ของคุณได้

<table><thead><tr><th width="168.5999755859375" align="right">แผน Chloros+</th><th align="center">ทองแดง</th><th align="center">BRONZE</th><th align="center">SILVER</th><th align="center">GOLD</th></tr></thead><tbody><tr><td align="right">อุปกรณ์ที่รองรับ</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>