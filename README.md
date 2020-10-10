# Id_Nobel_Prize_Paper_Titles_Generation
Solution for ODS (ods.ai) Hackaton (3-10.10.2020) Id Nobel Prize Paper Titles Generation

В рамках Хакатона, организованного [ODS](https://ods.ai/) 3-10 октября 2020 было предложено следующее решение к следующей задаче:
реализовать генератор тем [Шнобелевской премии](https://www.improbable.com/).

Программа написана на Python 3.7; данные представлены на английском языке.

В качестве исходных данных использован датасет с названиями научных статей ([STEM-тематика](https://www.kaggle.com/Cornell-University/arxiv)) (S-science (наука), T-technology (технология), Е-engineering (инженерия), М-mathematics (математика)), а также датасет со статьями по медицине ([Kaggle](https://www.kaggle.com/Cornell-University/arxiv)). Поскольку данные представлены в избыточном и сыром виде, проведена предобработка: выделены только названия статей, отфильтрованы аббревиатуры и узко-специализированные сокращения. В результате использовано 1.771.038 статей.

Генератор реализован на основе триграммной модели [источник](https://habr.com/ru/post/88514/). Представленная в статье модель была доработана следующим образом:
* улучшена рандомизация (каждое следующее слово, начиная со второго, выбирается случайным образом)

Идеи по улучшению (пространство для творчества):
* улучшенная рандомизация (подбирается индивидуально под каждую задачу);
* разнообразие данных (модель обучена и работает со STEM-дисциплинами);
* улучшенное быстройдействие (за счет применения более изысканных алгоритмов).

Пример использования основного функционала Dы можете найти в ноутбуке Ig_Nobel_Prize.ipynb

Автор - Александр Мирленко

Весь скрипт логически поделен на 3 основные части:
1. Подготовка данных для составления модели. Сначала из исходного корпуса (названия статей) формируется генератор текста ( функция gen_lines). Затем форимируются генератор токенов (функция gen_tokens) - выдает очищенную полседовательность слов и знаков препинания и генератор триграмм (gen_trigrams) - тройки идущих подряд токенов. 
Символы '$' используются для маркировки начала предложения. В целом, генератор триграмм действует следующим образом: он возвращает три подряд идущих токена, на каждой итерации сдвигаясь на один токен.
2. Составление (обучение) модели (функция train). 

Суть метода заключается в том, что наиболее вероятные слова или знаки препинания последовательно выбираются до тех пор, пока не встречаем признак начала следующей фразы (символа $). 
Модель (model) представляет собой список, в котором для каждой пары слов содержится список пар (слово, вероятность).

Первое слово задается вручную через CLI. В случае, если заданное слово отсутствует в модели, с помощью метода process.extract из библиотеки fuzzywuzzy, основанного на расчете расстояния Левенштейна, подбирается случайное слово из пяти наиболее близких к нему по значению. Каждое последующее слово выбирается случайным образом из модели (функция unirand) на основании предыдущего слова. 

