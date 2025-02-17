Отлично! Теперь разберем **потоки (Streams) в Node.js**.  

---

# **Работа с потоками (Streams) в Node.js**  

## **1. Что такое потоки?**  
🔹 Потоки (Streams) — это способ обработки **больших объемов данных** частями, **без загрузки всего файла в память**.  
🔹 Это особенно полезно при работе с **файлами**, **сетевыми запросами**, **базами данных**.  

📌 В Node.js есть **4 типа потоков**:  
1️⃣ **Читающий поток (`Readable`)** – для чтения данных.  
2️⃣ **Записывающий поток (`Writable`)** – для записи данных.  
3️⃣ **Дуплексный поток (`Duplex`)** – одновременно для чтения и записи.  
4️⃣ **Трансформирующий поток (`Transform`)** – изменяет данные на лету.  

Все потоки являются экземплярами `EventEmitter` и работают через события (`data`, `end`, `error` и т. д.).  

---

## **2. Читающие потоки (Readable)**  
Читающий поток позволяет **читать данные частями**.  

### **📌 Пример: чтение файла потоком**
```js
const fs = require('fs');

const readStream = fs.createReadStream('large-file.txt', 'utf8');

readStream.on('data', (chunk) => {
    console.log('Получен кусок данных:', chunk.length);
});

readStream.on('end', () => {
    console.log('Чтение завершено');
});

readStream.on('error', (err) => {
    console.error('Ошибка:', err);
});
```
🔹 Данные читаются **по кускам**, а не загружаются полностью в память!  

---

## **3. Записывающие потоки (Writable)**  
Записывающий поток позволяет **записывать данные частями**.  

### **📌 Пример: запись файла потоком**
```js
const writeStream = fs.createWriteStream('output.txt');

writeStream.write('Первая строка\n');
writeStream.write('Вторая строка\n');
writeStream.end(); // Завершение записи

writeStream.on('finish', () => {
    console.log('Файл успешно записан!');
});

writeStream.on('error', (err) => {
    console.error('Ошибка записи:', err);
});
```
🔹 Запись выполняется **частями**, и файл закрывается только после `end()`.  

---

## **4. Передача данных через потоки (pipe)**  
Вместо загрузки файла в память **можно передавать данные напрямую**.  

### **📌 Пример: копирование файла через `pipe`**
```js
const readStream = fs.createReadStream('input.txt');
const writeStream = fs.createWriteStream('output.txt');

readStream.pipe(writeStream);

writeStream.on('finish', () => {
    console.log('Файл успешно скопирован!');
});
```
📌 **Здесь данные передаются напрямую, не загружаясь в память!**  

---

## **5. Дуплексные потоки (Duplex)**  
Дуплексные потоки **читают и записывают данные одновременно**.  

### **📌 Пример: создание дуплексного потока**
```js
const { Duplex } = require('stream');

const duplexStream = new Duplex({
    read(size) {
        this.push('Часть данных');
        this.push(null);
    },
    write(chunk, encoding, callback) {
        console.log('Получено для записи:', chunk.toString());
        callback();
    }
});

duplexStream.on('data', (chunk) => console.log('Прочитано:', chunk.toString()));
duplexStream.write('Данные в поток');
duplexStream.end();
```

---

## **6. Трансформирующие потоки (Transform)**  
Трансформирующий поток **изменяет данные на лету** (например, сжатие, шифрование).  

### **📌 Пример: конвертация в верхний регистр**
```js
const { Transform } = require('stream');

const upperCaseTransform = new Transform({
    transform(chunk, encoding, callback) {
        this.push(chunk.toString().toUpperCase());
        callback();
    }
});

process.stdin.pipe(upperCaseTransform).pipe(process.stdout);
```
📌 Этот код **читает ввод с клавиатуры и делает буквы заглавными**.  

---

## **7. Практическое задание 🚀**
1️⃣ Создай файл `bigfile.txt` с большим текстом.  
2️⃣ Напиши **читающий поток**, который выводит содержимое файла в консоль.  
3️⃣ Создай **записывающий поток**, который добавляет текст в `output.txt`.  
4️⃣ Используй **`pipe()`** для копирования `bigfile.txt` в `copy.txt`.  
5️⃣ Создай **трансформирующий поток**, который заменяет `Node.js` на `JavaScript` в потоке данных.  
