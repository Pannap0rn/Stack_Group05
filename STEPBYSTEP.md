## 3.4 การแสดงตัวอย่างแบบ Step-by-step

### ข้อมูลเริ่มต้น (Initial State)

กำหนดให้ระบบ Emergency Workflow มีลำดับ Action ที่ถูกต้องดังนี้

CALL_RECEIVED -> TEAM_ASSIGNED -> VEHICLE_DISPATCHED -> ARRIVED_AT_SCENE -> CASE_CLOSED

หลังจากเพิ่ม Action ตามลำดับ จะได้สถานะ Event Stack จากบนลงล่าง (Top to Bottom) ดังนี้

```text
Event Stack
Top
CASE_CLOSED
ARRIVED_AT_SCENE
VEHICLE_DISPATCHED
TEAM_ASSIGNED
CALL_RECEIVED
Bottom
```

และเริ่มต้น

```text
Redo Stack = [ว่าง]
```

---

### กรณีที่ 1: กรณีปกติ (Normal Case) — Algorithm A (Event Stack / Iteration)

**เป้าหมาย:** ต้องการตรวจสอบ Action `TEAM_ASSIGNED` ว่าสามารถเพิ่มเข้าสู่ Event Stack ได้ถูกต้องตามลำดับหรือไม่

เริ่มต้น:

```text
eventStack =
Top
CASE_CLOSED
ARRIVED_AT_SCENE
VEHICLE_DISPATCHED
TEAM_ASSIGNED
CALL_RECEIVED
Bottom

redoStack = [ว่าง]
```

**Step 1:** ตรวจสอบ Action ล่าสุดใน Event Stack โดยดูจากลำดับของข้อมูลใน Stack

สถานะ:

```text
eventStack =
Top
CASE_CLOSED
ARRIVED_AT_SCENE
VEHICLE_DISPATCHED
TEAM_ASSIGNED
CALL_RECEIVED
Bottom

redoStack = [ว่าง]
```

ระบบพบว่า Action `TEAM_ASSIGNED` อยู่ในตำแหน่งที่ถูกต้องของ Workflow หลัง `CALL_RECEIVED`

**Step 2:** ตรวจสอบ Action ก่อนหน้าโดยเลื่อนผ่านข้อมูลใน Event Stack

```text
CASE_CLOSED
↓
ARRIVED_AT_SCENE
↓
VEHICLE_DISPATCHED
↓
TEAM_ASSIGNED
```

เมื่อพบ `TEAM_ASSIGNED` ตรงกับเป้าหมาย จึงหยุดการตรวจสอบ

**Step 3:** ยืนยันว่า Action `TEAM_ASSIGNED` อยู่ในลำดับที่ถูกต้อง

ลำดับ Workflow:

```text
CALL_RECEIVED
↓
TEAM_ASSIGNED
↓
VEHICLE_DISPATCHED
↓
ARRIVED_AT_SCENE
↓
CASE_CLOSED
```

ดังนั้นผลลัพธ์คือ

```text
Action TEAM_ASSIGNED = ถูกต้อง
ผลลัพธ์ = SUCCESS
```

สถานะสุดท้าย:

```text
eventStack =
Top
CASE_CLOSED
ARRIVED_AT_SCENE
VEHICLE_DISPATCHED
TEAM_ASSIGNED
CALL_RECEIVED
Bottom

redoStack = [ว่าง]
```

**สรุป:** Algorithm A สามารถตรวจสอบลำดับของ Action จากข้อมูลใน Event Stack ได้สำเร็จ โดยไม่พบ Action ที่ผิดลำดับ

---

### กรณีที่ 2: กรณีขอบเขต/ผิดลำดับ (Worst Case) — Algorithm B (Event Stack + State Machine)

**เป้าหมาย:** ต้องการเพิ่ม Action `CASE_CLOSED` แต่ปัจจุบัน `currentState = RECEIVED`

สถานะเริ่มต้น:

```text
currentState = RECEIVED

eventStack =
Top
TEAM_ASSIGNED
CALL_RECEIVED
Bottom

redoStack = [ว่าง]
```

ผู้ใช้เลือก Action:

```text
CASE_CLOSED
```

