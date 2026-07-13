---
name: cowork
description: >-
  Use when helping someone plan or manage work for the Cowork project app —
  understanding their projects/tasks, drafting or simulating tasks, or proposing
  a project plan. FIRST prefer the Cowork connector (MCP) when its tools are
  available — read and act on live data directly, never tell the user "Cowork
  has no API". Fall back to producing an .xlsx file for import only when no
  connector is available or the user asks for a file. Covers Cowork's
  project/stage/phase/task model, team roles, and the exact import-file contract
  so a file loads cleanly and a human can approve it in the app.
---

# Cowork skill

Cowork คือแอปจัดการงาน/โปรเจกต์ของทีม โครงคือ **Project → Stage (ช่วงงาน) → Task (งาน) → Checklist**
skill นี้ทำให้ AI ของคุณ "เข้าใจระบบ Cowork" — ช่วย **วางแผนงาน จำลอง task และลงมือจัดการงานจริง** ได้ 2 ทาง: ผ่าน **connector (สด)** หรือผ่าน **ไฟล์ export/import**

## ⭐ ก่อนอื่น — เช็คว่ามี connector ไหม (สำคัญสุด อ่านก่อนทุกครั้ง)

**ดูก่อนว่าตอนนี้คุณมีเครื่องมือ (tools) ของ Cowork ให้เรียกอยู่หรือเปล่า** — เช่น `list projects`, `get me`, `create task`, `assign task` (พวกนี้มาจาก **Cowork MCP connector**)

- **ถ้ามี → ใช้ connector เป็นหลักเสมอ** อ่านโปรเจกต์/งาน/ทีม และสร้าง/แก้/มอบหมายงาน **สดผ่าน tool ได้เลย**
  - **ห้าม**บอกผู้ใช้ว่า "Cowork ไม่มี API" หรือให้ไป export ไฟล์ — มีทางสดอยู่แล้ว ให้เรียก tool ตรงๆ
  - งานเขียน (create/update/assign) connector จะให้ผู้ใช้กดอนุมัติทีละครั้งเองอยู่แล้ว — ทำได้เลย ไม่ต้องกลัว
- **ถ้าไม่มี tools พวกนั้น → ใช้ทางไฟล์ export/import ตามคู่มือด้านล่าง**
- **ถ้าผู้ใช้อยากทำเป็นไฟล์เอง** (วางแผนออฟไลน์ / ทำ .xlsx) แม้จะเชื่อม connector อยู่ → ทำตามที่ผู้ใช้เลือกได้

> สรุป: **connector = ทางหลัก (สด) · ไฟล์ export/import = ทางสำรอง หรือเมื่อผู้ใช้เลือก**
> ส่วนที่เหลือของ skill นี้ = รายละเอียดของทางไฟล์ + ความรู้เรื่องโครงสร้าง Cowork (ใช้ได้กับทั้งสองทาง)

## กติกาทอง (อ่านก่อนเสมอ)

1. **คุณเสนอ — คนอนุมัติ** ทุกไฟล์ที่สร้างจะเข้าหน้า preview ในแอป ให้คนตรวจทุกแถวก่อนบันทึก คุณจะไม่ได้เขียนลงฐานข้อมูลตรงๆ
2. **ห้ามแต่ง ID** แถวที่มี ID = แก้งานเดิมนั้น · แถวที่ **ID ว่าง = สร้างงานใหม่** · ห้ามคิด ID ขึ้นเอง
3. **import ไม่เคยลบ** ลบแถวออกจากไฟล์ ≠ ลบงาน มันแค่ถูกปล่อยไว้เฉยๆ
4. **Stage สร้างจากไฟล์ไม่ได้** ต้องมีอยู่ในโปรเจกต์ก่อน — ใช้ชื่อให้ตรง (ดู [reference/tasks-contract.md](reference/tasks-contract.md))

## ใช้ skill นี้เมื่อ

