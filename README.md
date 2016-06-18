# **This is in PoC/Pre-Alpha stage, not for production**

# Thai Fulltext Plugin for MySQL/MariaDB

## แนะนำ

อ้างถึง http://th.lug.wikia.com/wiki/Tlwg_todo_list, http://th.lug.wikia.com/wiki/Tlwg_todo_list, https://bact.cc/2008/mysql-thai-full-text-parser-plug-in-idea/  มีความต้องการ Full Text Search  Engine บน MySQL/MariaDB แต่ว่าภาษาไทยต้องการการตัดคำและในขณะนั้น (เกือบ 10 ปีที่แล้ว) ระบบการตัดคำภาษาไทยยังไม่มีมาตรฐาน

แต่ตอนนี้เรามีแล้วคือ (libthai)[https://github.com/tlwg/libthai] ของ TLWG จึงเป็นโอกาสอันดีที่จะลองทำ

## ติดตั้งไบนารี่

ดาวน์โหลดได้จาก  https://github.com/pruet/thaift_parser/master/binary/ จะคอมไพล์มาสำหรับ x86_64 บน Ubunt/Debian แต่เดาว่าคงใช้กับ Linux ตัวอื่น ๆ ได้

วิธีการติดตั้งคือ เอาไปใส่ใน PLUGIN_DIR ของ MySQL/MariaDB เสร็จแล้วอาจจะต้องแก้ File Permission/ACL/SELinux/AppArmor เพื่อให้ MySQL/MariaDB สามารถเข้าไปใช้งานได้

ต้องมี libthai/libthai-data ติดตั้งอยู่ในเครื่องถึงจะทำงานได้

## คอมไพล์จาก Source code

ต้องมี Source code ของ MySQL 5.7 และควรจะ clone ลงไปยังdirection MYSQL_SOURCE/plugin/fulltext/ แล้วทำการแก้ไขไฟล์ CMakeLists.txt โดยเพิ่มสามบรรทัดนี้เข้าไปเพื่อให้สามารถคอมไพล์ไปพร้อม ๆ กับ MySQL/MariaDB

```
MYSQL_ADD_PLUGIN(thaift_parser ./thaift_parser/plugin_thaift.cc
                MODULE_ONLY MODULE_OUTPUT_NAME "libpluginthaift")
TARGET_LINK_LIBRARIES(thaift_parser "thai")
```

## ตั้งค่า/ใช้งาน
กรุณาอ่าน http://dev.mysql.com/doc/refman/5.7/en/fulltext-fine-tuning.html และ http://dev.mysql.com/doc/refman/5.7/en/fulltext-fine-tuning.html

## ข้อจำกัดการใช้งาน

* มี Performance Penalty จากการ insert และ select เพราะต้องมีการตัดคำทุกครั้ง
* บน MySQL 5.7 ใช้ได้กับทั้ง InnoDB และ MyISAM แต่ว่ายังไม่ได้ทดสอบหนัก ๆ กับ MyISAM
* บน MariaDB ใช้ได้กับ MyISAM เท่านั้น เพราะ InnoDB5.6 ที่อยู่ใน MariaDB ปัจจุบัน ยังไม่สนับสนุน Custom full-text plugin ต้องรอ InnoDB5.7 ที่(ว่ากันว่า)[https://jira.mariadb.org/browse/MDEV-9330]จะอยู่ใน MariaDB 10.2

## การพัฒนา
* ได้โปรด Pull Request มา ถ้าท่านได้ปรับปรุงโค้ดแล้ว รวมถึง Test-cases ต่าง ๆ ด้วย
* การทำงานบางอย่าง โดยเฉพาะใน Binary search กำลังอยู่ระหว่างการพูดคุยว่าจะให้ออกมาอย่างไร ถ้าสนใจ ลองเข้าไปที่ https://www.facebook.com/veer66/posts/10153549226592595