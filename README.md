# cobik skills

ตระกูล Claude skill ที่สอน AI ให้ทำงานกับ **cobik** (แอปจัดการงานของทีม) ได้เก่งขึ้น
โครง cobik: **Client → Project → Stage (ช่วงงาน) → Task (งาน) → Sub-task (งานย่อย ชั้นเดียว)** — รายละเอียดแต่ละชั้นดูบล็อกโมเดลใน `cobik-use/SKILL.md` (generate จากแอป ห้ามแก้มือ)

> ทิศทางใหญ่: cobik เปิดเป็น **API/MCP** ให้แต่ละคนทำงานผ่าน AI ของตัวเอง โดยแพลตฟอร์มเป็น
> "กรรมการถือกติกา" (RLS + สิทธิ์ + ไม่มีลบถาวร + activity log) ให้ทุกคนเห็นภาพเดียวกัน
> skill พวกนี้คือ "สมองเสริม" ที่ทำงาน **คู่กับ** connector — ไม่ใช่ทดแทน

## skill ในตระกูลนี้

แต่ละ skill อยู่ในโฟลเดอร์ของตัวเอง (มี `SKILL.md`) แยกตาม **"งานที่คนอยากได้"** ไม่ใช่ตามช่องทาง

| Skill | ไว้ทำอะไร | ช่องทาง |
|---|---|---|
| [`cobik-use`](cobik-use/SKILL.md) | **ตัวฐาน** — วิธีขับ connector ให้ถูกและปลอดภัย (กติกา, filter ก่อน, bulk tools, สิทธิ์, เมื่อไรควรหยุดถาม) โหลดก่อนงาน connector ทุกครั้ง | สด (MCP) |
| [`cobik-plan`](cobik-plan/SKILL.md) | **วางแผนจากบรีฟ** — เปลี่ยนบรีฟเป็น stage → task → ประเมิน → มอบหมาย | สด / ไฟล์ |
| [`cobik-import`](cobik-import/SKILL.md) | **สายไฟล์** — สร้าง `.xlsx` เพื่อ import (ตอนไม่มี connector หรือขอเป็นไฟล์) | ไฟล์ |
| [`cobik-status`](cobik-status/SKILL.md) | **รายงานสถานะ/สุขภาพ** — readout "เราเป็นไงบ้าง" (อ่านล้วน) | สด (MCP) |
| [`cobik-allocation`](cobik-allocation/SKILL.md) | **เกลี่ยงาน/คาแพซิตี้** — ใครว่าง/ใครล้น แล้วย้ายงานให้สมดุล | สด (MCP) |
| [`cobik-triage`](cobik-triage/SKILL.md) | **ดูแลบอร์ดเดิม** — เลื่อนวัน/rename/reassign/merge/restore เป็นชุด | สด (MCP) |

## วิธีติดตั้ง (Claude Code)

แต่ละ skill = โฟลเดอร์หนึ่งใต้ `~/.claude/skills/` โคลน repo แล้ววางโฟลเดอร์ skill ที่ต้องการ (หรือ symlink ทั้งชุด):

```bash
git clone <repo-url> ~/cobik-skill
for s in cobik-use cobik-plan cobik-import cobik-status cobik-allocation cobik-triage; do
  ln -s ~/cobik-skill/$s ~/.claude/skills/$s
done
```

อัปเดตล่าสุดเมื่อไหร่ก็ `git pull` ในโฟลเดอร์ repo

> **เปลี่ยนชื่อจาก cowork → cobik (2026-09):** ใครเคยลง `cowork-*` (หรือ `cowork` ตัวเดียวรุ่นแรก) ไว้ ให้ถอดของเก่าแล้วต่อใหม่ครั้งเดียว:
>
> ```bash
> rm -f ~/.claude/skills/cowork ~/.claude/skills/cowork-{use,plan,import,status,allocation,triage}
> mv ~/cowork-skill ~/cobik-skill 2>/dev/null || git clone <repo-url> ~/cobik-skill
> cd ~/cobik-skill && git pull
> for s in cobik-use cobik-plan cobik-import cobik-status cobik-allocation cobik-triage; do
>   ln -sf ~/cobik-skill/$s ~/.claude/skills/$s
> done
> ```

## เชื่อม MCP connector

ถ้าใช้ cobik ผ่าน MCP อยู่แล้ว connector จะส่ง "สมองย่อ" (บริบทระบบ) ให้อัตโนมัติตอนเชื่อม —
skill เหล่านี้คือเวอร์ชัน **ลงลึก** ที่โหลดเฉพาะตอนทำงานนั้นๆ (เหมือน `figma-use` ของ Figma)

## สำคัญ: การ sync (กัน drift)

ความรู้ที่ใช้ร่วมกันมี **ต้นฉบับเดียวในตัวแอป** — skill ไม่พิมพ์ซ้ำเอง:

| เนื้อหา | ต้นฉบับในแอป | generator | ไปโผล่ที่ |
|---|---|---|---|
| โครงโมเดล (Project→Stage→Task) | `lib/modelContext.js` | `node scripts/gen-skill-context.mjs` | ทุกไฟล์ที่มี marker `<!-- MODEL:START/END -->` |
| สัญญาไฟล์ import | `lib/taskImportContract.js` | `node scripts/gen-skill-contract.mjs` | `cobik-import/reference/tasks-contract.md` |

แก้ต้นฉบับในแอป → รัน generator ที่เกี่ยว → commit repo นี้ตาม (ไม่งั้น skill จะสอนของเก่า)
เจ้าของงานนี้ = agent **`integrations-steward`** ในฝั่งแอป
