ข้อมูลเริ่มต้น (Initial State)
Top
CASE_CLOSED
ARRIVED_AT_SCENE
VEHICLE_DISPATCHED
TEAM_ASSIGNED
CALL_RECEIVED
Bottom
กรณีที่ 1: กรณีปกติ (Normal Case) — Algorithm A
เป้าหมาย: Undo CASE_CLOSED เพื่อย้อนกลับไปยัง ON_SCENE
เริ่มต้น:
eventStack =
Top
CASE_CLOSED
ARRIVED_AT_SCENE
VEHICLE_DISPATCHED
TEAM_ASSIGNED
CALL_RECEIVED
Bottom

redoStack = [ว่าง]

Step 1: Pop CASE_CLOSED ออกจาก Event Stack และ Push เข้า Redo Stack
eventStack =
Top
ARRIVED_AT_SCENE
VEHICLE_DISPATCHED
TEAM_ASSIGNED
CALL_RECEIVED
Bottom

Step 2: ตรวจสอบ State ใหม่จาก Event Stack
currentState = ON_SCENE

Step 3: หาก Redo ให้ Pop CASE_CLOSED จาก Redo Stack และ Push กลับ Event Stack
eventStack =
Top
CASE_CLOSED
ARRIVED_AT_SCENE
VEHICLE_DISPATCHED
TEAM_ASSIGNED
CALL_RECEIVED
Bottom

สถานะสุดท้าย: currentState = CLOSED

กรณีที่ 2: กรณีขอบเขต — Algorithm B
เป้าหมาย: เพิ่ม CASE_CLOSED ในขณะที่ currentState = RECEIVED
เริ่มต้น:
eventStack =
Top
TEAM_ASSIGNED
CALL_RECEIVED
Bottom


currentState = RECEIVED

Step 1: รับ Action CASE_CLOSED

Step 2: ตรวจสอบ Transition

RECEIVED + CASE_CLOSED
-> ไม่มี Transition

Step 3: ปฏิเสธ Action

ERROR: Invalid Action

สถานะสุดท้าย:
eventStack =
Top
TEAM_ASSIGNED
CALL_RECEIVED
Bottom

currentState = RECEIVED
