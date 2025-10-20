# API Endpoints для Python Telegram Бота

## Базовый URL
\`\`\`
https://ваш-сайт.vercel.app/api
\`\`\`

## 1. Проверка статуса пользователя

**Endpoint:** `GET /moderation/status`

**Параметры:**
- `userId` (query parameter) - ID пользователя

**Пример запроса:**
\`\`\`python
import requests

response = requests.get(
    'https://ваш-сайт.vercel.app/api/moderation/status',
    params={'userId': 'user_1234567890_abc123'}
)
data = response.json()
print(data)
\`\`\`

**Ответ:**
\`\`\`json
{
  "status": "pending",
  "message": ""
}
\`\`\`

Возможные статусы: `"pending"`, `"approved"`, `"rejected"`

---

## 2. Одобрить регистрацию

**Endpoint:** `POST /moderation/approve`

**Body (JSON):**
\`\`\`json
{
  "userId": "user_1234567890_abc123",
  "message": "Добро пожаловать на платформу!"
}
\`\`\`

**Пример запроса:**
\`\`\`python
import requests

response = requests.post(
    'https://ваш-сайт.vercel.app/api/moderation/approve',
    json={
        'userId': 'user_1234567890_abc123',
        'message': 'Добро пожаловать на платформу!'
    }
)
data = response.json()
print(data)
\`\`\`

**Ответ:**
\`\`\`json
{
  "success": true,
  "message": "User approved successfully"
}
\`\`\`

---

## 3. Отклонить регистрацию

**Endpoint:** `POST /moderation/reject`

**Body (JSON):**
\`\`\`json
{
  "userId": "user_1234567890_abc123",
  "message": "Неверные данные. Попробуйте снова."
}
\`\`\`

**Пример запроса:**
\`\`\`python
import requests

response = requests.post(
    'https://ваш-сайт.vercel.app/api/moderation/reject',
    json={
        'userId': 'user_1234567890_abc123',
        'message': 'Неверные данные. Попробуйте снова.'
    }
)
data = response.json()
print(data)
\`\`\`

**Ответ:**
\`\`\`json
{
  "success": true,
  "message": "User rejected successfully"
}
\`\`\`

---

## 4. Отправить сообщение пользователю

**Endpoint:** `POST /moderation/message`

**Body (JSON):**
\`\`\`json
{
  "userId": "user_1234567890_abc123",
  "message": "Пожалуйста, проверьте правильность введенного email."
}
\`\`\`

**Пример запроса:**
\`\`\`python
import requests

response = requests.post(
    'https://ваш-сайт.vercel.app/api/moderation/message',
    json={
        'userId': 'user_1234567890_abc123',
        'message': 'Пожалуйста, проверьте правильность введенного email.'
    }
)
data = response.json()
print(data)
\`\`\`

**Ответ:**
\`\`\`json
{
  "success": true,
  "message": "Message sent successfully"
}
\`\`\`

---

## Пример Python Telegram Бота

\`\`\`python
from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import Application, CommandHandler, CallbackQueryHandler, ContextTypes, MessageHandler, filters
import requests

# Ваш URL сайта
SITE_URL = "https://ваш-сайт.vercel.app"

# Хранилище для ожидания сообщений
waiting_for_message = {}

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Бот модерации запущен!")

async def approve_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Команда: /approve user_id"""
    if len(context.args) < 1:
        await update.message.reply_text("Использование: /approve <user_id>")
        return
    
    user_id = context.args[0]
    waiting_for_message[update.effective_user.id] = ('approve', user_id)
    await update.message.reply_text(f"Введите сообщение для пользователя {user_id}:")

async def reject_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Команда: /reject user_id"""
    if len(context.args) < 1:
        await update.message.reply_text("Использование: /reject <user_id>")
        return
    
    user_id = context.args[0]
    waiting_for_message[update.effective_user.id] = ('reject', user_id)
    await update.message.reply_text(f"Введите причину отклонения для пользователя {user_id}:")

async def message_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Команда: /message user_id"""
    if len(context.args) < 1:
        await update.message.reply_text("Использование: /message <user_id>")
        return
    
    user_id = context.args[0]
    waiting_for_message[update.effective_user.id] = ('message', user_id)
    await update.message.reply_text(f"Введите сообщение для пользователя {user_id}:")

async def handle_message(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Обработка текстовых сообщений"""
    user_id = update.effective_user.id
    
    if user_id not in waiting_for_message:
        return
    
    action, target_user_id = waiting_for_message[user_id]
    message_text = update.message.text
    
    try:
        if action == 'approve':
            response = requests.post(
                f"{SITE_URL}/api/moderation/approve",
                json={'userId': target_user_id, 'message': message_text}
            )
        elif action == 'reject':
            response = requests.post(
                f"{SITE_URL}/api/moderation/reject",
                json={'userId': target_user_id, 'message': message_text}
            )
        elif action == 'message':
            response = requests.post(
                f"{SITE_URL}/api/moderation/message",
                json={'userId': target_user_id, 'message': message_text}
            )
        
        if response.status_code == 200:
            await update.message.reply_text(f"✅ Действие выполнено успешно!")
        else:
            await update.message.reply_text(f"❌ Ошибка: {response.text}")
    
    except Exception as e:
        await update.message.reply_text(f"❌ Ошибка соединения: {str(e)}")
    
    finally:
        del waiting_for_message[user_id]

def main():
    # Замените на ваш токен бота
    application = Application.builder().token("YOUR_BOT_TOKEN").build()
    
    application.add_handler(CommandHandler("start", start))
    application.add_handler(CommandHandler("approve", approve_command))
    application.add_handler(CommandHandler("reject", reject_command))
    application.add_handler(CommandHandler("message", message_command))
    application.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_message))
    
    application.run_polling()

if __name__ == '__main__':
    main()
\`\`\`

---

## Как это работает:

1. **Пользователь регистрируется** → Сайт отправляет уведомление в Telegram
2. **Модератор получает сообщение** с User ID и данными пользователя
3. **Модератор использует команды:**
   - `/approve user_123` → Бот просит ввести сообщение → Отправляет на сайт
   - `/reject user_123` → Бот просит ввести причину → Отправляет на сайт
   - `/message user_123` → Бот просит ввести сообщение → Отправляет на сайт
4. **Сайт обновляет статус** → Пользователь видит результат модерации

---

## Важные замечания:

- **User ID** генерируется автоматически при регистрации
- **Статусы хранятся в памяти** (для production используйте базу данных)
- **Polling каждые 5 секунд** - пользователь автоматически видит обновления
- **Замените URL** на ваш реальный домен Vercel
