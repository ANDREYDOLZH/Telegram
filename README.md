# Telegram
Тут будут располагаться основные кодовые составляющие для работы и создания Телеграм ботов через Phython
import telebot ,wikipedia, re
wikipedia.set_lang("ru")
bot = telebot.TeleBot('5314976701:AAF4BMdSokOeTlcrj6rSytFjPU_tHn3XVQU')  # токен я вставил

@bot.message_handler(commands=['start'])
def start(mess, res=False):
    bot.send_message(mess.chat.id, '<b>Привет, я главный бот, хочешь узнать, что я умею, напиши "/navigator"</b>',parse_mode='html')

@bot.message_handler(commands=['navigator'])
def help(m, res=False):
    bot.send_message(m.chat.id,'Хорошо, что обратился, вот мой основной функционал:\n 1) "/start" - перезапуск меня\n 2) "/learn" - узнай полезную информацию')



# Чистим текст статьи в Wikipedia и ограничиваем его тысячей символов
def getwiki(s):
    try:
        ny = wikipedia.page(s)
        # Получаем первую тысячу символов
        wikitext = ny.content[:1000]
        # Разделяем по точкам
        wikimas = wikitext.split('.')
        # Отбрасываем всЕ после последней точки
        wikimas = wikimas[:-1]
        # Создаем пустую переменную для текста
        wikitext2 = ''
        # Проходимся по строкам, где нет знаков «равно» (то есть все, кроме заголовков)
        for x in wikimas:
            if not ('==' in x):
                # Если в строке осталось больше трех символов, добавляем ее к нашей переменной и возвращаем утерянные при разделении строк точки на место
                if (len((x.strip())) > 3):
                    wikitext2 = wikitext2 + x + '.'
            else:
                break
        # Теперь при помощи регулярных выражений убираем разметку
        wikitext2 = re.sub('\([^()]*\)', '', wikitext2)
        wikitext2 = re.sub('\([^()]*\)', '', wikitext2)
        wikitext2 = re.sub('\{[^\{\}]*\}', '', wikitext2)
        # Возвращаем текстовую строку
        return wikitext2
    # Обрабатываем исключение, которое мог вернуть модуль wikipedia при запросе
    except Exception as e:
        return 'В энциклопедии нет информации об этом'

# Функция, обрабатывающая команду /start
@bot.message_handler(commands=["learn"])
def start(mis, res=False):
    bot.send_message(mis.chat.id, 'Отправьте мне любое слово, и я найду его значение на Wikipedia')
# Получение сообщений от юзера
@bot.message_handler(content_types=["text"])
def handle_text(message):
    bot.send_message(message.chat.id, getwiki(message.text))
    print(message)
#@bot.message_handler(content_types=["text"])
#def repeat_all_messages(message):
    #bot.send_message(message.chat.id, "Приятно познакомиться, "+ message.text)




#@bot.message_handler(content_types=["text"])


bot.polling(none_stop=True, interval=0)
