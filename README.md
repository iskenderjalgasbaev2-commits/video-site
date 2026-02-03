# import telebot
import json
import os

TOKEN = '8207656574:AAHJpw4qz_RTSpmni08YVBkpREwA9l6CV14'
bot = telebot.TeleBot(TOKEN)

VIDEO_FILE = "videos.json"

if os.path.exists(VIDEO_FILE):
    with open(VIDEO_FILE, "r") as f:
        video_dict = json.load(f)
else:
    video_dict = {}

# /start komandasiga javob
@bot.message_handler(commands=['start'])
def start(message):
    bot.reply_to(message, "Salom! Men Iskender Jalg'asbaev botiman. Video kod yuboring.")

# /viral komandasiga javob
@bot.message_handler(commands=['viral'])
def start_viral(message):
    bot.reply_to(message, "Salom! Men Iskender Jalg'asbaev botiman. Video kod yuboring.")

# Guruhga kelgan video
@bot.message_handler(content_types=['video'])
def save_video(message):
    if message.caption:
        code = message.caption.strip().lower()
        video_dict[code] = {"chat_id": message.chat.id, "msg_id": message.message_id}

        with open(VIDEO_FILE, "w") as f:
            json.dump(video_dict, f)

        bot.reply_to(message, f"Video saqlandi! Kod: {code}")
    else:
        bot.reply_to(message, "Video yuborildi, lekin caption (kod) bo‘sh!")

# Kod yuborilganda video jo‘natish
@bot.message_handler(func=lambda message: not message.text.startswith("/"))
def send_video_by_code(message):
    code = message.text.strip().lower()

    if code in video_dict:
        data = video_dict[code]
        try:
            bot.forward_message(message.chat.id, data["chat_id"], data["msg_id"])
        except Exception as e:
            bot.reply_to(message, f"Xatolik yuz berdi: {e}")
    else:
        bot.reply_to(message, "Bunday kod topilmadi!")

bot.polling()
