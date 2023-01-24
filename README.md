# infosec_bot
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
from datetime import datetime
import json, os, string, sys, threading, logging, time, re, random
import openai

#OpenAI API key
aienv = os.getenv('OPENAI_KEY')
if aienv == None:
    openai.api_key = "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"
else:
    openai.api_key = aienv
print(aienv)

#Telegram bot key
tgenv = os.getenv('TELEGRAM_KEY')
if tgenv == None:
    tgkey = "5884776359:AAHZ65ZwcHJZlRkncFTAYmHklyD3Z-0Zf1E"
else:
    tgkey = tgenv
print(tgenv)

# Enable logging
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
                    level=logging.INFO)
logger = logging.getLogger(__name__)


def start(update, context):
    update.message.reply_text("Hello! Welcome to our FUTURE .You can call me TERMINATOR too")


def help(update, context):
    update.message.reply_text("""
    The Following commands are available:
    /start -> Welcome to Infosec🔴🔴007
    /help ->This Message
    /about -> About Infosec
    /contact -> Developer Info
    
    """)

def error(update, context):
    logger.warning('Update "%s" caused error "%s"', context)


def about(update, context):
    update.message.reply_text("""
            Infosec is not just a chatbot. Its the future which going to tame you or tamed by you. It's an AI-enabled customer service solution that answers your questions, responds to your tweets, and helps you find the products you're looking for. Infosec has the power to save you time, increase your sales, and make your customer service operation more efficient. Now choice is Your CONSTRUCT OR DESTRUCT .
        """)


def contact(update, context):
    update.message.reply_text("Developer: 🔴HARSHIT & ANANYA🔴 \n CONTACT US @mscis_ignou \n")


def handle_message(update, context):
    chat_log = session.get('chat_log')
    answer = ask(update.message.text, chat_log)
    session['chat_log'] = append_interaction_to_chat_log(update.message.text, answer,
                                                         chat_log)
    update.message.reply_text(f"{str(answer)}")


def main():
    updater = telegram.ext.Updater(TOKEN, use_context=True)
    bot = updater.dispatcher

    bot.add_handler(telegram.ext.CommandHandler("start", start))
    bot.add_handler(telegram.ext.CommandHandler("help", help))
    bot.add_handler(telegram.ext.CommandHandler("about", about))
    bot.add_handler(telegram.ext.CommandHandler("contact", contact))
    bot.add_handler(telegram.ext.MessageHandler(
    telegram.ext.Filters.text, handle_message))
    
    def reset(bot, update):
    """Send a message when the command /reset is issued."""
    global chat_log
    global cache
    global qcache
    global tim
    global botname
    global username
    left = str(tim)
    if user == update.message.from_user.id:
        chat_log = None
        cache = None
        qcache = None
        botname = 'Infosec'
        username = 'infosec_007bot'
        update.message.reply_text('Bot has been reset, send a message!')
        return
    if tim == 1:
        chat_log = None
        cache = None
        qcache = None
        botname = 'infosec'
        username = 'infosec_007bot'
        update.message.reply_text('Bot has been reset, send a message!')
        return 
    else:
        update.message.reply_text('I am currently talking to someone else. Can you please wait ' + left + ' seconds?')
        return


def retry(bot, update):
    """Send a message when the command /retry is issued."""
    global chat_log
    global cache
    global qcache
    global tim
    global botname
    global username
    left = str(tim)
    if user == update.message.from_user.id:
        new = True
        comput = threading.Thread(target=wait, args=(bot, update, botname, username, new,))
        comput.start()
        return
    if tim == 1:
        chat_log = None
        cache = None
        qcache = None
        botname = 'Infosec'
        username = 'Infosec_007bot'
        update.message.reply_text('Send a message!')
        return 
    else:
        update.message.reply_text('I am currently talking to someone else. Can you please wait ' + left + ' seconds?')
        return

def runn(bot, update):
    """Send a message when a message is received."""
    new = False
    global botname
    global username
    if "/botname " in update.message.text:
        try:
            string = update.message.text
            charout = string.split("/botname ",1)[1]
            botname = charout
            response = "The bot character name set to: " + botname
            update.message.reply_text(response)
        except Exception as e:
            update.message.reply_text(e)
        return
    if "/username " in update.message.text:
        try:
            string = update.message.text
            userout = string.split("/username ",1)[1]
            username = userout
            response = "Your character name set to: " + username
            update.message.reply_text(response)
        except Exception as e:
            update.message.reply_text(e)
        return
    else:
        comput = threading.Thread(target=interact, args=(bot, update, botname, username, new,))
        comput.start()


