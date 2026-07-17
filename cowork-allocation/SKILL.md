---
name: cowork-allocation
description: >-
  Balance people's workload in Cowork — see who is free, who is overloaded, and
  move work to even it out. Use when the user asks "who's free", "is someone
  overloaded", "rebalance the workload", "spread this out", "can we take on more",
  or "who should do this". Load together with `cowork-use`. For a status readout
  use `cowork-status`; for date-shifting and general board upkeep use
  `cowork-triage`.
---

# Cowork — เกลี่ยงาน/คาแพซิตี้ (playbook)

ดูภาระงานรายคน แล้ว **ย้ายงานจากคนล้น → คนว่าง** ให้สมดุล
โหลดคู่กับ **`cowork-use`** · เพดานที่ถือว่าเริ่มตึง ~**70–80%** ของเวลา

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

## สูตร (ดู → วินิจฉัย → เกลี่ย)

**1. ดูภาพภาระงาน**
- `capacity_report` → โหลดรายคน (ใครกี่ %) · รับช่วงเวลาได้ ถามช่วงไหนก็ระบุ
- `who_is_free` → ใครว่างจริงในช่วงที่สนใจ (คิดจาก allocation จริง ไม่ใช่เดา)

**2. วินิจฉัย**
- ใครเกิน ~70–80% = ล้น · ใครยังมีที่ว่าง = รับได้
- ถ้าถามว่า "รับงานใหม่ไหวไหม" → เทียบชั่วโมงงานใหม่กับที่ว่างของทีม

**3. เกลี่ย (ทำอย่างระวัง)**
- `reassign_work` = ย้ายงานเป็นชุดตามกฎ (จากคนล้น → คนว่าง) — เครื่องมือหลักของ skill นี้
- ต้องการแค่ถอดผู้รับ → `unassign_tasks` · เปลี่ยนรายตัว → ผ่าน `cowork-use`
- **ห้ามเดาอีเมล** — ปลายทางต้องเป็นสมาชิกโปรเจกต์
- **สรุปก่อนยิง** — reassign = เปลี่ยนเจ้าของงาน บอกผู้ใช้ว่า "ย้าย N งานจาก A→B" ให้เห็นก่อนกดจริง

## ตัวอย่างสั้น
ผู้ใช้: "May งานล้น เกลี่ยให้หน่อย"
1. `capacity_report` → May 95%, Ken 40%, ว่างพอ
2. `who_is_free` ช่วงเดียวกัน → ยืนยัน Ken รับได้
3. เสนอ: "ย้าย 2 งาน design ของ May (ช่วง 14–18) ไป Ken → May เหลือ ~70%" — โอเคไหม
4. ตกลง → `reassign_work` (May→Ken, กรองเฉพาะ 2 งานนั้น)

> เลื่อนวันแทนการย้ายคน (ยืดเดดไลน์) = งานของ `cowork-triage` · อยากได้แค่ readout = `cowork-status`
