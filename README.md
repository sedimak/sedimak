from pyowm import OWM
from pyowm.utils.config import get_default_config
from telebot import TeleBot

owm = OWM('d20577dcc7a8e0f6c7d3a93f96d6c520')
bot = TeleBot("1809394431:AAHuC0fKjCAeG-0726fTymfdmujg3dqNjOg")
@bot.message_handler(commands=['start'])
def send_welcome(message):
  bot.reply_to(message, "Вас вітає бот для погоди s'ul 1.0\n"+"введіть місце=>")

@bot.message_handler(content_types=['text']) 
def get_text_messages(message):
    config_dict = get_default_config()
    config_dict['language'] = 'ua'

    mgr = owm.weather_manager()
    city = get_text_messages(message)

    all_ok = False
    while not all_ok:
        try:
            observation = mgr.weather_at_place(city)
        except pyowm.commons.exceptions.NotFoundError:
            answer =("Вы неверно ввели город, введите КОРРЕКТНОЕ название: ")
            city = get_text_messages(message)
        else:
            w = observation.weather
            all_ok = True

    temp =w.temperature('celsius')["temp"] 
    wh= w.detailed_status
    wt = w.wind()['speed']
    
    answer ="температура у місті->"+str(temp)
    bot.send_message(message.chat.id,answer)
    
    answer ="статус->"+wh
    bot.send_message(message.chat.id,answer)
    
    answer ="вітер->"+str(wt)
    bot.send_message(message.chat.id,answer)
    
    bot.send_message(message.chat.id,"введіть місце=>")
bot.polling(none_stop = True)
