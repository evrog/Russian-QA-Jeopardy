# Датасет вопросов и ответов викторины "Своя игра"

Файл https://github.com/evrog/Russian-QA-Jeopardy/blob/main/Russian_QA_Jeopardy_dataset_extended.csv , размещенный в данном репозитории, содержит 29 375 вопросов и ответов для викторин формата "Своя игра", собранных из Базы Вопросов Интернет-клуба "Что? Где? Когда?" https://db.chgk.info/ .

## Определения

Вопросно-ответная система (QA-система) - программа, которая отвечает на вопросы так, как это делает человек, на естественном языке.

Датасет - набор данных, организованный по определенному принципу в двумерную таблицу.

Своя игра (en. Jeopardy) - российская телевизионная игра-викторина, транслируемая на телеканале НТВ, производится по лицензии и является одной из локализованных версий американской телевикторины Jeopardy! («Рискуй!»). https://ru.wikipedia.org/wiki/%D0%A1%D0%B2%D0%BE%D1%8F_%D0%B8%D0%B3%D1%80%D0%B0

Спортивное ЧГК - движение фанатов интеллектуальных викторин в стиле телешоу "Что? Где? Когда?", "Своя игра" и др., игровые мероприятия и коллекции вопросов и ответов для викторин.

CSV (comma-separated values) - формат хранения двумерных таблиц (строки и столбцы), при котором внутри строки каждое новое значение отделяется от предыдущего при помощи специального символа (разделителя - delimiter). 

Delimiter (разделитель) - специальный символ, который разделяет значения внутри строки в формате CSV. Чаще всего разделителем выступает запятая (comma): 1 , 2 , 3..

## Лингвистические особенности вопросов

Вопросы "Своей игры" ориентированы, скорее, на припоминание факта, чем на логику, поэтому их называют вопросами на эрудицию. Вопросы короче, чем в других форматах спортивного ЧГК: в среднем 14.28 токенов (98.37 символов) против 34 токена (244.9 символа) во всей базе спортивного ЧГК. Чаще всего вопрос строится в форме утверждения или с использованием повелительного наклонения (например, "Скажите..", "Назовите.."). Глаголы "сказать" и "назвать" и синонимичные им являются одними из самых частых глаголов в вопросах. Довольно часто предмет вопроса написан верхним регистром, например "ЭТОТ тип письма в переводе с греческого..". Вопросы в собранном датасете не требуют опоры (изображения, видео, аудио и т.п.), т.е. полностью вербализованы. 34% слов в ответах это имена собственные; из них 70% - люди. Вопросы легко гуглятся.

## Датасет

Датасет содержит основную информацию о вопросах. Деления на тренировочный и тестовый набор нет. Датасет и закрытый тестовый набор из 512 вопросов, который ранее не публиковался в сети Интернет, помимо этого репозитория, будет доступен в проект Russian SuperGLUE.

Формат данных в .csv таблице:

ID вопроса | Номер вопроса в теме | Текст вопроса | Ответ | Тема | Источник вопроса | Полное имя автора | Название турнира | Ссылка на турнир
--- | --- | --- | --- | --- | --- | --- | --- | --- |
QuestionID | QuestionNumber | QuestionText | Answer | Topic | Source | Author Full Name | TourName | TourLink

Разделитель: табуляция.

## Соревнование QA-систем против игроков

Соревнование между системами автоматического ответа на вопросы "Своей игры" проходит на базе Тюменского государственного университета. Ближайшее мероприятие состоится 17 июля 2022. После каждого соревнования вопросы будут добавлены в тестовый набор на Russian SuperGLUE. Сайт мероприятия https://www.utmn.ru/contin/ 

На соревновании QA-система отвечает на вопросы, соревнуясь с двумя опытными игроками. Проводится два раунда вопросов: более легкий и сложный. Затем игроки и система меняются.

