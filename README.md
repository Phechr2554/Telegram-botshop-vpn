# Bio-shop Telegram Bot

บอท Telegram สำหรับขายและแจกทดลองใช้งาน VPN ผ่าน 3x-ui Panel  
พร้อมระบบเครดิต, ซองอั่งเปา TrueMoney, โค้ดเติมเครดิต, โค้ดรีเซ็ตสิทธิ์ทดลอง และ log ธุรกรรม

**อัปเดตล่าสุด:** 8 มิถุนายน 2026  
**รองรับ:** 3x-ui v3.2.8

---

## โครงสร้างไฟล์

```text
bot_v328_output/
├── main.py
├── database.py
├── xui_api.py
├── requirements.txt
├── railway.toml.txt  ← เปลี่ยนชื่อเป็น railway.toml ก่อน deploy
└── README.md
```

---

## ตั้งค่า Environment Variables

| ตัวแปร | จำเป็น | คำอธิบาย |
|---|:---:|---|
| `BOT_TOKEN` | ✅ | Token จาก `@BotFather` |
| `ADMIN_IDS` | ✅ | Telegram user ID ของแอดมิน คั่นด้วย comma เช่น `111,222` |
| `XUI_URL` | ✅ | URL panel รวม webBasePath ถ้ามี เช่น `https://host:2053/secretpath` ไม่ต้องต่อ `/panel` |
| `XUI_API_TOKEN` | ⭐ แนะนำ | API Token จาก 3x-ui — ใช้แทน login ปกติ ลดปัญหา fail2ban |
| `XUI_USERNAME` | fallback | username ของ panel เมื่อไม่ได้ใช้ `XUI_API_TOKEN` |
| `XUI_PASSWORD` | fallback | password ของ panel เมื่อไม่ได้ใช้ `XUI_API_TOKEN` |
| `AIS_INBOUND_ID` | ✅ | Inbound ID สำหรับปุ่ม AIS |
| `TRUE_INBOUND_ID` | ✅ | Inbound ID สำหรับปุ่ม TRUE |
| `DB_PATH` | แนะนำ | Path SQLite เช่น `/data/bot.db` บน Railway |
| `TRUEMONEY_WALLET_PHONE` | ❌ | เบอร์ TrueMoney เริ่มต้น (ตั้งภายหลังผ่าน `/Settingsmycredit` ได้) |

> ถ้าตั้ง `XUI_API_TOKEN` แล้ว ไม่จำเป็นต้องใส่ `XUI_USERNAME` / `XUI_PASSWORD`

---

## การสร้าง API Token ใน 3x-ui

1. เข้า 3x-ui Panel
2. ไปที่ **Settings → Security → API Tokens**
3. กด **Create** แล้วเปิดใช้งาน token
4. Copy token ไปใส่ใน env `XUI_API_TOKEN`

หรือรันบน VPS:

```bash
x-ui setting -getApiToken
```

---

## วิธีหา Inbound ID

เข้า 3x-ui Panel → **Inbounds** → ดูเลข **ID** ของ inbound ที่ต้องการ แล้วนำไปใส่ใน `AIS_INBOUND_ID` และ `TRUE_INBOUND_ID`

---

## Deploy บน Railway

1. เปลี่ยนชื่อ `railway.toml.txt` → `railway.toml`
2. Push โค้ดขึ้น GitHub
3. สร้าง Railway project แล้วเชื่อม repo
4. ตั้ง Variables ตามตารางด้านบน
5. สร้าง **Volume** แล้ว mount ที่ `/data` (สำคัญ — ป้องกันข้อมูลหายตอน redeploy)
6. Deploy — Railway จะรัน `python main.py` อัตโนมัติ

---

## Protocol ที่รองรับ

| Protocol | สร้าง client | สร้าง link |
|---|:---:|:---:|
| VLESS | ✅ | ✅ |
| VMess | ✅ | ✅ |
| Trojan | ✅ | ✅ |
| Shadowsocks | ✅ | ✅ |
| Hysteria / Hysteria2 | ✅ | ✅ |
| mixed, socks, http, wireguard, dokodemo | ✅ | ❌ |

