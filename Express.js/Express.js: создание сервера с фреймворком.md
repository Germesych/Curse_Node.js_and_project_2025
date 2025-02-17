Отлично! Теперь разберем, как **создать сервер на Express.js** 🚀  

---

# **Express.js: создание сервера с фреймворком**  

## **1. Что такое Express.js?**  
🔹 **Express.js** — это минималистичный **фреймворк** для создания веб-приложений на Node.js.  
🔹 Позволяет **упростить** работу с HTTP-запросами, маршрутами и middleware.  
🔹 Используется для API, серверных рендеров и обработки данных.  

📌 **Почему Express?**  
✅ Простота и удобство.  
✅ Гибкость (легко расширять).  
✅ Множество **пакетов и модулей**.  
✅ Подходит для REST API и SSR.  

---

## **2. Установка Express.js**  
📌 Установим Express в новый проект:  
```bash
mkdir my-express-app && cd my-express-app
npm init -y
npm install express
```
📌 Проверим `package.json`, где появится `"express"` в `dependencies`.  

---

## **3. Создание простого сервера**  
📌 Создадим файл `server.js` и напишем **минимальный сервер**:  
```js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('Привет, Express!');
});

app.listen(3000, () => {
    console.log('Сервер запущен на http://localhost:3000');
});
```
🔹 `express()` — создаем сервер.  
🔹 `app.get('/', (req, res) => {...})` — маршрут для `GET /`.  
🔹 `res.send('...')` — отправляем ответ.  
🔹 `app.listen(3000, () => {...})` — слушаем порт **3000**.  

📌 Запускаем сервер:  
```bash
node server.js
```
📌 Открываем **http://localhost:3000** — видим `Привет, Express!`.  

---

## **4. Маршрутизация в Express**  
📌 Обрабатываем **разные пути** (`/`, `/about`, `/api`):  
```js
app.get('/', (req, res) => res.send('Главная страница'));
app.get('/about', (req, res) => res.send('О нас'));
app.get('/api', (req, res) => res.json({ message: 'API работает' }));
```
📌 Открываем `/api` — сервер вернет JSON-объект.  

---

## **5. Middleware (промежуточные обработчики)**  
📌 Middleware — это **функции, которые выполняются перед основным обработчиком**.  
```js
app.use((req, res, next) => {
    console.log(`Запрос: ${req.method} ${req.url}`);
    next(); // Передаем управление дальше
});
```
📌 Теперь в консоли будут **логи каждого запроса**.  

---

## **6. Обработка POST-запросов**  
📌 Добавляем поддержку JSON-запросов:  
```js
app.use(express.json());

app.post('/send', (req, res) => {
    console.log('Полученные данные:', req.body);
    res.send('Данные получены');
});
```
📌 Отправляем `POST`-запрос через **Postman или cURL**.  

---

## **7. Работа с файлами (статические файлы)**  
📌 Отдаем файлы из папки `public`:  
```js
app.use(express.static('public'));
```
📌 Теперь файл `public/index.html` будет доступен по `http://localhost:3000/index.html`.  

---

## **8. Подключение шаблонизаторов (Pug, EJS)**  
📌 Устанавливаем Pug:  
```bash
npm install pug
```
📌 Включаем Pug в Express:  
```js
app.set('view engine', 'pug');

app.get('/hello', (req, res) => {
    res.render('index', { title: 'Привет', message: 'Express + Pug' });
});
```
📌 Файл `views/index.pug`:  
```pug
html
  head
    title= title
  body
    h1= message
```
📌 Открываем `http://localhost:3000/hello` — рендерится Pug-шаблон.  

---

## **9. Практическое задание 🚀**  
1️⃣ Создай сервер на Express.js.  
2️⃣ Добавь **GET**-маршруты (`/`, `/about`, `/api`).  
3️⃣ Реализуй **POST**-маршрут `/send`, который принимает JSON.  
4️⃣ Отдавай статические файлы из папки **public**.  
5️⃣ Используй **Pug** или **EJS** для рендеринга страниц.  
