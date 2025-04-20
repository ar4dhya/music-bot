from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, ContextTypes, filters
from youtubesearchpython import VideosSearch
import yt_dlp
import os

BOT_TOKEN = "YOUR_TELEGRAM_BOT_TOKEN"

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Send me a YouTube/Spotify/Apple Music link and I’ll give you an MP3!")

async def handle_link(update: Update, context: ContextTypes.DEFAULT_TYPE):
    url = update.message.text
    await update.message.reply_text("Processing...")

    if "youtube.com" in url or "youtu.be" in url:
        yt_url = url
    else:
        # Treat any other music link as a search query
        result = VideosSearch(url, limit=1).result()
        yt_url = result['result'][0]['link']

    await update.message.reply_text(f"Found: {yt_url}\nDownloading MP3...")

    ydl_opts = {
        'format': 'bestaudio/best',
        'outtmpl': 'song.%(ext)s',
        'postprocessors': [{
            'key': 'FFmpegExtractAudio',
            'preferredcodec': 'mp3',
            'preferredquality': '192',
        }],
        'quiet': True,
    }

    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        ydl.download([yt_url])

    await update.message.reply_audio(audio=open("song.mp3", "rb"))
    os.remove("song.mp3")

app = ApplicationBuilder().token(BOT_TOKEN).build()
app.add_handler(CommandHandler("start", start))
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_link))

app.run_polling()
