# Cowork skill

Claude skill ที่สอน AI ให้ "เข้าใจระบบ Cowork" — ช่วยวางแผนงาน จำลอง task
และสร้างไฟล์ `.xlsx` เพื่อ import งานกลับเข้าโปรเจกต์ได้ โดยผ่านหน้า preview
ให้คนอนุมัติเสมอ (AI เสนอ — คนตัดสิน)

> นี่คือก้าวแรก (เฟส 0.5) ของทิศทางที่ใหญ่กว่า: ปลายทางคือ Cowork เปิดเป็น **API/MCP**
> ให้แต่ละคนทำงานผ่าน AI ของตัวเอง โดยแพลตฟอร์มเป็น "กรรมการถือกติกา" ให้ทุกคนเห็นภาพเดียวกัน

## มีอะไรในนี้

| ไฟล์ | คืออะไร |
|---|---|
| [SKILL.md](SKILL.md) | ตัว skill — AI อ่านไฟล์นี้ก่อน (มี frontmatter name/description) |
| [reference/tasks-contract.md](reference/tasks-contract.md) | สัญญาไฟล์ import ครบทุกคอลัมน์+กติกา (**generate จากตัวแอป — ห้ามแก้มือ**) |
| [reference/system-context.md](reference/system-context.md) | บริบทระบบ: เฟส pitch/work, บทบาท, Stage, การประเมินเวลา |
| [scripts/make_import_xlsx.py](scripts/make_import_xlsx.py) | สคริปต์สร้าง `.xlsx` ที่ import ได้ (ต้องมี `openpyxl`) |

## วิธีติดตั้ง (Claude Code)

โคลน repo นี้แล้ววางในโฟลเดอร์ skills ของ Claude Code — เช่น
`~/.claude/skills/cowork/` (หรือ `.claude/skills/cowork/` ในโปรเจกต์)
แล้ว Claude จะเรียกใช้เองเมื่อคุณพูดถึงงาน Cowork

```bash
git clone <repo-url> ~/.claude/skills/cowork
```

อัปเดตเป็นเวอร์ชันล่าสุดเมื่อไหร่ก็ `git pull` ในโฟลเดอร์นั้น

## วิธีใช้ (คร่าวๆ)

1. บอก AI ว่าอยากวางแผน/เพิ่มงานในโปรเจกต์ Cowork ไหน
2. ถ้ามีไฟล์ Export ของโปรเจกต์นั้น ให้แนบไปด้วย (AI จะได้รู้ Stage/ทีม/PROJECT_ID ที่ถูกต้อง)
3. AI เสนอแผน → สร้างไฟล์ `.xlsx`
4. เปิดโปรเจกต์ใน Cowork → ปุ่ม **Import** → อัปโหลด → ตรวจในหน้า **preview** → กดยืนยัน

## สำคัญ: การ sync

ต้นฉบับความจริงของ "สัญญาไฟล์" อยู่ที่เดียว **ในตัวแอป**
(`cowork-app/lib/taskImportContract.mjs`) — ทั้งชีต AI Guide ตอน export และ
[reference/tasks-contract.md](reference/tasks-contract.md) ในนี้ อ่านจากไฟล์นั้น
ทุกครั้งที่แก้สัญญาในแอป ให้รัน `node scripts/gen-skill-contract.mjs` ในโปรเจกต์แอป
เพื่อ regenerate ไฟล์สัญญาใน repo นี้แล้ว commit ตาม (ไม่งั้น AI จะสร้างไฟล์ตามกติกาเก่า)
ดูวันที่ generate ล่าสุดที่หัวไฟล์ [reference/tasks-contract.md](reference/tasks-contract.md)
