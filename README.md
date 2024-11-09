# LabProject5
# แนวทางการทำงาน ESP32 project cook book
## 1. ระบุตัวอย่างที่ใช้ ว่าเอามาจากตัวอย่างไหน
ตัวอย่างที่ใช้ในโปรเจกต์นี้มาจาก ESP32 Project Cookbook ซึ่งมีตัวอย่างที่เกี่ยวข้องกับการควบคุม 7-segment display ผ่าน ESP32 
## 2. ระบุว่า จะแก้ไขตรงไหนบ้าง เพื่ออะไร
1. การเพิ่มการควบคุม LED: โดยในตัวอย่างนี้จะใช้ LED 8 ตัวควบคุมตามตัวเลขที่แสดงบน 7-segment display ซึ่งต่างจากตัวอย่างเดิมที่แสดงเพียงแค่ตัวเลขบน 7-segment display.
2. การปรับการแสดงผลของตัวเลข: โค้ดในตัวอย่างจะถูกปรับเพื่อให้แสดงตัวเลขจาก 1 ถึง 8 โดยแสดงบนหลัก 7-segment 2 หลัก (แทนการแสดงตัวเลขที่เป็นแค่หนึ่งตัวเดียว).
3. การจัดการกับการหน่วงเวลา (Delay): ปรับการหน่วงเวลาให้เหมาะสมสำหรับการแสดงผลตัวเลขและเปิด/ปิด LED ทีละตัว.
## 3. แสดงขั้นตอนการทำ project
**ขั้นตอนที่ 1 การต่อวงจร**
1. ต่อวงจรจาก ESP32 ไปยัง 7-segment display ตามขั้นตอนนี้

| 7-segment |ESP32 Pin | 
| -------- | -------- | 
| A | GPIO 12 | 
| B | GPIO 14 | 
| C	| GPIO 27 |
| D |	GPIO 26 |
| E	| GPIO 25 |
| F	| GPIO 33 |
| G	| GPIO 32 |

2. ต่อขา Control ของหลัก

| 7-segment |ESP32 Pin | 
| -------- | -------- | 
| Digit 1 | GPIO 4 | 
| Digit 2 | GPIO 5 | 

3. ต่อขา LED

| LED |ESP32 Pin | 
| -------- | -------- | 
| 1 | GPIO 15 | 
| 2 | GPIO 16 | 
| 3	| GPIO 17 |
| 4 |	GPIO 18 |
| 5	| GPIO 19 |
| 6	| GPIO 21 |
| 7	| GPIO 22 |
| 8	| GPIO 23 |


**ขั้นตอนที่ 2 การติดตั้งไลบรารีและการตั้งค่าบอร์ด**
1. ติดตั้ง Board ESP32
เปิด Arduino IDE แล้วไปที่ Tools → Board → Boards Manager... แล้วค้นหา "ESP32" แล้วคลิก Install
2. เลือกบอร์ด ESP32:
ไปที่ Tools → Board → เลือก ESP32 Dev Module
3. เลือกพอร์ตการเชื่อมต่อ:
ไปที่ Tools → Port แล้วเลือกพอร์ตที่เชื่อมต่อกับ ESP32

**ขั้นตอนที่ 3 เขียนโค้ด**
 ```cpp
int segmentPins[] = {12, 14, 27, 26, 25, 33, 32}; 
int digitPins[] = {4, 5};  

int numbers[10][7] = {
  {1, 1, 1, 1, 1, 1, 0},  // 0
  {0, 1, 1, 0, 0, 0, 0},  // 1
  {1, 1, 0, 1, 1, 0, 1},  // 2
  {1, 1, 1, 1, 0, 0, 1},  // 3
  {0, 1, 1, 0, 0, 1, 1},  // 4
  {1, 0, 1, 1, 0, 1, 1},  // 5
  {1, 0, 1, 1, 1, 1, 1},  // 6
  {1, 1, 1, 0, 0, 0, 0},  // 7
  {1, 1, 1, 1, 1, 1, 1},  // 8
  {1, 1, 1, 1, 0, 1, 1}   // 9
};

int ledPins[] = {15, 16, 17, 18, 19, 21, 22, 23}; 

void setup() {
  for (int i = 0; i < 7; i++) {
    pinMode(segmentPins[i], OUTPUT);  
  }

  for (int i = 0; i < 2; i++) {
    pinMode(digitPins[i], OUTPUT);  
  }

  for (int i = 0; i < 8; i++) {
    pinMode(ledPins[i], OUTPUT);  
  }
}

void loop() {
  for (int num = 1; num <= 8; num++) {
    digitalWrite(ledPins[num - 1], HIGH);

    displayDigit(num);

    delay(2000);

    digitalWrite(ledPins[num - 1], LOW);
  }
}

void displayDigit(int num) {
  for (int digit = 0; digit < 2; digit++) {
    digitalWrite(digitPins[digit], HIGH);  
    displayNumber(num);  
    delay(500);  
    digitalWrite(digitPins[digit], LOW);  
  }
}

void displayNumber(int num) {
  // ควบคุมการเปิด/ปิด segment ตามค่าที่กำหนดใน numbers[]
  for (int i = 0; i < 7; i++) {
    digitalWrite(segmentPins[i], numbers[num][i] == 1 ? HIGH : LOW);
  }
}
 ```

## 4. แสดงผลการทำ project
เมื่ออัพโหลดโค้ดแล้ว ESP32 จะทำการควบคุมการแสดงผลบน 7-segment display ดังนี้:
1. ตัวเลข 1-8 จะถูกแสดงบน 7-segment display
2. LED 8 ตัวจะถูกเปิดตามลำดับหมายเลขที่แสดงบน 7-segment, โดย LED ตัวแรกจะเปิดเมื่อเลข 1 แสดง, LED ตัวที่สองเมื่อเลข 2 แสดง, เป็นต้น
3. เมื่อแสดงตัวเลขครบแล้ว LED จะถูกปิดและเริ่มทำการแสดงเลขใหม่อีกครั้ง


https://github.com/user-attachments/assets/78b4ce9e-b32b-4802-ad39-369ba838422e


## 5. สรุปผลการทำ project
จากการทำโปรเจกต์นี้:
1. สามารถแสดงตัวเลขจาก 1 ถึง 8 บน 7-segment display ได้สำเร็จ
2. LED 8 ตัวสามารถเปิดตามลำดับตัวเลขที่แสดงบน 7-segment display
3. การทำงานของ ESP32 ในการควบคุมทั้ง 7-segment display และ LED นั้นสามารถทำได้อย่างมีประสิทธิภาพ