---

## คำสั่งผู้ใช้ทั่วไป

| คำสั่ง | ฟังก์ชัน |
|---|---|
| `/start` | แสดงเมนูหลัก |
| `/addclient` | ซื้อ/สร้าง client VPN โดยหักเครดิต |
| `/freeclient` | ทดลองใช้งานฟรีตามเงื่อนไขที่แอดมินตั้ง |
| `/mycredit` | ดูเครดิตคงเหลือ |
| `/addmycredit` | เติมเครดิตด้วยซองอั่งเปา TrueMoney |
| `/mycodes` | ดูโค้ด/ลิงก์ที่เคยสร้าง |
| `/Enterthecode` | กรอกโค้ดเครดิตหรือโค้ดรีเซ็ตสิทธิ์ทดลอง |
| `/checkprice` | ดูราคาต่อวัน |
| `/cancel` | ยกเลิกขั้นตอนที่กำลังทำ |

---

## คำสั่งแอดมิน

### เครดิตและราคา

| คำสั่ง | ฟังก์ชัน |
|---|---|
| `/addcredits @user จำนวน` | เพิ่มเครดิต |
| `/addcredits user_id จำนวน` | เพิ่มเครดิตด้วย user ID |
| `/Deletecredits @user จำนวน` | ลบเครดิต |
| `/deletecredits user_id จำนวน` | ลบเครดิตด้วย user ID |
| `/setprice จำนวน` | ตั้งราคาต่อวัน |

### TrueMoney / เครดิตอัตโนมัติ

| คำสั่ง | ฟังก์ชัน |
|---|---|
| `/Settingsmycredit` | เมนูตั้งค่าระบบเติมเครดิตทั้งหมด |
| `/setangpaophone เบอร์` | ตั้งเบอร์รับซองอั่งเปาแบบเร็ว |
| `/setangpaorate จำนวน` | ตั้งเครดิตต่อ 1 บาท |
| `/checkangpaophone` | ตรวจสอบเบอร์รับซอง อัตรา และสถานะ |

### การขายและช่องทาง

| คำสั่ง | ฟังก์ชัน |
|---|---|
| `/toggleaddclient` | เปิด/ปิดระบบ `/addclient` |
| `/buydm` | เปิดให้ซื้อผ่าน DM ได้ |
| `/nobuydm` | ปิดซื้อผ่าน DM และให้ซื้อจากทุกกลุ่ม |
| `/nobuydm group_id` | จำกัดการซื้อเฉพาะกลุ่มที่กำหนด |
| `/open cancel` | เปิดปุ่ม `/cancel` ในขั้นตอนสร้างโค้ด |
| `/close cancel` | ปิดปุ่ม `/cancel` |

### ทดลองใช้ฟรี

| คำสั่ง | ฟังก์ชัน |
|---|---|
| `/openfreeclient` | เปิดระบบทดลองใช้ฟรี |
| `/offfreeclient` | ปิดระบบทดลองใช้ฟรี |
| `/freeclientlimit จำนวน` | ตั้งจำนวนครั้งต่อคนต่อรอบ |
| `/freeclienttime ชั่วโมง` | ตั้งอายุ client ทดลอง |
| `/freeclientResettime` | เลือกโหมดรีเซ็ตสิทธิ์ทดลอง |
| `/resetfreeclientlimit @user` | รีเซ็ตสิทธิ์ทดลองของผู้ใช้ |
| `/channelfreeclient` | ตั้งช่องทางที่ใช้ `/freeclient` ได้ |

### โค้ดเครดิตและโค้ดรีเซ็ตสิทธิ์

| คำสั่ง | ฟังก์ชัน |
|---|---|
| `/addcode` | สร้างโค้ดเครดิตหรือโค้ดรีเซ็ตสิทธิ์ทดลอง |
| `/deletecode ชื่อโค้ด` | ลบโค้ด |
| `/checkcode` | ดูโค้ดทั้งหมด |
| `/statuscode` | เปิด/ปิดระบบกรอกโค้ด |
| `/checkusercode ชื่อโค้ด` | ดูผู้ใช้ที่กรอกโค้ดนั้น |

