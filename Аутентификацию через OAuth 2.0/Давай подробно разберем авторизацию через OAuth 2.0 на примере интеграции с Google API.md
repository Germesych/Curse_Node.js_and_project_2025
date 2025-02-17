Давай подробно разберем авторизацию через OAuth 2.0 на примере интеграции с Google API. Мы пройдем все основные этапы, чтобы ты мог использовать OAuth 2.0 для авторизации пользователей в своем приложении.

### Шаги для интеграции с Google OAuth 2.0

1. **Создание проекта в Google Cloud Console**
2. **Настройка OAuth 2.0 в Google Developer Console**
3. **Установка необходимых библиотек**
4. **Настройка и запуск приложения Express с OAuth 2.0**
5. **Обработка данных пользователя после авторизации**

---

### 1. Создание проекта в Google Cloud Console

Первое, что нужно сделать, это создать проект в [Google Cloud Console](https://console.cloud.google.com/):

1. Перейди в **Google Cloud Console**.
2. Создай новый проект.
3. В меню слева выбери **API & Services > Credentials**.
4. Нажми на **Create Credentials** и выбери **OAuth 2.0 Client ID**.
5. Укажи название приложения и URL для перенаправления (например, `http://localhost:3000/auth/google/callback`).
6. Скачай **Client Secret** JSON файл или запиши данные `Client ID` и `Client Secret`, они понадобятся для настройки приложения.

---

### 2. Настройка OAuth 2.0 в Google Developer Console

Google OAuth 2.0 требует двух ключевых параметров:
- **Client ID** — уникальный идентификатор твоего приложения.
- **Client Secret** — секретный ключ, который необходимо использовать для аутентификации.

### 3. Установка необходимых библиотек

Чтобы интегрировать OAuth 2.0 в наше Express-приложение, нам понадобятся следующие библиотеки:

- **passport** — для аутентификации.
- **passport-google-oauth20** — для стратегии аутентификации через Google OAuth 2.0.
- **express-session** — для хранения сессий.

Устанавливаем их с помощью npm:

```bash
npm install express passport passport-google-oauth20 express-session
```

---

### 4. Настройка и запуск приложения Express с OAuth 2.0

Теперь мы настроим Express-приложение для использования Google OAuth 2.0.

#### Основной код приложения:

```js
const express = require('express');
const passport = require('passport');
const GoogleStrategy = require('passport-google-oauth20').Strategy;
const session = require('express-session');

const app = express();

// Настройка сессии
app.use(session({
  secret: 'your-secret-key',
  resave: false,
  saveUninitialized: true
}));

// Инициализация passport
app.use(passport.initialize());
app.use(passport.session());

// Стратегия Google OAuth2
passport.use(new GoogleStrategy({
  clientID: 'YOUR_GOOGLE_CLIENT_ID', // полученный Client ID
  clientSecret: 'YOUR_GOOGLE_CLIENT_SECRET', // полученный Client Secret
  callbackURL: 'http://localhost:3000/auth/google/callback', // URL для перенаправления после успешной авторизации
}, function(accessToken, refreshToken, profile, done) {
  // Сохраняем данные о пользователе в сессии
  return done(null, profile);
}));

// Сериализация пользователя для сессии
passport.serializeUser(function(user, done) {
  done(null, user);
});

// Десериализация пользователя
passport.deserializeUser(function(obj, done) {
  done(null, obj);
});

// Маршрут для аутентификации через Google
app.get('/auth/google',
  passport.authenticate('google', { scope: ['https://www.googleapis.com/auth/plus.login'] })
);

// Обработчик для перенаправления после успешной аутентификации
app.get('/auth/google/callback',
  passport.authenticate('google', { failureRedirect: '/' }),
  function(req, res) {
    // Успешная аутентификация
    res.redirect('/');
  }
);

// Маршрут для выхода
app.get('/logout', (req, res) => {
  req.logout((err) => {
    if (err) return next(err);
    res.redirect('/');
  });
});

// Главная страница
app.get('/', (req, res) => {
  if (req.isAuthenticated()) {
    res.send(`<h1>Welcome ${req.user.displayName}</h1><a href="/logout">Logout</a>`);
  } else {
    res.send('<h1>Home Page</h1><a href="/auth/google">Login with Google</a>');
  }
});

// Запуск сервера
app.listen(3000, () => {
  console.log('Server started on http://localhost:3000');
});
```

### Пояснение:

1. **Настройка сессии**:
   Мы используем `express-session` для хранения сессии, которая будет использоваться для хранения данных пользователя после успешной аутентификации.

2. **Настройка стратегии OAuth2**:
   Мы создаем стратегию с помощью `passport-google-oauth20` и передаем `clientID`, `clientSecret` и `callbackURL`, которые мы получили на этапе регистрации приложения в Google Developer Console. Также указана функция `done`, которая вызывается, когда авторизация успешна.

3. **Маршруты**:
   - `/auth/google`: перенаправляет пользователя на страницу авторизации Google.
   - `/auth/google/callback`: обрабатывает перенаправление от Google после успешной авторизации. Если авторизация прошла успешно, пользователь будет перенаправлен на главную страницу.

4. **Сериализация и десериализация пользователя**:
   Для хранения данных о пользователе в сессии мы сериализуем объект пользователя и сохраняем его в сессии, чтобы использовать его в других частях приложения.

---

### 5. Обработка данных пользователя после авторизации

После того как пользователь авторизуется через Google, его профиль будет доступен в объекте `profile`, который передается в функцию `done`.

В данном примере, мы просто выводим имя пользователя (`req.user.displayName`) на главной странице.

---

### Заключение

Ты настроил простую систему авторизации через **Google OAuth 2.0**. В этом примере:
- Используется Passport для работы с OAuth.
- Авторизация выполняется через Google.
- После успешной авторизации данные о пользователе сохраняются в сессии, и они доступны на других страницах.

Если нужно, можем углубиться в более сложные настройки OAuth 2.0, например, добавление нескольких стратегий для других сервисов или обработку различных сценариев ошибок.
