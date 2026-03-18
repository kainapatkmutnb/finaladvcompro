# 📚 Book Management System (CRUD SQLite & EJS)

โปรเจกต์ระบบจัดการข้อมูลหนังสือ (CRUD) ที่สร้างด้วย **Node.js**, **Express**, **SQLite** และ **EJS** ออกแบบมาเพื่อให้มือใหม่ (Beginners) เข้าใจโครงสร้างการทำงานของ Full-stack Web Application

---

## 🛠️ โครงสร้างระบบ (System Architecture)
โปรเจกต์นี้ทำงานแบบแยกส่วนเพื่อให้เข้าใจง่าย:
1. **Database:** เก็บข้อมูลไว้ในไฟล์ SQLite (.sqlite)
2. **Backend API (`Port 5000`):** ทำหน้าที่คุยกับฐานข้อมูลโดยตรง (เพิ่ม, ลบ, แก้ไข, ค้นหา)
3. **Frontend Web (`Port 8080`):** ทำหน้าที่สร้างหน้าเว็บให้คนใช้ โดยจะไปคุยกับ API อีกทีหนึ่งเพื่อเอาข้อมูลมาแสดง

---

## 🔍 อธิบายโค้ดฉบับมือใหม่ (Line-by-Line Breakdown)

### 1. ส่วนของ Backend API (`JS CRUDSQLite...`)
เป็นด่านหน้าที่จัดการกับข้อมูลจริงๆ:

```javascript
// ดึงข้อมูลทั้งหมดจากตาราง books
app.get("/books", (req, res) => {
    db.all("SELECT * FROM books", (err, rows) => { 
        // db.all = ดึง "ทั้งหมด", rows = ข้อมูลที่ดึงมาได้ (เป็น Array)
        if (err) {
            res.status(500).send(err); // ถ้าพัง ส่ง error 500 กลับไป
        } else {
            res.json(rows); // ถ้าสำเร็จ ส่งข้อมูลกลับในรูปแบบ JSON
        }
    });
});

// เพิ่มข้อมูลใหม่
app.post("/books", (req, res) => {
    const book = req.body; // ดึงข้อมูลที่ส่งมาจากหน้าเว็บ
    db.run("INSERT INTO books (title, author) VALUES (?,?)", [book.title, book.author], 
    function (err) {
        // this.lastID = ID ล่าสุดที่เพิ่งถูกเพิ่มเข้าไปในฐานข้อมูล
        book.id = this.lastID; 
        res.send(book);
    });
});
```

---

### 2. ส่วนของ Frontend Web Server (`JS server...`)
ทำหน้าที่สร้างหน้าเว็บสวยๆ ให้เรา:

```javascript
// เมื่อเข้าที่อยู่หลัก (localhost:8080)
app.get("/", async (req, res) => {
    try {
        // ใช้ axios ไป "ขอข้อมูล" จาก API Server (Port 5000)
        const response = await axios.get("http://localhost:5000/books");
        
        // เมื่อได้มาแล้ว ให้เอาไป "หยอด" ลงในไฟล์ books.ejs ในโฟลเดอร์ views
        res.render("books", { books: response.data });
    } catch (err) {
        res.status(500).send("Error");
    }
});
```

---

### 3. ส่วนของหน้าจอ EJS (`public/views/books.ejs`)
คือ HTML ที่สามารถเขียนโค้ด JavaScript ฝังลงไปได้:

```html
<!-- การเขียน loop แสดงข้อมูลทุุกแถวมาใส่ในตาราง -->
<% for (var i = 0; i < books.length; i++) { %>
  <tr>
      <td><%= books[i].title %></td>   <!-- <%= ... %> คือการ "แสดงค่า" ออกมาบนหน้าจอ -->
      <td><%= books[i].author %></td>
      <td>
          <a href="/book/<%= books[i].id %>">View</a> <!-- ลิงก์ไปยัง ID นั้นๆ -->
      </td>
  </tr>
<% } %>
```

---

## 🚀 สรุปคำศัพท์ที่ต้องรู้ก่อนสอบ
- **`req` (Request):** สิ่งที่ "ขอ" มาจาก Browser (เช่น `req.body` ข้อมูลจาก Form, `req.params.id` ข้อมูลจาก URL)
- **`res` (Response):** สิ่งที่ Server "ส่งกลับ" ไปหา Browser (เช่น `res.send`, `res.json`, `res.render`)
- **`Middleware` (`app.use`):** ปลั๊กอินเสริม เช่นให้ Server อ่าน JSON ได้ หรือเรียกใช้โฟลเดอร์ CSS ได้
- **`EJS` Tags:**
    - `<% ... %>` : เขียนโค้ด Logic (เงื่อนไข, วนซ้ำ) **แต่ไม่แสดงผล**
    - `<%= ... %>` : เขียนตัวแปรเพื่อ **แสดงผลออกมาบนหน้าเว็บ**

---

## ⚡ วิธีรันโปรแกรม
1. เปิด Terminal 1: `node "JS CRUDSQLite 3 columns.js"`
2. เปิด Terminal 2: `node "JS server 3 columns.js"`
3. เข้าชม: [http://localhost:8080](http://localhost:8080)

---
✨ **Happy Coding! ติดตรงไหนสอบถามได้ทันทีครับ** ✨
