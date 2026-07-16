# Cowork skills

ตระกูล Claude skill ที่สอน AI ให้ทำงานกับ **Cowork** (แอปจัดการงานของทีม) ได้เก่งขึ้น
โครง Cowork: **Client → Project → Stage (ช่วงงาน) → Task (งาน) → Checklist**

> ทิศทางใหญ่: Cowork เปิดเป็น **API/MCP** ให้แต่ละคนทำงานผ่าน AI ของตัวเอง โดยแพลตฟอร์มเป็น
> "กรรมการถือกติกา" (RLS + สิทธิ์ + ไม่มีลบถาวร + activity log) ให้ทุกคนเห็นภาพเดียวกัน
> skill พวกนี้คือ "สมองเสริม" ที่ทำงาน **คู่กับ** connector — ไม่ใช่ทดแทน

## skill ในตระกูลนี้

แต่ละ skill อยู่ในโฟลเดอร์ของตัวเอง (มี `SKILL.md`) แยกตาม **"งานที่คนอยากได้"** ไม่ใช่ตามช่องทาง

| Skill | ไว้ทำอะไร | ช่องทาง |
|---|---|---|
| [`cowork-use`](cowork-use/SKILL.md) | **ตัวฐาน** — วิธีขับ connector ให้ถูกและปลอดภัย (กติกา, filter ก่อน, bulk tools, สิทธิ์, เมื่อไรควรหยุดถาม) โหลดก่อนงาน connector ทุกครั้ง | สด (MCP) |
| [`cowork-plan`](cowork-plan/SKILL.md) | **วางแผนจากบรีฟ** — เปลี่ยนบรีฟเป็น stage → task → ประเมิน → มอบหมาย | สด / ไฟล์ |
| [`cowork-import`](cowork-import/SKILL.md) | **สายไฟล์** — สร้าง `.xlsx` เพื่อ import (ตอนไม่มี connector หรือขอเป็นไฟล์) | ไฟล์ |

**กำลังจะมา (คลื่น 2):** `cowork-status` (รายงานสถานะ/สุขภาพ) · `cowork-allocation` (เกลี่ยงาน/คาแพซิตี้) · `cowork-triage` (ดูแลบอร์ดเดิม/รับมือความเปลี่ยนแปลง)

## วิธีติดตั้ง (Claude Code)

แต่ละ skill = โฟลเดอร์หนึ่งใต้ `~/.claude/skills/` โคลน repo แล้ววางโฟลเดอร์ skill ที่ต้องการ (หรือ symlink ทั้งชุด):

```bash
git clone <repo-url> ~/cowork-skill
ln -s ~/cowork-skill/cowork-use   ~/.claude/skills/cowork-use
ln -s ~/cowork-skill/cowork-plan  ~/.claude/skills/cowork-plan
ln -s ~/cowork-skill/cowork-import ~/.claude/skills/cowork-import
```

อัปเดตล่าสุดเมื่อไหร่ก็ `git pull` ในโฟลเดอร์ repo

> **ย้ายโครงสร้าง:** เดิม skill ชื่อ `cowork` ตัวเดียว ตอนนี้แตกเป็นตระกูล — ตัวสายไฟล์เดิมกลายเป็น
> `cowork-import` ใครลง `cowork` ไว้ ให้ลบตัวเก่าแล้ว symlink ใหม่ตามด้านบน

## เชื่อม MCP connector

ถ้าใช้ Cowork ผ่าน MCP อยู่แล้ว connector จะส่ง "สมองย่อ" (บริบทระบบ) ให้อัตโนมัติตอนเชื่อม —
skill เหล่านี้คือเวอร์ชัน **ลงลึก** ที่โหลดเฉพาะตอนทำงานนั้นๆ (เหมือน `figma-use` ของ Figma)

## สำคัญ: การ sync (กัน drift)

ความรู้ที่ใช้ร่วมกันมี **ต้นฉบับเดียวในตัวแอป** — skill ไม่พิมพ์ซ้ำเอง:

| เนื้อหา | ต้นฉบับในแอป | generator | ไปโผล่ที่ |
|---|---|---|---|
| โครงโมเดล (Project→Stage→Task) | `lib/modelContext.js` | `node scripts/gen-skill-context.mjs` | ทุกไฟล์ที่มี marker `<!-- MODEL:START/END -->` |
| สัญญาไฟล์ import | `lib/taskImportContract.js` | `node scripts/gen-skill-contract.mjs` | `cowork-import/reference/tasks-contract.md` |

แก้ต้นฉบับในแอป → รัน generator ที่เกี่ยว → commit repo นี้ตาม (ไม่งั้น skill จะสอนของเก่า)
เจ้าของงานนี้ = agent **`integrations-steward`** ในฝั่งแอป