**Step 1:** ระบบรับ Action `CASE_CLOSED` และตรวจสอบ `currentState`

```text
Current State = RECEIVED
Action = CASE_CLOSED
```

**Step 2:** ระบบตรวจสอบ Transition Table

```text
Current State        Action              Next State
----------------------------------------------------
RECEIVED              TEAM_ASSIGNED       ASSIGNED
```

จาก Transition Table ไม่มี Transition:

```text
RECEIVED + CASE_CLOSED
```

ดังนั้น

```text
getNextState(RECEIVED, CASE_CLOSED)
= NULL
```

**Step 3:** ระบบปฏิเสธ Action เนื่องจากผิดลำดับ

```text
ERROR: Invalid Action
```

Action `CASE_CLOSED` จะไม่ถูก Push เข้า Event Stack

สถานะ:

```text
eventStack =
Top
TEAM_ASSIGNED
CALL_RECEIVED
Bottom
```

```text
redoStack = [ว่าง]
```

```text
currentState = RECEIVED
```

**Step 4:** ระบบยังคงรักษา State เดิม เพราะ Action ที่ผิดลำดับไม่สามารถเปลี่ยน State ได้

```text
RECEIVED
   ↓
TEAM_ASSIGNED
   ↓
ASSIGNED
```

ดังนั้นต้องเพิ่ม `TEAM_ASSIGNED` ตามลำดับก่อนจึงจะสามารถไปยัง State ถัดไปได้

**สรุป:** Algorithm B สามารถตรวจพบ Action ที่ผิดลำดับโดยใช้ `currentState` และ Transition Table ทำให้ไม่เกิดการเปลี่ยน State หรือเพิ่ม Action ที่ไม่ถูกต้องเข้าสู่ Event Stack

---

### การทดสอบ Undo และ Redo

กำหนดให้ Workflow ดำเนินมาถึง

```text
eventStack =
Top
ARRIVED_AT_SCENE
VEHICLE_DISPATCHED
TEAM_ASSIGNED
CALL_RECEIVED
Bottom

redoStack = [ว่าง]

currentState = ON_SCENE
```

**Step 1: Undo**

Pop `ARRIVED_AT_SCENE` ออกจาก Event Stack และ Push เข้า Redo Stack

สถานะ:

```text
eventStack =
Top
VEHICLE_DISPATCHED
TEAM_ASSIGNED
CALL_RECEIVED
Bottom
```

```text
redoStack =
Top
ARRIVED_AT_SCENE
Bottom
```

```text
currentState = DISPATCHED
```

**Step 2: Undo อีกครั้ง**

Pop `VEHICLE_DISPATCHED` จาก Event Stack และ Push เข้า Redo Stack

สถานะ:

```text
eventStack =
Top
TEAM_ASSIGNED
CALL_RECEIVED
Bottom
```

```text
redoStack =
Top
VEHICLE_DISPATCHED
ARRIVED_AT_SCENE
Bottom
```

```text
currentState = ASSIGNED
```

**Step 3: Redo**

Pop `VEHICLE_DISPATCHED` จาก Redo Stack แล้ว Push กลับเข้า Event Stack

สถานะ:

```text
eventStack =
Top
VEHICLE_DISPATCHED
TEAM_ASSIGNED
CALL_RECEIVED
Bottom
```

```text
redoStack =
Top
ARRIVED_AT_SCENE
Bottom
```

```text
currentState = DISPATCHED
```

**Step 4: เพิ่ม Action ใหม่หลัง Undo/Redo**

หากเพิ่ม `ARRIVED_AT_SCENE` เป็น Action ใหม่ ระบบจะ Push Action เข้า Event Stack และล้าง Redo Stack

สถานะ:

```text
eventStack =
Top
ARRIVED_AT_SCENE
VEHICLE_DISPATCHED
TEAM_ASSIGNED
CALL_RECEIVED
Bottom
```

```text
redoStack =
[ว่าง]
```

```text
currentState = ON_SCENE
```

**สรุป:** เมื่อเพิ่ม Action ใหม่ ระบบจะล้าง Redo Stack เพื่อป้องกันการ Redo Action เก่าที่ไม่สอดคล้องกับ Workflow ปัจจุบัน
