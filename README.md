import zipfile

import os



# Create the bot files content

bot_code = """

# intraday_bot.py

import requests

import datetime

import time

import os



ANGEL_CLIENT_ID = os.getenv("ANGEL_CLIENT_ID")

ANGEL_CLIENT_SECRET = os.getenv("ANGEL_CLIENT_SECRET")

ANGEL_FEED_TOKEN = os.getenv("ANGEL_FEED_TOKEN")

ANGEL_REFRESH_TOKEN = os.getenv("ANGEL_REFRESH_TOKEN")

TELEGRAM_BOT_TOKEN = os.getenv("TELEGRAM_BOT_TOKEN")

TELEGRAM_CHAT_ID = os.getenv("TELEGRAM_CHAT_ID")

TRADE_SYMBOLS = ["RELIANCE", "HDFCBANK", "TCS"]



def send_telegram(msg):

    url = f"https://api.telegram.org/bot{TELEGRAM_BOT_TOKEN}/sendMessage"

    data = {"chat_id": TELEGRAM_CHAT_ID, "text": msg}

    requests.post(url, data=data)



def fetch_ohlc(symbol):

    return {

        "high": 1500,

        "low": 1480,

        "close": 1495,

        "vwap": 1490,

        "volume": 1200000,

        "ltp": 1492

    }



def check_strategy(symbol):

    data = fetch_ohlc(symbol)

    cpr = (data['high'] + data['low'] + data['close']) / 3

    vwap = data['vwap']

    ltp = data['ltp']

    

    if ltp > cpr and ltp > vwap:

        return "BUY"

    elif ltp < cpr and ltp < vwap:

        return "SELL"

    else:

        return "HOLD"



def place_order(symbol, action):

    send_telegram(f"{action} signal triggered for {symbol}")



def run_bot():

    while True:

        now = datetime.datetime.now()

        if now.hour >= 9 and now.hour < 15:

            for symbol in TRADE_SYMBOLS:

                signal = check_strategy(symbol)

                if signal != "HOLD":

                    place_order(symbol, signal)

            time.sleep(60)

        else:

            print("Market closed. Sleeping...")

            time.sleep(300)



if __name__ == "__main__":

    send_telegram("Intraday bot started")

    run_bot()

"""



requirements = "requests\n"

start_script = "#!/bin/bash\npython intraday_bot.py\n"



# Save all files and zip them

zip_path = "/mnt/data/intraday_bot_render.zip"

with zipfile.ZipFile(zip_path, 'w') as zipf:

    zipf.writestr("intraday_bot.py", bot_code)

    zipf.writestr("requirements.txt", requirements)

    zipf.writestr("start.sh", start_script)



zip_path
