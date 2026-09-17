# LINE Workflow Automation

ระบบอัตโนมัติสำหรับกลุ่ม LINE ขององค์กร: บันทึกแชทกลุ่ม, สรุปงานประจำวันด้วย AI, แจ้งเตือนรายการยอดขายผิดปกติ, เก็บไฟล์แนบถาวร และมี Admin Dashboard ให้จัดการทุกอย่าง

## ความสามารถหลัก

- **Webhook Recorder** — รับข้อความจากกลุ่ม LINE ผ่าน Messaging API แล้วบันทึกลง PostgreSQL
- **AI Daily Summary / Report** — สรุปบทสนทนาประจำวันและออกรายงานผู้บริหารด้วย Anthropic Claude (ถ้าไม่ใส่ API key จะใช้ตัวสรุปแบบ rule-based แทน)
- **Anomaly Alert** — แจ้งเตือนอัตโนมัติเมื่อพบรายการยอดขายที่ผิดปกติเกินเกณฑ์ที่ตั้งไว้
- **Media Archiver** — ดาวน์โหลดไฟล์แนบจากไลน์กลุ่มเก็บถาวร รองรับทั้งดิสก์ในเครื่องและ Object Storage (Cloudflare R2 / AWS S3 / MinIO)
- **Admin Dashboard** — ล็อกอิน, ดูแดชบอร์ด, จัดการกลุ่ม/สินค้า/สรุปงาน/งาน/สื่อ/ล็อก, ส่งข้อความเข้ากลุ่ม
- **Scheduler ในตัว** — ตั้งเวลาสรุปงานและออกรายงานด้วย cron โดยไม่ต้องพึ่ง Task Scheduler ภายนอก (เมื่อ deploy บน cloud ที่รันค้างตลอด)

## เริ่มใช้งาน

```bash
npm install
copy .env.example .env    # mac/Linux ใช้ cp .env.example .env
npm run db:setup          # สร้างตารางและข้อมูลตัวอย่างใน PostgreSQL
npm run dev
```

เปิด http://localhost:3000/ และ http://localhost:3000/admin สำหรับ Dashboard

ค่าที่ต้องตั้งใน `.env` อย่างน้อย: `DATABASE_URL`, `CHANNEL_ACCESS_TOKEN`, `CHANNEL_SECRET` (จาก LINE Developers Console) — ดูรายละเอียดค่าทั้งหมดใน `.env.example`

## คำสั่งที่มี

| คำสั่ง | ทำอะไร |
| --- | --- |
| `npm run dev` | รันแบบ watch (รีสตาร์ทเองเมื่อแก้ไฟล์) |
| `npm run build` | คอมไพล์ TypeScript ไปที่ `dist/` |
| `npm start` | รันไฟล์ที่ build แล้ว |
| `npm run typecheck` | ตรวจ type อย่างเดียว ไม่สร้างไฟล์ |
| `npm run db:setup` | สร้างตาราง + seed ข้อมูลตัวอย่าง |
| `npm run db:reset` | ล้างและสร้างฐานข้อมูลใหม่ |
| `npm run seed:chat` / `npm run seed:sales` | seed เฉพาะข้อมูลแชท / ยอดขาย |
| `npm run hash-password` | สร้าง hash รหัสผ่านสำหรับ Admin Dashboard |
| `npm run daily-summary` / `npm run daily-report` | รันสรุปงาน / รายงานด้วยมือ (ไม่รอ scheduler) |
| `npm run seed:media` / `npm run media:cleanup` / `npm run media:migrate` | จัดการข้อมูลสื่อ (seed / ลบไฟล์เก่า / ย้ายขึ้น S3) |
| `npm run storage:check` | ตรวจการเชื่อมต่อ Object Storage ก่อน deploy จริง |

## Deploy

มี Render Blueprint (`render.yaml`) พร้อมใช้งาน — ตั้งค่าเริ่มต้นเป็นแผน `free` สำหรับทดสอบ (เปลี่ยนเป็น `starter` เมื่อใช้งานจริง เพราะแผน free จะ sleep เมื่อไม่มี traffic ทำให้ LINE webhook ตอบช้าเกิน 2 วินาที) รายละเอียดเต็มอยู่ที่ `Docs/10-Deployment-Render-Neon.md`

## เอกสารเพิ่มเติม

ดูขั้นตอนอบรมและสถาปัตยกรรมทั้งหมดได้ในโฟลเดอร์ `Docs/` เช่น `03-Workshop-3-Group-Chat-Recorder.md`, `04-Workshop-4-AI-Summary-and-Dashboard.md`, `09-Architecture-and-Repo-Review.md`
