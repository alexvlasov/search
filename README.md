Search
======

Наработки по курсу "Информационный поиск"

-----------------------------------------
1) Задание:
Построить индекс по тексту Л.Н. Толстого "Анна Каренина". Вывести статистику:
 - количество токенов и терминов
 - средняя длина токена и термина


Запуск: ./1.py
Результаты и логи в stats.txt.

------------------------------------------
2) Задание: Реализовать поиск по обратному индексу.
Ввод: булев запрос и (опционально) количество выводимых результатов.
Вывод: список найденных результатов:
    - на отдельной строке номер параграфа и снипет с найденными словами из этого параграфа
    - результаты сортируются по номеру параграфа
    - (*) результаты сортируются по частоте найденных слов запроса в параграфе (самые частотные результаты сверху)


Перед запуском нужно сделать pip install -r requirements.txt
Запуск можно сделать так:
    ./2.py -q 'я & яичница | этот & ядовитый & язык ' -p 1000 -s 1000
или так:
    ./2.py -q 'Анна & любовь | смерть & боль | еда & печенье' -p 100 -s 1000
или любой другой запрос, содержащий слова с & или | (без скобок и отрицания)

Поддерживаемые опции:
 - запрос (-q)
 - количество выводимых параграфов (-p)
 - количество снипеттов в найденном параграфе (в качестве снипеттов - предложение с найденными словами) (-s)
Более подробно -- в хелпе: (./2.py -h)
Результаты выводятся на стандартный вывод в формате:
    Query in [номера всех параграфов, соответствующих запросу]
    номер параграфа:
        предложение с запросом (слова из запроса выделены желтым цветом)

Баги:
1. Код поиска по обратному индексу соединен с построением обратного индекса. Соответственно, при каждом запросе каждый раз строится обратный индекс.
2. В зависимости от запроса, поиск занимает от 0.5 до 1.5 минут.

------------------------------------------
3) Задание: «Извлечение коллокаций».
Задача:
1. Взять корпус текстов Л.Н.Толстого с сайта az.lib.ru (> 1М словоупотреблений).
2. Выбрать 2 метода поиска коллокаций.
3. Получить все биграммы, отсортированные по частотности, рассчитать для
них веса.
4. Оценить точность работы методов

Перед запуском нужно сделать pip install -r requirements.txt
Вся программа разделена на следующие части:
 - Извлекаем текст по урлам, т.е. получаем корпус
 - Токенизируем текст и приводим все токены к нижнему регистру, получаем информацию о каждом токене в виде (токен, часть речи, частота в корпусе). Часть речи для каждого токена определяем с помощью библиотеки pymorph. Эта библиотека требует загрузки словарей. Поэтому при первом запуске этапа мы загружаем требуемые словари в папку morph_dicts и загружаем zip-архив со словарями размером 8 М.
 - Ищем биграммы, отсортировываем их по частоте
 - Ищем коллокации и отсортировываем их по частоте
 - Проверяем результаты коллокаций по критерию Пирсона и отношению правдоподобия
 - Оцениваем точность работы методов, результаты выводятся на стандартный вывод

Для каждого из пунктов получаем 2 файла: *.txt и *.pkl.
 - *.txt - для просмотра в текстовом виде
 - *.pkl - для излечения результатов этапа на любом шаге. Это сделано, чтобы лишний раз не выполнять загрузку словарей и не засорять директорию ненужными файлами. Т.е. чтобы просто запустить алгоритм поиска биграмм и коллокаций по сохраненному тексту и размеченным токенам, нужно посмотреть, есть ли в дериктории tolstoy_results файлы text.pkl & tokens.pkl.
Для выполнения каждого этапа с нуля, можно просто удалить папку tolstoy_results.

Среднее время выполнения для каждого этапа:
0m49.716s - создаем корпус
12m10.779s - скачиваем словари, размечаем текст и сохраняем
0m28.658s - поиск биграмм
0m29.924s - поиск коллокаций
2m1.314s - проверка результата

Описание файлов в папке результатов tolstoy_result:
text.pkl - корпус, string-объект
tokens.pkl - информация о токенах, объект типа collections.Counter {(token, tag), freq}
bigrams.pkl - биграмы, объект типа collections.Counter {(w1, w2), freq}
collocations.pkl - коллокации, объект типа collections.Counter {(w1, w2), freq}
text.txt - текст
tokens.txt - токен, часть речи, частота(вес)
bigrams.txt - биграма, частота
collocations.txt - коллокация, частота
check_res.txt - результаты критериев: коллокация, True - независмые слова (т.е. биграма является коллокацией), False - зависимые слова(т.е. биграма не является коллокацией), l - метод максимального провдоподобия, p - критерий Пирсона.
check_res__my_res.txt - результаты критериев и мои версии коллокаций

Запуск: ./3.py
Результат:
Точность по Критерию Пирсона:  0.62
Точность по методу максимального правдоподобия:  0.645833333333
