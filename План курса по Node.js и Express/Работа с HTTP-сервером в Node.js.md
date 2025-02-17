Отлично! Теперь разберем, как **работает HTTP-сервер в Node.js**.  

---

# **Работа с HTTP-сервером в Node.js**  

## **1. Что такое HTTP-сервер?**  
🔹 HTTP-сервер принимает **запросы** от клиентов (например, браузеров) и **отправляет** им **ответы**.  
🔹 В Node.js сервер создается с помощью встроенного модуля **`http`**.  
🔹 Запросы обрабатываются **асинхронно**, без блокировки основного потока.  

---

## **2. Создание простого HTTP-сервера**  
### **📌 Базовый HTTP-сервер**
```js
const http = require('http');

const server = http.createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Привет, мир!');
});

server.listen(3000, () => {
    console.log('Сервер запущен на http://localhost:3000');
});
```
📌 Открываем браузер и заходим на `http://localhost:3000` — увидим `Привет, мир!`.  

🔹 **Разбор кода:**  
- `http.createServer((req, res) => {...})` — создаем сервер.  
- `res.writeHead(200, { 'Content-Type': 'text/plain' })` — отправляем заголовки ответа.  
- `res.end('Привет, мир!')` — отправляем данные клиенту.  
- `server.listen(3000, () => {...})` — сервер слушает порт **3000**.  

---

## **3. Обработка запросов (req)**  
📌 В объекте `req` хранятся данные о **запросе**:  

### **📌 Получаем URL и метод**
```js
const server = http.createServer((req, res) => {
    console.log(`Запрос: ${req.method} ${req.url}`);

    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Запрос обработан');
});

server.listen(3000, () => console.log('Сервер запущен'));
```
🔹 `req.method` — метод запроса (GET, POST, PUT, DELETE).  
🔹 `req.url` — путь запроса (`/`, `/about`, `/api/data`).  

---

## **4. Отправка HTML-страницы**  
📌 Чтобы отправить **HTML-страницу**, меняем `Content-Type`:  
```js
const server = http.createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/html' });
    res.end('<h1>Привет, мир!</h1><p>Это мой сервер на Node.js</p>');
});

server.listen(3000, () => console.log('Сервер запущен'));
```
📌 Теперь браузер отобразит **HTML-код**.  

---

## **5. Маршрутизация (обработка разных URL)**  
📌 Добавляем **разные ответы для разных страниц**:  
```js
const server = http.createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/html' });

    if (req.url === '/') {
        res.end('<h1>Главная</h1>');
    } else if (req.url === '/about') {
        res.end('<h1>О нас</h1>');
    } else {
        res.writeHead(404);
        res.end('<h1>Страница не найдена</h1>');
    }
});

server.listen(3000, () => console.log('Сервер запущен'));
```
📌 Открываем `http://localhost:3000/about` — видим страницу "О нас".  

---

## **6. Отправка JSON-данных (API-сервер)**  
📌 Отправляем JSON-объект клиенту:  
```js
const server = http.createServer((req, res) => {
    if (req.url === '/api') {
        res.writeHead(200, { 'Content-Type': 'application/json' });
        res.end(JSON.stringify({ message: 'Привет, мир!', status: 200 }));
    } else {
        res.writeHead(404);
        res.end('Страница не найдена');
    }
});

server.listen(3000, () => console.log('API-сервер запущен'));
```
📌 Открываем `http://localhost:3000/api` — сервер вернет JSON.  

---

## **7. Чтение файла и отправка в ответе (fs + http)**  
📌 Читаем файл `index.html` и отправляем пользователю:  
```js
const fs = require('fs');

const server = http.createServer((req, res) => {
    fs.readFile('index.html', (err, data) => {
        if (err) {
            res.writeHead(500);
            res.end('Ошибка сервера');
        } else {
            res.writeHead(200, { 'Content-Type': 'text/html' });
            res.end(data);
        }
    });
});

server.listen(3000, () => console.log('Сервер запущен'));
```
📌 Теперь сервер **читает файл и отправляет его клиенту**.  

---

## **8. Обработка POST-запросов**  
📌 Получаем данные из `POST`-запроса:  
```js
const server = http.createServer((req, res) => {
    if (req.method === 'POST' && req.url === '/send') {
        let body = '';

        req.on('data', (chunk) => {
            body += chunk.toString();
        });

        req.on('end', () => {
            console.log('Данные:', body);
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('Данные получены');
        });
    } else {
        res.writeHead(404);
        res.end('Страница не найдена');
    }
});

server.listen(3000, () => console.log('Сервер запущен'));
```
📌 Теперь можно отправить `POST`-запрос с JSON-данными.  

---

## **9. Практическое задание 🚀**  
1️⃣ Напиши сервер с разными страницами (`/`, `/about`, `/api`).  
2️⃣ Добавь обработку **POST-запроса** и вывод данных в консоль.  
3️⃣ Читай и отправляй **HTML-файл** в ответе.  
4️⃣ Создай **JSON API**, возвращающий список пользователей.  
