import requests
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters

# CoinGecko API uchun URL
api_url = "https://api.coingecko.com/api/v3/coins/bitcoin"

# Bot tokeni
TOKEN = "7030647754:AAGQ6BZiqmlzhBX4OGPa0ERAgkedoWadLt0"

# "/start" buyrug'ini qabul qiladigan funksiya
def start(update, context):
    update.message.reply_text("Assalomu alaykum! Bot ishlashga tayyor 😊")

# "coin" so'zi kelsa, kriptovalyuta ma'lumotlarini olish
def get_crypto_data(update, context):
    response = requests.get(api_url)
    if response.status_code == 200:
        data = response.json()
        update.message.reply_text(data)
    else:
        update.message.reply_text("Kriptovalyuta ma'lumotlarini olishda xatolik yuz berdi 😔")

def main():
    updater = Updater(TOKEN, use_context=True)
    dp = updater.dispatcher

    # "/start" buyrug'ini ushlab turish
    dp.add_handler(CommandHandler("start", start))
    
    # "coin" so'zi kelsa, kriptovalyuta ma'lumotlarini olish
    dp.add_handler(MessageHandler(Filters.text & ~Filters.command, get_crypto_data))

    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
