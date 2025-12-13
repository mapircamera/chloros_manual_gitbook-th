---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# เป้าหมายการสอบเทียบ

MAPIR นำเสนอเป้าหมายการสอบเทียบที่หลากหลายเพื่อให้ครอบคลุมการใช้งานที่หลากหลาย T4-R50 ขนาดกะทัดรัดที่แสดงด้านล่างประกอบด้วยแผง 4 แผงที่ตรวจวัดการสะท้อนแสงตั้งแต่ 250 - 2,500 นาโนเมตร

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>

เป้าหมายอ้างอิงแบบกระจาย T4 มีเส้นโค้งการสะท้อนแสงต่อไปนี้ [ดาวน์โหลดข้อมูลที่นี่](XPROT000046X):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 การสะท้อนแสง :: 250-2500nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 การสะท้อนแสง :: 400-1000nm</p></figcaption></figure>

เมื่อดูกราฟการสะท้อนแสง คุณจะเห็นว่าค่าคือความยาวคลื่น (แกน x) เทียบกับเปอร์เซ็นต์การสะท้อนแสง (แกน y) เมื่อเราจับภาพของเป้าหมายการปรับเทียบ เราจะสร้างความสัมพันธ์ระหว่างค่าพิกเซลและเปอร์เซ็นต์การสะท้อนแสง ภายในสเปกตรัมที่แถบเซ็นเซอร์ของกล้องแต่ละตัวไวต่อ

ซึ่งหมายความว่าในทุกภาพที่คุณถ่ายด้วยกล้องของเรา คุณสามารถใช้ภาพถ่ายของเป้าหมายการสะท้อนแสงของเรา เช่น [T4-R50](XPROT000047X) หรือ [T4-R125](XPROT000048X) เพื่อปรับเทียบภาพสำหรับการสะท้อนแสง เมื่อปรับเทียบแต่ละพิกเซลในภาพแล้วจะเท่ากับเปอร์เซ็นต์การสะท้อนแสง

หากคุณส่งออกภาพที่ปรับเทียบแล้วในรูปแบบ Chloros เป็น JPG หรือ TIFF ทั่วไป เปอร์เซ็นต์การสะท้อนแสงจะถูกคำนวณโดยการหารค่าพิกเซลด้วยความลึกบิตของรูปแบบภาพ ดังนั้นสำหรับ JPG หารด้วย 255 และ TIFF หารด้วย 65,535 คุณยังสามารถเลือกเอาต์พุตรูปแบบ PERCENT ในคลอรอสได้ จากนั้นแต่ละพิกเซลจะมีช่วงตั้งแต่ค่าเปอร์เซ็นต์ 0.0 ถึง 1.0 (การสะท้อนแสง 0% ถึง 100%) โปรดทราบว่าแอปพลิเคชันรูปภาพบางแอปพลิเคชันไม่สามารถยอมรับรูปภาพเปอร์เซ็นต์ (จุดลอยตัว) ได้ และแอปพลิเคชันเหล่านี้มีพื้นที่จัดเก็บขนาดใหญ่

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>