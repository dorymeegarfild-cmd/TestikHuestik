# 🤖 Настройка Telegram бота для модерации

## 📋 Что было реализовано

### ✅ Интерактивные кнопки
- **✅ Одобрить** - сразу одобряет регистрацию пользователя
- **❌ Отклонить** - отклоняет регистрацию с системным сообщением
- **💬 Написать сообщение** - показывает инструкцию для отправки сообщения

### ✅ Команды бота
- `/message <userId> <сообщение>` - отправить сообщение пользователю
- `/help` - показать справку по командам

### ✅ Автоматические уведомления
- При новой регистрации модератор получает красивое сообщение с кнопками
- Все действия логируются и сохраняются
- Пользователь получает уведомление о решении модератора

## 🔧 Настройка на VPS

### 1. Создание бота
1. Напишите @BotFather в Telegram
2. Создайте нового бота командой `/newbot`
3. Получите токен бота

### 2. Настройка webhook
```bash
# Установите webhook для получения обновлений
curl -X POST "https://api.telegram.org/bot<YOUR_BOT_TOKEN>/setWebhook" \
     -H "Content-Type: application/json" \
     -d '{"url": "https://your-domain.com/api/telegram-bot"}'
```

### 3. Переменные окружения
Добавьте в `.env.local`:
```env
TELEGRAM_BOT_TOKEN=your_bot_token_here
TELEGRAM_CHAT_ID=your_chat_id_here
```

### 4. Python скрипт для VPS (альтернатива)
Если хотите использовать Python вместо webhook:

```python
import requests
import json
from telegram import Bot, Update
from telegram.ext import Application, CommandHandler, CallbackQueryHandler

# Ваши настройки
BOT_TOKEN = "your_bot_token"
WEBHOOK_URL = "https://your-domain.com/api/telegram-bot"

def handle_callback_query(update: Update, context):
    """Обработка нажатий на кнопки"""
    query = update.callback_query
    query.answer()
    
    # Отправляем данные на ваш сайт
    data = {
        "callback_query": {
            "data": query.data,
            "message": {
                "chat": {"id": query.message.chat.id},
                "message_id": query.message.message_id,
                "text": query.message.text
            },
            "from": query.from_user
        }
    }
    
    response = requests.post(WEBHOOK_URL, json=data)
    return response.json()

def handle_message(update: Update, context):
    """Обработка текстовых сообщений"""
    message = update.message
    
    # Отправляем данные на ваш сайт
    data = {
        "message": {
            "text": message.text,
            "from": message.from_user,
            "chat": {"id": message.chat.id}
        }
    }
    
    response = requests.post(WEBHOOK_URL, json=data)
    return response.json()

def main():
    """Запуск бота"""
    application = Application.builder().token(BOT_TOKEN).build()
    
    # Добавляем обработчики
    application.add_handler(CallbackQueryHandler(handle_callback_query))
    application.add_handler(CommandHandler("message", handle_message))
    application.add_handler(CommandHandler("help", handle_message))
    
    # Запускаем бота
    application.run_polling()

if __name__ == '__main__':
    main()
```

## 🎯 Как это работает

### 1. Регистрация пользователя
1. Пользователь заполняет форму регистрации
2. Данные отправляются на `/api/auth/register`
3. Бот получает уведомление с кнопками действий

### 2. Модерация через бота
1. Модератор нажимает кнопку в Telegram
2. Бот отправляет callback на `/api/telegram-bot`
3. Статус пользователя обновляется
4. Пользователь получает уведомление на сайте

### 3. Отправка сообщений
1. Модератор использует команду `/message userId текст`
2. Сообщение сохраняется для пользователя
3. Пользователь видит его как системное уведомление

## 🔒 Безопасность

- Все действия логируются
- Проверка прав доступа к боту
- Валидация данных пользователей
- Защита от спама и злоупотреблений

## 📱 Уведомления пользователей

Пользователи получают уведомления через:
- Страницу модерации (`/moderation`)
- Системные сообщения на сайте
- Возможность интеграции с Telegram (опционально)

## 🚀 Готово к использованию!

Теперь у вас есть полноценная система модерации через Telegram бота с интерактивными кнопками и удобным интерфейсом!