### Log และรายการ

| คำสั่ง | ฟังก์ชัน |
|---|---|
| `/logbuy @user` | ดูประวัติซื้อของผู้ใช้ |
| `/logfree @user` | ดูประวัติทดลองใช้ฟรีของผู้ใช้ |
| `/logbuyall` | ดูประวัติซื้อรวมทุกคน |
| `/logfreeall` | ดูประวัติทดลองใช้ฟรีรวมทุกคน |
| `/listaddclient จำนวน` | ตั้งจำนวนรายการที่แสดงใน log ซื้อ |
| `/listfreeclient จำนวน` | ตั้งจำนวนรายการที่แสดงใน log ทดลองใช้ฟรี |

### การแสดงผลและเมนู

| คำสั่ง | ฟังก์ชัน |
|---|---|
| `/Sorting` | ตั้งการเรียงใน `/mycodes` |
| `/Purchaseinformation จำนวน` | ตั้งจำนวนโค้ดที่เก็บต่อผู้ใช้ |
| `/Showpurchaselist จำนวน` | ตั้งจำนวนโค้ดที่แสดงใน `/mycodes` |
| `/runstartflnish` | เปิดระบบเด้ง `/start` หลังจบคำสั่ง |
| `/runstartflnish วินาที` | เปิดและตั้งเวลาหน่วง |
| `/stopstartflnish` | ปิดระบบเด้ง `/start` |
| `/addrunstartflnish คำสั่ง` | เพิ่มคำสั่งที่ต้องเด้งเมนู |
| `/deleterunstartflnish คำสั่ง` | ลบคำสั่งจากรายการเด้งเมนู |
| `/startadmin` | แสดงเมนูคำสั่งแอดมินและสถานะระบบ |

---

## หมายเหตุสำคัญ

- ชื่อ client ที่สร้างใน 3x-ui จะเป็น `{Remark}-{ชื่อ}` สำหรับซื้อ และ `{Remark}-FREE-{ชื่อ}` สำหรับทดลองใช้ฟรี
- ถ้าใช้ในกลุ่ม ผู้ใช้ต้องเคยทัก `/start` ใน DM กับบอทก่อน บอทจึงจะส่งลิงก์ส่วนตัวได้
- `/addclient` จะคืนเครดิตอัตโนมัติเมื่อสร้าง client หรือดึงลิงก์ไม่สำเร็จ
- ซองอั่งเปาที่เคยใช้แล้วจะไม่ถูกเติมซ้ำ
- ฐานข้อมูล SQLite ควรอยู่บน persistent volume เช่น `/data/bot.db`

---

## Troubleshooting

| อาการ | สาเหตุที่เป็นไปได้ | วิธีแก้ |
|---|---|---|
| "เกิดข้อผิดพลาดในการเชื่อมต่อ" | Inbound ID ผิด หรือ panel offline | ตรวจ `AIS_INBOUND_ID` / `TRUE_INBOUND_ID` และดู Railway logs |
| "ระบบ VPN Panel ไม่พร้อมใช้งาน" | Login ไม่ผ่าน | ตรวจ `XUI_URL` และ `XUI_API_TOKEN` |
| `HTTP 403` ตอน login | IP ถูก fail2ban block | ใช้ `XUI_API_TOKEN` แทน |
| `HTTP 404` ตอนเรียก API | `XUI_URL` ขาด Sub-Path | เพิ่ม path ลับ เช่น `https://host:2053/secretpath` |
| สร้าง client ได้แต่ไม่มี link | Protocol ไม่รองรับ URL | ตรวจว่า inbound ใช้ VLESS/VMess/Trojan/SS/Hysteria |
| ข้อมูลหายหลัง redeploy | ไม่มี persistent volume | สร้าง Volume และตั้ง `DB_PATH=/data/bot.db` |
