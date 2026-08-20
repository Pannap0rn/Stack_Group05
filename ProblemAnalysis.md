## 3.1 การวิเคราะห์ปัญหา

### 1. Input
* Action ของระบบ ได้แก่ `CALL_RECEIVED`, `TEAM_ASSIGNED`, `VEHICLE_DISPATCHED`, `ARRIVED_AT_SCENE` และ `CASE_CLOSED`
* คำสั่งจากผู้ใช้ ได้แก่ Add Action, Undo, Redo และ Show Status

### 2. Output
* Event Stack ที่เก็บลำดับของ Action
* Redo Stack ที่เก็บ Action ที่ถูก Undo
* Current State ของระบบ ได้แก่ `NEW`, `RECEIVED`, `ASSIGNED`, `DISPATCHED`, `ON_SCENE` และ `CLOSED`
* ข้อความแจ้งผลการทำงานหรือข้อผิดพลาด

### 3. ข้อจำกัด (Constraints)
* Action ต้องถูกเพิ่มตามลำดับของ Emergency Workflow
* ไม่สามารถเพิ่ม Action ใหม่หลังจาก State เป็น `CLOSED`
* Action ที่ผิดลำดับต้องไม่ถูกเพิ่มเข้า Event Stack
* เมื่อเพิ่ม Action ใหม่หลังจาก Undo ต้องล้าง Redo Stack

### 4. Assumptions
* ผู้ใช้เลือก Action จากรายการที่ระบบกำหนด
* Event Stack และ Redo Stack สามารถเก็บข้อมูลได้เพียงพอสำหรับการทำงาน
* State ของระบบเริ่มต้นที่ `NEW`
* Workflow มีลำดับที่แน่นอนและไม่สามารถข้าม State ได้

### 5. กรณีปกติ (Normal Case)
ผู้ใช้เพิ่ม Action ตามลำดับที่ถูกต้อง

`CALL_RECEIVED → TEAM_ASSIGNED → VEHICLE_DISPATCHED → ARRIVED_AT_SCENE → CASE_CLOSED`

ระบบจะเพิ่ม Action เข้า Event Stack และเปลี่ยน State ไปตามลำดับจนถึง `CLOSED`

### 6. กรณีขอบเขต (Boundary Case)
* Undo จน Event Stack ว่าง
* Redo จน Redo Stack ว่าง
* เพิ่ม Action เมื่อ State เป็น `CLOSED`
* Undo หลายครั้งติดต่อกัน
* Redo หลังจาก Undo

### 7. กรณีผิดพลาด (Error Case)
* ผู้ใช้เพิ่ม Action ผิดลำดับ เช่น เพิ่ม `CASE_CLOSED` ขณะที่ State เป็น `RECEIVED`
* ผู้ใช้เลือกเมนูหรือ Action ที่ไม่มีอยู่ในระบบ
* ผู้ใช้กด Undo ขณะที่ Event Stack ว่าง
* ผู้ใช้กด Redo ขณะที่ Redo Stack ว่าง

### 8. Operation ที่เกิดขึ้นบ่อย
* Push Action เข้า Event Stack
* Pop Action จาก Event Stack เมื่อ Undo
* Push Action เข้า Redo Stack เมื่อ Undo
* Pop Action จาก Redo Stack เมื่อ Redo
* ตรวจสอบลำดับของ Action และ State

### 9. Operation ที่ใช้เวลามากที่สุด

Operation ที่ใช้เวลามากที่สุดคือ `rebuildState()` หลังการ Undo เนื่องจากต้องตรวจสอบ Action ใน Event Stack ตั้งแต่ต้นเพื่อคำนวณ `currentState` ใหม่ ทำให้มี Time Complexity เป็น `O(n)` เมื่อมี Action จำนวน `n` รายการ
