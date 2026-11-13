# android-superapp

Claude skill สำหรับทดสอบการ์ด Jira บนเว็บแอปที่เปิดอยู่ในแอป SuperApp บน Android emulator
อัดคลิปแยกตาม AC พร้อมแสดงรอยแตะ ตรวจคลิปทุกไฟล์ก่อนส่ง อัปโหลดขึ้น Drive โดยตั้งชื่อไฟล์ตาม AC
แล้วเทียบชื่อและขนาดไฟล์บน Drive กับไฟล์ในเครื่อง จากนั้นร่างคอมเมนต์ Jira ให้คนโพสต์เอง

กติกาทั้งหมดอยู่ใน [references/android-superapp.md](references/android-superapp.md)

## ต้องติดตั้ง Helix ก่อน

skill นี้ทำงานลำพังไม่ได้ เพราะเรียกใช้ `testing-ticket-workflow` ของ Helix สำหรับ intake,
ขั้นยืนยันแผน, การตัดสิน verdict และขั้น Jira

Helix: `<HELIX_REPO_URL>` (ขอลิงก์จากทีม QA วิธีติดตั้งอยู่ใน README ของ Helix)

## ติดตั้ง

```bash
git clone https://github.com/<you>/android-superapp.git
cd android-superapp
ln -s "$PWD" ~/.claude/skills/android-superapp
```

เปิด Claude Code session ใหม่ แล้วพิมพ์ `/android-superapp` ตามด้วยลิงก์การ์ด

## ต้องมีในเครื่องที่รัน

| ของที่ต้องมี | หมายเหตุ |
|---|---|
| Android Studio + emulator + `adb` | ใช้ image แบบ `google_apis` (debuggable) จะอ่านหน้าเว็บได้ครบ ถ้าเป็นอิมเมจ Play Store กดด้วย OCR และการวัดที่ต้องอ่าน DOM จะเป็น BLOCKED |
| Node.js ≥ 18 | ตัวช่วยใน `tools/superapp-test/` ไม่มี dependency |
| macOS (ไม่บังคับ) | ตัว OCR และตัวทำแถบเฟรมเป็น Swift คอมไพล์ทีละไฟล์ด้วย `swiftc -O tools/superapp-test/swift/ocr.swift -o ocr` และ `swiftc -O tools/superapp-test/swift/frames.swift -o frames` ถ้าไม่มี skill จะบอกว่าตรวจข้อไหนไม่ได้ |
| Chrome ที่ล็อกอิน Google | ใช้ตอนอัปโหลดคลิปขึ้น Drive (อัปทีละไฟล์ ไฟล์ละไม่เกินประมาณ 7 MB เพราะตัวส่งนับขนาดแบบ base64 รวมต่อรอบไม่เกิน 10 MB) |
| APK ของแอป, VPN, บัญชีทดสอบ | ขอจากผู้ดูแลระบบ ไม่อยู่ใน repo นี้ |

## เริ่มใช้

1. เปิด AVD ใน Android Studio → Device Manager แล้วเปิดแอปและล็อกอินเอง (skill ไม่พิมพ์รหัสผ่าน และหยุดทุกครั้งที่เจอหน้า login)
2. พิมพ์ `/android-superapp` ตามด้วยลิงก์การ์ด
3. ตอบช่อง intake ในข้อความเดียว ได้แก่ ชื่อ AVD, บัญชี (ไม่ใส่รหัสผ่าน), VPN, โฟลเดอร์ Drive ปลายทาง และนโยบายไฟล์ชื่อซ้ำ
4. ตรวจแผนที่ skill แสดง แล้วยืนยันก่อนเริ่มอัด skill จะไม่เริ่มจนกว่าคุณยืนยัน

**ข้อจำกัดที่ควรรู้:** emulator หนึ่งเครื่องให้ session เดียวขับต่อครั้ง skill ไม่เปิดบั๊กเอง
ไม่บันทึกข้อมูลเข้าระบบ เว้นแต่ได้รับอนุญาตตอนยืนยันแผน และผลที่ได้จากแถบเฟรมจะระบุว่ามาจากแถบเฟรม ไม่ใช่การวัด

## ห้าม commit ข้อมูลจริง

repo นี้เป็น public ห้ามใส่ package name, host, ID โฟลเดอร์ Drive, อีเมลบัญชี หรือ token
ให้ใช้ placeholder (`{APP_PKG}` `{WEB_HOST}` `{DESTINATION}` `{TEST_ACCOUNT}`) แล้วเก็บค่าจริงไว้นอก repo

## ทดสอบตัวช่วย

```bash
npm test
```
