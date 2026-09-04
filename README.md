# w8_cicd_Sittaroad
เปิด failed run แล้วบันทึก:
| **Observation**                        | **Student answer**                                                                                                                                  |
| -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Failed job**                         | Test on Node.js 20                                                                                                                                  |
| **Failed step**                        | Run automated tests                                                                                                                                 |
| **Error message บรรทัดสำคัญ**          | `AssertionError [ERR_ASSERTION]: Expected values to be strictly equal`                                                                              |
| **Expected value**                     | `Hello, Nina! This test should fail.`                                                                                                               |
| **Actual value**                       | `Hello, Nina! CI/CD is working.`                                                                                                                    |
| **เหตุใด step หลังจากนั้นจึงไม่ทำงาน** | เพราะ Step `Run automated tests` ทำงานล้มเหลว (`exit code 1`) เมื่อมี test fail ทำให้ GitHub Actions หยุด workflow และไม่รัน step ถัดไปโดยอัตโนมัติ |


เปรียบเทียบ failed run กับ recovery run:
| **Question**                         | **Student answer**                                                                                                                                                    |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Commit SHA ที่ fail**              | SHA ของ commit ที่ทำให้ `npm test` fail                                                                                                                               |
| **Commit SHA ที่แก้แล้ว**            | SHA ของ commit หลังแก้ `index.test.js`                                                                                                                                |
| **เวลาที่ pipeline ใช้**             | Failed run: `…` / Recovery run: `…`                                                                                                                                   |
| **Pipeline ช่วยลดความเสี่ยงอย่างไร** | Pipeline รัน `npm ci` และ `npm test` อัตโนมัติทุกครั้งที่มีการ push หรือ pull request ทำให้ตรวจพบข้อผิดพลาดก่อนนำโค้ดไปใช้งาน ลดความเสี่ยงจากการ deploy โค้ดที่มี bug |


Part G — Exit Ticket

| ข้อ   | คำถาม                                                                               | คำตอบ                                                                                                                                                                                            |
| ----- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **1** | ส่วนใดของงานนี้คือ Continuous Integration?                                          | เมื่อมีการ **commit/push code** แล้ว pipeline ทำการติดตั้ง dependencies และรัน `npm test` อัตโนมัติ เพื่อตรวจสอบว่าโค้ดยังทำงานถูกต้อง                                                           |
| **2** | งานนี้มี Continuous Delivery หรือ Continuous Deployment แล้วหรือยัง? เพราะเหตุใด?   | **ยังไม่มี** เพราะ pipeline มีเพียงขั้นตอนตรวจสอบและทดสอบ code แต่ยังไม่มีขั้นตอน **deploy application ไปยัง server/production อัตโนมัติ**                                                       |
| **3** | Green pipeline ยืนยันอะไรได้บ้าง และยืนยันอะไรไม่ได้?                               | **ยืนยันได้:** ขั้นตอนใน pipeline ผ่านและ test ที่กำหนดผ่านทั้งหมด **ยืนยันไม่ได้:** application ไม่มี bug ทั้งหมด, production จะใช้งานได้ 100%, performance/security หรือระบบภายนอกจะไม่มีปัญหา |
| **4** | เหตุใดเราจึงต้องทดลองสร้าง red pipeline?                                            | เพื่อทดสอบว่า pipeline สามารถ **ตรวจจับข้อผิดพลาดได้จริง** และเข้าใจกระบวนการ **Fail → แก้ไข → Test ใหม่ → Green**                                                                               |
| **5** | หาก test ผ่านแต่ application ใช้งานจริงไม่ได้ ควรเพิ่ม test หรือ pipeline stage ใด? | ควรเพิ่ม **Integration Test / E2E Test** และอาจเพิ่ม **Deploy to Staging + Smoke Test/Health Check** เพื่อทดสอบ application ในสภาพแวดล้อมที่ใกล้เคียงการใช้งานจริง                               |


Self-study Extension 
Option 2: Add a Node.js Version Matrix
ปรับ workflow ให้ทดสอบมากกว่าหนึ่ง Node.js version และอธิบายว่า matrix ช่วยตรวจ compatibility อย่างไร

------------------------------------------------------------------

ปรับ ci.yml:14-18 ให้ใช้ matrix ทดสอบ Node.js 20 และ 22 แล้ว โดยแต่ละเวอร์ชันจะทำงานเป็น job แยกกัน และตั้งค่า setup-node จากค่าใน matrix

Matrix ช่วยตรวจ compatibility โดยรัน test suite เดียวกันบนหลาย runtime หากโค้ดหรือ dependency ทำงานไม่ได้กับเวอร์ชันใด job นั้นจะแจ้งแยกชัดเจน ขณะที่เวอร์ชันอื่นยังรายงานผลได้ตามปกติ

ตรวจสอบแล้วด้วย npm.cmd test: ผ่านทั้ง 2 tests.

