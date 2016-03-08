# Регистрация в heroku

1. Идём в http://heroku.com/, регистрируемся.
- Идём по гайду https://devcenter.heroku.com/articles/getting-started-with-python#introduction
- Устанавливаем себе на пекарню утилки от heroku.

# Конфиги и переменные

Используя heroku, конфиги стоит хранить в окружении. Для этого:

1. Задаём параметры командой:

 heroku config:set variable="value"

 не забываем кавычки, по крайней мере в случае переменной template.
- В python получаем доступ к ним с помощью os.environ.get()

# Шаблонный бот

    git clone git@github.com:strizhechenko/twitterbot_example.git

или

    git clone https://github.com/strizhechenko/twitterbot_example.git

В этой репе лежат:

- Procfile - объясняет какой командой запускать бота;
- runtime - какая среда (читай версия python) нужна для запуска бота;
- requirements.txt - все зависимости бота;
- bot.py - набросок бота;
- morpher.py - модуль для нормализации русскоязычных существительных.

## Конфиг

Я обычно использую в качестве словаря бота - не какой-то файл, базу или что-то ещё, а выдернутые из моей собственной ленты существительные. Для этого нужна авторизация под несколькими пользователями - самого бота (**user\_access\_token/secret**) и reader'а (в данном случае - **strizhechenko\_access\_token/secret**).

    $ heroku config -s
    consumer_key=
    consumer_secret=
    reader_name=strizhechenko
    strizhechenko_access_secret=
    strizhechenko_access_token=
    template='%s – признак любви.'
    timeout=30
    user_access_secret=
    user_access_token=

## Регистрация приложения

**consumer key** / **consumer secret** - можно получить зарегистрировав приложение здесь - https://apps.twitter.com/. По сути это идентификаторы вашего приложения, лучше его никому не знать. Я к примеру для всех ботов использую один идентификатор.

## Авторизация пользователей
Авторизовать пользователей можно следующим образом:

    virtualenv env/
    . env/bin/activate
    pip install -r requirements.txt 
    export consumer_key=your_consumer_key
    export consumer_secret=your_consumer_secret
    python -m twitterbot_utils.TwiAuth

Утилита спросит PIN код, который будет показан в браузере, а затем отдаст **access token / secret**. В верхней части экрана возможно придётся перелогиниться на другого пользователя, т.к. авторизовать надо и себя и бота.

Осталось два парметра  template и timeout:

    template='%s – признак любви.'

**template** - шаблон фразы, в которую нужно подставлять существительные. Как printf почти.

    timeout=30

**timeout** - с каким промежутком постить, задаётся в минутах.

Есть ещё две необязательные опции:

    tweet_grab = 3
    tweets_per_tick = 2

**tweet\_grab** - число твитов, которые подтягиваются из ленты reader'а для выхватывания существительных.

**tweets\_per\_tick** - число твитов, которые бот постит за один тик (т.е. раз в timeout).

# Заливка в heroku

Итак мы всё настроили, возможно слегка поправили код bot.py. Закомитьтесь, если что-то меняли (и блудили):

    git add -p
    git commit -m "блаблабла"

Меняем origin, репозиторий-пример с гитхаба нам больше не нужен.

    git remote remove origin
    heroku create yourbotname
    git push heroku master

После git push heroku должен обнарудить питоновое приложение и собрать его из requirements.txt:

    remote: -----> Python app detected

Закончиться всё должно:

    remote: Verifying deploy... done.

# Запускаем бота

    heroku scale worker=1

Дальше уже остаётся любоваться логами и чинить если что-то не работает:

    heroku logs -t

Рестарт бота:

    heroku scale worker=0
    heroku scale worker=1