Поскольку мы не тестируем акустические технологии QA-систем, во время соревнования каждой системе нужен только интерфейс для текстовых сообщений. Система находится под контролем оператора. Интерфейс, будь то командная строка или графический пользовательский интерфейс, транслируется на экран позади игроков. Когда открывается новый вопрос и ведущий начинает его читать, менеджер игры отправляет вопрос оператору в текстовом виде. Когда система возвращает ответ, оператор нажимает сигнальную кнопку. Если он сделает это раньше двух других игроков, оператор читает ответ, а ведущий оценивает его как правильный или неправильный. Оператор не имеет права
изменить ответ системы, но может воздержаться от нажатия кнопки. В остальном правила игры совпадают с классической версией. 

Ключевая задача QA-систем — автоматически отвечать на наибольшее количество вопросов, максимально корректно и быстро. Желательно, чтобы системы взвешивали свою уверенность в ответе, чтобы не терять баллы за бессмысленные ответы. Тем не менее на соревнованиях с оператором оператор может воздержаться от нажатия кнопки.

## Базовый алгоритм

В качестве базового алгоритма мы предлагаем модель, обученную на русскоязычном сегменте Википедии, от DeepPavlov https://docs.deeppavlov.ai/en/master/features/skills/odqa.html. При тестировании на 800 вопросах модель дала 16 правильных ответов. Код для запуска модели есть в этом репозитории.

## Оценка QA-системы

Оценка системы складывается из двух этапов: 
1. оценка корректности ответа,
2. подсчет очков.

В качестве первой метрики мы предлагаем коэффициент METEOR в реализации NLTK. У правильных ответов коэффициент METEOR выше 0.227743. Перед сравнением ответов рекомендуем предобработать результат и ответ из датасета.

Вторая метрика аналогична подсчету очков на игре: при правильном ответе вес вопроса в теме (категории) добавляется к счету системы, при неправильном минусуется. Если система может воздержаться от ответа, например, оценив свой лучший результат как маловероятный, то счет не меняется.

## Лицензия

Вопросы, ответы и темы, размещенные в датасете, распространяются по лицензии Базы Вопросов Интернет-клуба "Что? Где? Когда?" https://db.chgk.info/copyright . Результаты собственной разработки авторов: компьютерные программы и текстовые файлы, не содержащие вопросы, а также метод структурирования информации в датасете, созданные разработчиками, распространяются по лицензии Creative Commons «Attribution-ShareAlike» («Атрибуция-СохранениеУсловий») 4.0 Всемирная https://creativecommons.org/licenses/by-sa/4.0/ .

## Цитирование

На данный момент доступен препринт Mikhalkova, Elena. "A Russian Jeopardy! Data Set for Question-Answering Systems." arXiv preprint arXiv:2112.02325 (2021). Статья принята к публикации в материалах LREC 2022.

# Russian QA Jeopardy! Dataset

File https://github.com/evrog/Russian-QA-Jeopardy/blob/main/Russian_QA_Jeopardy_dataset_extended.csv , hosted in this repository, contains 29,375 questions and answers for game quizzes, collected from the Internet Quiz Club "What? Where? When?" https://db.chgk.info/ .

## Definitions

Question-answering system (QA-system) - a program that answers questions the way a person does, in a natural language.

A dataset - a set of data organized according to a certain principle into a two-dimensional table.

Own Game - a Russian TV quiz show broadcast on the NTV channel and is one of the localized versions of the American TV quiz game Jeopardy! ("Take a risk!"). https://ru.wikipedia.org/wiki/%D0%A1%D0%B2%D0%BE%D1%8F_%D0%B8%D0%B3%D1%80%D0%B0

Sports ChGK - a movement of fans of intellectual quizzes in the style of the TV shows "What? Where? When?", "Own Game", etc. Also, quiz cups and collections of questions and answers for quizzes.

CSV (comma-separated values) is a format for storing two-dimensional tables (rows and columns), in which each new value inside a row is separated from the previous one using a special character (delimiter).

