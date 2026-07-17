---
name: cowork-use
description: >-
  Foundational guide for operating the Cowork project-management connector (MCP)
  — how to read and act on the team's live projects, stages, and tasks
  correctly. Load this FIRST before any Cowork connector work. Use when the user
  asks to view, plan, create, update, assign, reassign, or analyze work in
  Cowork AND the Cowork tools (list_projects, create_task, assign_task, …) are
  available. For end-to-end planning from a brief, also load `cowork-plan`. When
  NO connector is available (build an .xlsx import file instead), use
  `cowork-import`.
---

# Cowork — ขับ connector สด (ตัวฐาน)

Cowork คือแอปจัดการงานของทีม skill นี้สอน AI ให้ **"ขับ tool สดของ Cowork ให้เก่งและปลอดภัย"** — เป็นรากที่ skill งานอื่น (`cowork-plan` และอื่นๆ) ห้อยอยู่

<!-- MODEL:START · generate จาก cowork-app/lib/modelContext.js (รัน node scripts/gen-skill-context.mjs) — ห้ามแก้มือระหว่าง marker -->
```
Client (ลูกค้า) → Project → Stage (ช่วงงาน) → Task (งาน) → Checklist (ติ๊กย่อยในงาน)
```

- **Client** = ลูกค้า จัดกลุ่มโปรเจกต์ (ลูกค้า 1 ราย มีได้หลายโปรเจกต์ · โปรเจกต์สังกัดลูกค้าได้ 1 รายหรือไม่มีก็ได้) — ใช้ดูงบรวม/อัตราชนะต่อลูกค้า · การ import ทาง .xlsx ยังไม่แตะ client (ตั้งลูกค้าในแอป)
- **Project** = โปรเจกต์หนึ่งงานของทีม มีเจ้าของ (owner), งบ, ช่วงวันของ pitch และของ work
- **Stage** = "ช่วงงาน" ที่ใช้จัดกลุ่ม task เช่น Research / Design / Production · แต่ละ stage สังกัด **เฟลเดียว** (pitch หรือ work)
- **Task** = งานย่อย มีสถานะ/ความสำคัญ/ผู้รับผิดชอบ/ช่วงเวลา/ชั่วโมง
- **Checklist** = รายการติ๊กย่อยในแต่ละ task (import ทาง .xlsx ยังไม่แตะ checklist)
<!-- MODEL:END -->

## ⭐ ก่อนอื่น — มี connector ไหม
ดูว่ามี tool ของ Cowork ให้เรียกอยู่ไหม (`list_projects`, `get_me`, `create_task`, `assign_task` …)
- **มี → นี่คือทางหลัก** อ่าน/สร้าง/แก้/มอบหมายงานสดผ่าน tool ได้เลย · **ห้าม**บอกผู้ใช้ว่า "Cowork ไม่มี API" หรือให้ไป export ไฟล์
- **ไม่มี → หยุดใช้ skill นี้** ไปใช้ `cowork-import` (สาย .xlsx) แทน

## ขอบเขต skill นี้ (Skill Boundaries)
- **ตัวนี้ทำ:** กติกาการขับ connector ให้ถูก — อ่านข้อมูล, เขียนงานเดี่ยว/เป็นชุด, มอบหมาย, อ่านเชิงวิเคราะห์
- **ไปหา `cowork-plan`:** ตั้งโปรเจกต์/เฟสใหม่จากบรีฟ end-to-end (สร้าง stage → เรียงงาน → ประเมิน → มอบหมาย)
- **ไปหา `cowork-import`:** ทำไฟล์ .xlsx (ตอนไม่มี connector หรือผู้ใช้ขอไฟล์)
- **ไปหา `cowork-status`:** รายงานสถานะ/สุขภาพ (readout อ่านล้วน — "เราเป็นไงบ้าง")
- **ไปหา `cowork-allocation`:** เกลี่ยงาน/คาแพซิตี้/ใครว่าง/คนล้น
- **ไปหา `cowork-triage`:** ดูแลบอร์ดเดิม (เลื่อนวัน/rename/reassign/merge/restore)

## เริ่มยังไง
1. เรียก **`list_projects`** ก่อนเสมอ (ไม่รับ input) → ได้ทุกโปรเจกต์ที่ผู้ใช้เห็น + id + สถิติงาน · tool อื่นๆ ต้องใช้ `project_id` จากตรงนี้
2. ยังไม่มีโปรเจกต์เลย? `get_me` ยืนยันว่าเชื่อมต่อติด

