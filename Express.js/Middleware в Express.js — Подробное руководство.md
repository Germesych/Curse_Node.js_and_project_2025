# **Middleware в Express.js — Подробное руководство**  

## **1. Что такое Middleware?**  
🔹 Middl---

## **Что дальше?**  
Теперь переходим к **работе с базами данных (MongoDB, Mongoose)!**  

Как тебе материал? Нужно что-то пояснить? 😊могут изменять запрос (`req`) и ответ (`res`)** или передавать управление дальше.  
🔹 Используются для **логирования, обработки ошибок, проверки аутентификации, работы с JSON и т.д.**  

📌 **Общий вид Middleware:**  
```js
app.use((req, res, next) => {
    console.log('Middleware сработал');
    next(); // Передает управление дальше
});---

## **Что дальше?**  
Теперь переходим к **работе с базами данных (MongoDB, Mongoose)!**  

Как тебе материал? Нужно что-то пояснить? 😊` **обязательно вызываем**, иначе запрос "зависнет"!  

---

## **2. Виды Middleware**  
1️⃣ **Глобальные** (работают для всех маршрутов).  
2️⃣ **Локальные** (работают только для конкретного маршрута).  
3️⃣ **Встроенные** (`express.json()`, `express.static()`).  
4️⃣ **Ошибки** (обрабатывают ошибки в приложении).  

---

## **3. Глобальный Middleware**  
📌 Middleware срабатывает **на каждый запрос**.  
```js
app.use((req, res, next) => {
    console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
    next(); // Передаем управление дальше
});
```
📌 Теперь каждый запрос будет **логироваться в консоли**.  

---

## **4. Middleware для проверки токена (Авторизация)**  
📌 Проверяем, есть ли в заголовках `Authorization`:  
```js
app.use((req, res, next) => {
    const token = req.headers.authorization;
    if (!token) {
        return res.status(403).json({ error: 'Нет доступа' });
    }
    console.log('Токен:', token);
    next();
});
```
📌 Запрос б---

## **Что дальше?**  
Теперь переходим к **работе с базами данных (MongoDB, Mongoose)!**  

Как тебе материал? Нужно что-то пояснить? 😊= { id: 1, name: 'Александр' }; // Добавляем объект пользователя
    next();
});

app.get('/profile', (req, res) => {
    res.json(req.user);
});
```
📌 Открываем `http://localhost:3000/profile` — получаем `{ id: 1, name: 'Александр' }`.  

---

## **6. Локальный Middleware (только для одного маршрута)**  
📌 Middleware можно подключать только к **определенному маршруту**.  
```js
const checkAdmin = (req, res, next) => {
    if (req.query.admin !== 'true') {
        return res.status(403).json({ error: 'Доступ запрещен' });
    }
    next();
};

app.get('/admin', checkAdmin, (req, res) => {
    res.send('Добро пожаловать в админ-панель!');
});
```
📌 Открываем `http://localhost:3000/admin?admin=true` → Доступ разрешен.  
📌 Без `?admin=true` получим `403: Доступ запрещен`.  

---

## **7. Встроенные Middleware в Express**  
📌 **Работа с JSON (нужно для `POST`-запросов)**  
```js
app.use(express.json()); // Позволяет принимать JSON в `req.body`
```
📌 **Кодировка---

## **Что дальше?**  
Теперь переходим к **работе с базами данных (MongoDB, Mongoose)!**  

Как тебе материал? Нужно что-то пояснить? 😊.urlencoded({ extended: true }));
```
📌 **Отдача статических файлов**  
```js
app.use(express.static('public')); // Теперь файлы доступны в браузере
```

---

## **8. Middleware для обработки ошибок**  
📌 Ошибки можно **ловить и обрабатывать** в отдельном Middleware.  
```js
app.use((err, req, res, next) => {
    console.error('Ошибка:', err.message);
    res.status(500).json({ error: 'Ошибка сервера' });
});
```
📌 Если в коде будет `next(new Error('Что-то пошло не так'))`, сервер отправит `500: Ошибка сервера`.  

---

## **9. Комбинирование нескольких Middleware**  
📌 Можно вызывать несколько Middleware подряд:  
```js
const firstMiddleware = (req, res, next) => {
    console.log('Первый Middleware');
    next();
};

const secondMiddleware = (req, res, next) => {
    console.log('Второй Middleware');
    next();
};

app.get('/test', firstMiddleware, secondMiddleware, (req, res) => {
    res.send('Готово!');
});
```
📌 В консоли:  
```
Первый Middleware
Второй Middleware
```
📌 В браузере — `Готово!`.  

---

## **10. Практическое задание 🚀**  
1️⃣ Напиши **глобальный Middleware** для логирования запросов.  
2️⃣ Создай **локальный Middleware** для маршрута `/admin`, который требует `?admin=true`.  
3️⃣ Реализуй Middleware **для проверки заголовка `Authorization`**.  
4️⃣ Сделай **Middleware для обработки ошибок**.  
5️⃣ Добавь **`express.json()`**, чтобы сервер принимал `POST`-запросы с JSON.  