Delimiter - a special character that separates values within a string in the CSV format. The most common separator is a comma: 1, 2, 3..

## Linguistic features of questions

Questions of "Own game" are focused more on remembering a fact than on logic, therefore they are said to test erudition. The questions are shorter than in other formats of the Sports ChGK: an average of 14.28 tokens (98.37 characters) versus 34 tokens (244.9 characters) in the entire Sports ChGK database. Most often, the question is built in the form of a statement or using the imperative mood (for example, "Tell ..", "Name .."). The verbs "name" and "say" and their synonyms are among the most frequent verbs in questions. Quite often, the object of the question is written in upper case, for example "THIS genre is translated from Greek..". Questions in the collected dataset do not require support like images, video, audio, etc., i.e. they are fully verbalized. 34% of tokens in the answers are proper names; 70% of them are people. Questions are easy to find via Google.

## Dataset

The dataset contains basic information about questions. There is no division into training, developer and test sets. The dataset and a closed test set of 512 questions, which has not previously been published on the Internet, in addition to this repository, will be available in the Russian SuperGLUE project.

Data format in .csv table:

Question ID | Number of a question in the topic | Text of a question | Answer | Topic (category) | Source of information for a question | Full name of the author | Title of a tournament | Link to a tournament
--- | --- | --- | --- | --- | --- | --- | --- | --- |
QuestionID | QuestionNumber | QuestionText | Answer | Topic | Source | AuthorFullName | TourName | TourLink

Separator: tab (\t).

## Challenge: QA-systems against players

The competition between automatic QA-systems and experiences Jeopardy! players takes place at the Tyumen State University. The next event will be held on July 17, 2022. After each competition, questions will be added to the Russian SuperGLUE test set. Event website https://www.utmn.ru/contin/

In the competition, a QA system answers questions by competing with two experienced players. There are two rounds of questions: easier and more difficult. Then the players and the system change.

As we don't test acoustic QA systems, each system only needs a text messaging interface during competition. The system is under the control of the operator. The interface, whether it is a command line or a graphical user interface, is broadcast on a screen behind the players. When a new question is opened and the host starts reading it, the game manager sends the question to the operator in the text form. When the system returns a response, the operator presses the signalling button. If he or she does it before the other two players, the operator reads the answer, and the host evaluates it as correct or incorrect. The operator is not authorized to change the system's response, but may refrain from pressing the button. The rest of the game rules are the same as in the classic version.

The key task of QA systems is to automatically answer the largest number of questions, as correctly and quickly as possible. It is desirable that systems weigh their confidence so as not to lose points for meaningless answers. However, in competitions with an operator, the operator may abstain from pressing the button.

## Baseline algorithm

As a baseline algorithm, we suggest a model trained on the Russian-language segment of Wikipedia, by DeepPavlov https://docs.deeppavlov.ai/en/master/features/skills/odqa.html. When tested on 800 questions, the model gave 16 correct answers. The code to run the model is in this repository.

## QA system evaluation

System evaluation consists of two stages:
1. assessment of the correctness of the answer,
2. scoring.

As the first metric, we propose the METEOR coefficient in the NLTK implementation. Correct answers have METEOR coefficient higher than 0.227743. Before comparing the answers, we recommend preprocessing the result and the answer from the dataset.

The second metric is similar to scoring in the classic game: if the answer is correct, the weight of the question in the topic (category) is added to the system's score; if it is incorrect, it is subtracted. If the system can abstain from answering, for example, by self-evaluating its best result as incorrect, then the score does not change.

## License

Questions, answers and topics posted in the dataset are distributed under the license of the Spots ChGK Internet Club "What? Where? When?" https://db.chgk.info/copyright. The results of the authors' own development: computer programs and text files that do not contain questions, as well as our method for structuring information in the dataset are distributed under the Creative Commons Attribution-ShareAlike 4.0 Worldwide license https://creativecommons .org/licenses/by-sa/4.0/ .