- ผู้ใช้อยากให้ช่วย **วางแผน/จำลองงาน** ของโปรเจกต์ Cowork ("วางแผน pitch 3 สัปดาห์ ทีม 4 คน")
- อยาก **สร้างไฟล์ .xlsx เพื่อ import** งานเข้าโปรเจกต์
- ถามเรื่องโครงสร้าง เฟส บทบาท หรือกติกาของ Cowork

## ขั้นตอนแนะนำ (ทางไฟล์ export/import — ใช้เมื่อ "ไม่มี" connector หรือผู้ใช้เลือกทำเป็นไฟล์)

1. **ขอไฟล์ Export ก่อนถ้ามี** — ในแอปมีปุ่ม Export ได้ .xlsx ที่มี PROJECT_ID, รายชื่อ Stage ที่ใช้ได้, และรายชื่อทีม (อีเมล) การเริ่มจากไฟล์นี้ทำให้ Stage/คน/โปรเจกต์ตรงกันอัตโนมัติ แล้วคุณแค่ "เติม/แก้แถว"
2. **ถ้าไม่มีไฟล์** ก็สร้างใหม่จากศูนย์ได้ แต่ต้องรู้ชื่อ Stage และอีเมลทีมที่มีจริง (ถามผู้ใช้) เพราะชื่อที่ไม่ตรงจะถูกตีกลับในหน้า preview
3. อ่าน **[reference/tasks-contract.md](reference/tasks-contract.md)** ให้ครบ แล้วกรอกให้ตรงสัญญา
4. อ่าน **[reference/system-context.md](reference/system-context.md)** เพื่อวางแผนให้สมเหตุสมผล (เฟส pitch/work, บทบาท, การประเมินเวลา)
5. สร้างไฟล์ด้วย **[scripts/make_import_xlsx.py](scripts/make_import_xlsx.py)** (หรือเขียนโค้ดเองตามโครงในไฟล์สัญญา) แล้วส่งให้ผู้ใช้เอาไป import

## ชีตที่ import ได้มีแค่ "Tasks"

ไฟล์ Export มีหลายชีต (Project / Team / Milestones / Allocation / AI Guide) แต่ **ตอน import แอปอ่านแค่ชีต `Tasks`** ชีตอื่นเป็นข้อมูลอ้างอิงอย่างเดียว แก้ไปก็ไม่มีผล

## ตัวอย่างสั้น — เสนอแผน pitch

ผู้ใช้: "ช่วยร่างงานช่วง pitch โปรเจกต์เว็บ 2 สัปดาห์ให้หน่อย"

คุณสร้างแถวใหม่ (ID ว่างทั้งหมด, Phase = `pitch`, Status = `todo`) เช่น:

| Title | Status | Priority | Phase | Stage | Assignees (email) | Start | Due | Estimate (hrs) | ... | ID (ห้ามแก้) |
|---|---|---|---|---|---|---|---|---|---|---|
| Research คู่แข่ง | todo | med | pitch | Research | may@studio.co | 2026-07-14 | 2026-07-16 | 8 |  |  |
| ร่าง mood/direction | todo | high | pitch | Design | may@studio.co | 2026-07-17 | 2026-07-21 | 12 |  |  |
| ทำสไลด์ pitch | todo | high | pitch | Design |  | 2026-07-22 | 2026-07-25 | 16 |  |  |

แล้วบอกผู้ใช้ว่า "เปิดโปรเจกต์ใน Cowork → ปุ่ม Import → อัปโหลดไฟล์ → ตรวจในหน้า preview แล้วกดยืนยัน"

> **ต้นฉบับของสัญญานี้อยู่ในตัวแอป** (`cowork-app/lib/taskImportContract.mjs`) และ [reference/tasks-contract.md](reference/tasks-contract.md) ถูก generate จากที่นั่น — ห้ามแก้ด้วยมือ ดูวันที่ generate ท้ายหัวไฟล์สัญญา
