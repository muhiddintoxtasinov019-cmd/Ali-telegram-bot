# Ali-telegram-bot
Shahsiy telegram bot
import os
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, filters, ContextTypes

# Sizning Telegram ID
ADMIN_ID = 7520683033
AI_MODE = False  # AI rejimini boshlang'ichda o'chiq

# /start komandasi
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Salom! 🤖\nMen ishlayapman. Admin band bo'lsa men javob beraman 🙂")

# /on komandasi - AI rejim yoqish
async def on(update: Update, context: ContextTypes.DEFAULT_TYPE):
    global AI_MODE
    if update.effective_user.id == ADMIN_ID:
        AI_MODE = True
        await update.message.reply_text("AI rejim YOQILDI ✅")

# /off komandasi - AI rejim o'chirish
async def off(update: Update, context: ContextTypes.DEFAULT_TYPE):
    global AI_MODE
    if update.effective_user.id == ADMIN_ID:
        AI_MODE = False
        await update.message.reply_text("AI rejim O‘CHDI ❌")

# Guruh yoki shaxsiy chatdagi xabarlar
async def messages(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if AI_MODE:
        await update.message.reply_text("Admin hozir band, lekin men yordam beraman 🙂")

# Bot tokenni env variable dan olish
TOKEN = os.environ.get("TOKEN")

# Botni ishga tushirish
app = ApplicationBuilder().token(TOKEN).build()
app.add_handler(CommandHandler("start", start))
app.add_handler(CommandHandler("on", on))
app.add_handler(CommandHandler("off", off))
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, messages))
app.run_polling()
