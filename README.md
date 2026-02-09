import os
from telegram import Update
from telegram.ext import ApplicationBuilder, MessageHandler, filters, ContextTypes
from googleapiclient.discovery import build
import re

# YouTube API key
YOUTUBE_API_KEY = "BU_YERGA_SIZNING_API_KEY"

# Xabarlarni qayta ishlash funksiyasi
async def song_search(update: Update, context: ContextTypes.DEFAULT_TYPE):
    text = update.message.text

    # Agar YouTube linki bo'lsa
    match = re.search(r"(?:v=|youtu\.be/)([\w-]+)", text)
    if match:
        video_id = match.group(1)
        url = f"https://www.youtube.com/watch?v={video_id}"
        await update.message.reply_text(f"Topildi: {url}")
        return

    # Agar oddiy nom bo‘lsa, qidirish
    youtube = build('youtube', 'v3', developerKey=YOUTUBE_API_KEY)
    request = youtube.search().list(
        part='snippet',
        q=text,
        maxResults=1,
        type='video'
    )
    response = request.execute()
    if response['items']:
        video_id = response['items'][0]['id']['videoId']
        title = response['items'][0]['snippet']['title']
        url = f"https://www.youtube.com/watch?v={video_id}"
        await update.message.reply_text(f"{title}\n{url}")
    else:
        await update.message.reply_text("Hech narsa topilmadi 😔")

# Bot tokenni env variable dan olish
TOKEN = os.environ.get("TOKEN")

# Botni ishga tushirish
app = ApplicationBuilder().token(TOKEN).build()
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, song_search))
app.run_polling()
