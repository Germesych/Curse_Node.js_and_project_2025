# **Аутентификация и авторизация с использованием JWT**

## **1. Что такое JWT?**  
🔹 **JWT (JSON Web Token)** — это **стандарт для безопасной передачи информации** между клиентом и сервером в виде JSON-объекта. Он обычно используется для **аутентификации** и **авторизации**.  
🔹 JWT состоит из трех частей:
   1. **Header** — информация о типе токена и алгоритме подписи.
   2. **Payload** — данные, которые передаются (например, информация о пользователе).
   3. **Signature** — подпись для проверки целостности и подлинности данных.

Пример JWT:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

---

## **2. Установка зависимостей**  
Для работы с JWT мы установим библиотеку **jsonwebtoken**.  

```bash
npm install jsonwebtoken bcryptjs
```

- **jsonwebtoken** — для создания и проверки JWT.
- **bcryptjs** — для хеширования паролей.

---

## **3. Создание пользователя и аутентификация с JWT**  
Мы будем хранить данные пользователя в базе, а затем использовать JWT для аутентификации.

### Пример регистрации пользователя:

1. Хешируем пароль с помощью **bcryptjs**.
2. Создаем токен с помощью **jsonwebtoken**.

```js
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
const User = require('./models/User'); // Модель пользователя

// Регистрация
app.post('/register', async (req, res) => {
    const { email, password } = req.body;

    // Проверка на существующего пользователя
    const existingUser = await User.findOne({ email });
    if (existingUser) {
        return res.status(400).json({ error: 'Пользователь уже существует' });
    }

    // Хеширование пароля
    const hashedPassword = await bcrypt.hash(password, 10);

    // Создание нового пользователя
    const newUser = new User({
        email,
        password: hashedPassword
    });

    await newUser.save();

    // Создание JWT
    const token = jwt.sign({ userId: newUser._id }, 'secret_key', { expiresIn: '1h' });

    res.status(201).json({ message: 'Пользователь зарегистрирован', token });
});
```

### Пример входа (аутентификация):
```js
// Вход
app.post('/login', async (req, res) => {
    const { email, password } = req.body;

    // Проверка пользователя
    const user = await User.findOne({ email });
    if (!user) {
        return res.status(400).json({ error: 'Пользователь не найден' });
    }

    // Проверка пароля
    const isMatch = await bcrypt.compare(password, user.password);
    if (!isMatch) {
        return res.status(400).json({ error: 'Неверный пароль' });
    }

    // Создание JWT
    const token = jwt.sign({ userId: user._id }, 'secret_key', { expiresIn: '1h' });

    res.status(200).json({ message: 'Вход успешен', token });
});
```

---

## **4. Защищенные маршруты с JWT**  
Чтобы защитить маршруты, нужно проверять JWT в заголовке запроса и извлекать данные о пользователе.

### Middleware для проверки токена:
```js
const jwt = require('jsonwebtoken');

// Проверка токена
const authenticateToken = (req, res, next) => {
    const token = req.header('Authorization')?.replace('Bearer ', '');
    if (!token) {
        return res.status(401).json({ error: 'Токен не предоставлен' });
    }

    try {
        const decoded = jwt.verify(token, 'secret_key');
        req.userId = decoded.userId; // Сохраняем userId в запросе
        next(); // Передаем управление дальше
    } catch (err) {
        return res.status(403).json({ error: 'Неверный токен' });
    }
};

// Пример защищенного маршрута
app.get('/profile', authenticateToken, (req, res) => {
    res.json({ message: 'Доступ к профилю разрешен', userId: req.userId });
});
```

---

# **Работа с файлами в Express**

## **1. Основы работы с файлами в Express**  
В Express часто возникает необходимость работы с файлами — загружать их, сохранять или отдавать пользователям. Для этого мы используем модуль **multer** для загрузки файлов и стандартные средства Node.js для работы с файловой системой.

### Установка multer:
```bash
npm install multer
```

---

## **2. Загрузка файлов с помощью Multer**  
**Multer** позволяет легко обрабатывать загрузку файлов. Мы можем ограничить типы файлов, размер и место, куда они будут сохраняться.

### Пример загрузки файла:
```js
const multer = require('multer');
const path = require('path');

// Настройки хранения файлов
const storage = multer.diskStorage({
    destination: (req, file, cb) => {
        cb(null, 'uploads/'); // Папка для хранения файлов
    },
    filename: (req, file, cb) => {
        cb(null, Date.now() + path.extname(file.originalname)); // Уникальное имя
    }
});

const upload = multer({ storage });

// Маршрут для загрузки одного файла
app.post('/upload', upload.single('file'), (req, res) => {
    res.json({ message: 'Файл загружен', file: req.file });
});
```

### Пример загрузки нескольких файлов:
```js
app.post('/uploadMultiple', upload.array('files', 3), (req, res) => {
    res.json({ message: 'Файлы загружены', files: req.files });
});
```

---

## **3. Отдача файлов пользователю**  
Для отдачи статических файлов можно использовать встроенную функцию `express.static()`, а также настраивать маршруты для отдачи конкретных файлов.

### Пример отдачи статических файлов:
```js
app.use(express.static('public'));
```

### Пример отдачи конкретного файла:
```js
app.get('/file/:filename', (req, res) => {
    const { filename } = req.params;
    const filePath = path.join(__dirname, 'uploads', filename);
    res.sendFile(filePath);
});
```

---

## **Практическое задание 🚀**

1️⃣ Реализуйте **аутентификацию и авторизацию** с JWT для вашего приложения.  
2️⃣ Сделайте **защищенные маршруты**, которые доступны только авторизованным пользователям.  
3️⃣ Напишите маршруты для **загрузки одного и нескольких файлов**.  
4️⃣ Реализуйте функционал **отдачи файлов** пользователю по запросу.