## กติกาทอง (อ่านก่อนลงมือ)
1. **connector = แหล่งความจริง** ห้ามไปดึงข้อมูล Cowork จากไฟล์ในเครื่อง/export/connector อื่น
2. **ห้ามแต่งอีเมล** ผู้รับผิดชอบระบุด้วยอีเมลที่เป็นสมาชิกโปรเจกต์เท่านั้น — ไม่รู้ก็เว้นว่างหรือถาม อย่าเดา
3. **งานใหม่เริ่มที่ `todo`** · ประเมินชั่วโมงให้สมจริง · ช่อง "ชั่วโมงจริง" ปล่อยว่างจนกว่างานจะเสร็จ
4. **ชื่อ=EN · ประโยค=TH** ตอนตั้งชื่องาน ใช้ภาษาลูกผสมของทีม (ชื่องาน/สเตจเป็นอังกฤษ คำอธิบายเป็นไทย)

## ทำงานให้ประหยัด (สำคัญ — จุดที่ AI มักทำพลาด)
- **กรอง/ค้นก่อน แล้วค่อยดึง** — รายการงานรับ filter ได้ (status, phase, stage, ข้อความในชื่อ, อีเมลผู้รับ) · เรียก list ครั้งเดียวแล้วจัดการทั้งชุด อย่าดึงทุกอย่างมานับมือ
- **แก้หลายอันใช้ tool เป็นชุด อย่าวนเรียกทีละอัน** — update หลายอันทีเดียว, rename ด้วย find→replace, เลื่อนวันตามกฎ, assign/reassign เป็นชุด, reorder/merge/apply-template สำหรับ stage · ทั้งหมดจบใน step เดียว + รายงานผลรายตัว
- **คำถาม "เราเป็นไงบ้าง" ใช้ read เชิงวิเคราะห์** ไม่ใช่ดึง list ดิบมานับ:
  - `project_deep_dive` — ทุกอย่างของ **1 โปรเจกต์** ในครั้งเดียว (สุขภาพ, stage, งานที่ต้องสน, workload รายคน, ส่งตรงเวลา, งบ, activity)
  - `portfolio_pulse` — **ทั้ง portfolio** ครั้งเดียว (progress/overdue/unassigned ทุกโปรเจกต์, workload ทีม, ใครล้น, เทรนด์ส่งงาน/อัตราชนะ)
  - `estimate_accuracy` — วางแผน vs จริง บนงานที่เสร็จแล้ว รายคน/รายโปรเจกต์
  - หยิบพวกนี้ก่อนที่จะยิง project_health + list_tasks + capacity_report ทีละตัว

## สิทธิ์และแนวกันชน (Guardrails)
- เห็นและทำได้เฉพาะโปรเจกต์ที่ผู้ใช้เป็นสมาชิก (บังคับด้วย row-level security)
- งานระดับ task (สร้าง/แก้/มอบหมาย) = สมาชิกทุกคนทำได้ · แก้ตั้งค่าโปรเจกต์/ทีม = PM/Lead/admin เท่านั้น
- **ลบ task/stage = เข้าถังกู้คืนได้** (`list_trash` ดู, `restore_task`/`restore_stage` เอากลับพร้อม id เดิม) — ไม่มีอะไรหายถาวร
- ทุกการเปลี่ยนถูกบันทึกใน activity log · เราทำงานภายในสิทธิ์ของผู้ใช้เสมอ → **เสนอและลงมือได้เต็มที่ในกรอบนี้ แอปเองเป็นกรรมการ**

## เพดาน workload
เลี่ยงกองงานทับซ้อนบนคนเดียว — แอปวัด workload รายคน · เกิน ~70–80% ของเวลาถือว่าเริ่มตึง ควรเตือน/เกลี่ย (`who_is_free`, `capacity_report` ช่วยดู)

## เมื่อไรควร "หยุดถาม" แทนเดา
- ผู้รับผิดชอบไม่ชัด/อีเมลไม่ตรงสมาชิก
- ขอบเขตงานกำกวม (จำนวนงาน, เฟส, ช่วงเวลา ที่คาดเดาไม่ได้)
- การกระทำเป็นชุดที่ดูรุนแรง (เลื่อนวันทั้งโปรเจกต์, reassign ยกทีม) — สรุปให้ดูก่อนกดจริง