def wait(bot, update, botname, username, new):
    global user
    global chat_log
    global cache
    global qcache
    global tim
    global running
    if user == "":
        user = update.message.from_user.id
    if user == update.message.from_user.id:
        tim = timstart
        compute = threading.Thread(target=interact, args=(bot, update, botname, username, new,))
        compute.start()
        if running == False:
            while tim > 1:
                running = True
                time.sleep(1)
                tim = tim - 1
            if running == True:
                chat_log = None
                cache = None
                qcache = None
                user = ""
                username = 'Infosec Bot'
                botname = 'Infosec_007'
                update.message.reply_text('Timer has run down, bot has been reset to defaults.')
                running = False
    else:
        left = str(tim)
        update.message.reply_text('I am currently talking to someone else. Can you please wait ' + left + ' seconds?')
        
        #Main functions#
################
def limit(text, max):
    if (len(text) >= max):
        inv = max * 10
        print("Reducing length of chat history... This can be a bit buggy.")
        nl = text[inv:]
        text = re.search(r'(?<=\n)[\s\S]*', nl).group(0)
        return text
    else:
        return text


def ask(username, botname, question, chat_log=None):
    if chat_log is None:
        chat_log = 'The following is a chat between two users:\n\n'
    now = datetime.now()
    ampm = now.strftime("%I:%M %p")
    t = '[' + ampm + '] '
    prompt = f'{chat_log}{t}{username}: {question}\n{t}{botname}:'
    response = completion.create(
        prompt=prompt, engine="text-curie-001", stop=['\n'], temperature=0.7,
        top_p=1, frequency_penalty=0, presence_penalty=0.6, best_of=3,
        max_tokens=500)
    answer = response.choices[0].text.strip()
    return answer
    # fp = 15 pp= 1 top_p = 1 temp = 0.9

def append_interaction_to_chat_log(username, botname, question, answer, chat_log=None):
    if chat_log is None:
        chat_log = 'The following is a chat between two users:\n\n'
    chat_log = limit(chat_log, max)
    now = datetime.now()
    ampm = now.strftime("%I:%M %p")
    t = '[' + ampm + '] '
    return f'{chat_log}{t}{username}: {question}\n{t}{botname}: {answer}\n'

def interact(bot, update, botname, username, new):
    global chat_log
    global cache
    global qcache
    print("==========START==========")
    tex = update.message.text
    text = str(tex)
    analyzer = SentimentIntensityAnalyzer()
    if new != True:
        vs = analyzer.polarity_scores(text)
        if debug == True:
            print("Sentiment of input:\n")
            print(vs)
        if vs['neg'] > 1:
            update.message.reply_text('Can we talk something else?')
            return
    if new == True:
        if debug == True:
            print("Chat_LOG Cache is...")
            print(cache)
            print("Question Cache is...")
            print(qcache)
        chat_log = cache
        question = qcache
    if new != True:
        question = text
        qcache = question
        cache = chat_log
    #update.message.reply_text('Computing...')
    try:
        answer = ask(username, botname, question, chat_log)
        if debug == True:
            print("Input:\n" + question)
            print("Output:\n" + answer)
            print("====================")
        stripes = answer.encode(encoding=sys.stdout.encoding,errors='ignore')
        decoded = stripes.decode("utf-8")
        out = str(decoded)
        vs = analyzer.polarity_scores(out)
        if debug == True:
            print("Sentiment of output:\n")
            print(vs)
        if vs['neg'] > 1:
            update.message.reply_text('I do not think I could provide you a good answer for this. Use /retry to get positive output.')
            return
        update.message.reply_text(out)
        chat_log = append_interaction_to_chat_log(username, botname, question, answer, chat_log)
        if debug == True:
            #### Print the chat log for debugging
            print('-----PRINTING CHAT LOG-----')
            print(chat_log)
            print('-----END CHAT LOG-----')
    except Exception as e:
            print(e)
            errstr = str(e)
            update.message.reply_text(errstr)


def error(bot, update):
    """Log Errors caused by Updates."""
    logger.warning('Update "%s" caused error "%s"', update)


def main():
    """Start the bot."""

    updater = Updater(tgkey, use_context=False)
    # Get the dispatcher to register handlers
    dp = updater.dispatcher
    # on different commands - answer in Telegram
    dp.add_handler(CommandHandler("start", start))
    dp.add_handler(CommandHandler("help", help))
    dp.add_handler(CommandHandler("reset", reset))
    dp.add_handler(CommandHandler("retry", retry))
    # on noncommand i.e message - echo the message on Telegram
    dp.add_handler(MessageHandler(Filters.text, runn))
    # log all errors
    dp.add_error_handler(error)
    # Start the Bot
    updater.start_polling()
   
    updater.idle()


if __name__ == '__main__':
    main()
