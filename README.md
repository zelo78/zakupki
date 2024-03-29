# Zakupki

Программа для сбора информации с сайта [zakupki.gov.ru](https://zakupki.gov.ru)

## Использование

Программа создаёт файл `zakupki.xlsx`. Он используется программой для:

- ведения лога запусков (вкладка `log`)
- сохранения статуса выполненных/невыполненных задач (вкладка `jobs`). При запуске без параметров командной строки программа будет пытаться выполнить незавершенные задачи
- сохранят в него найденную информацию - данные о торгах (вкладка `lots`)
- получает из него информацию от пользователя, по каким торгам пользователь желает получить дополнительную информацию с сайта

В этом файле можно создавать другие вкладки, они не помешают работе программы.

Сбор данных проходит в три этапа.

- На первом этапе программа получает от пользователя в командной строке ИНН организации и годы, за которые надо собирать данные. 
Она загружает данные обо всех торгах этого заказчика (кроме исключенных из-за наличия слов из черного списка - это позволяет отсечь заведомо не нужные лоты).
Пример запуска: `python zakupki.py -i 2311040088 -y 2020 2021`. Это поиск всех торгов для организации с ИНН 2311040088 за период 2020-2021 годы.
- Сбор всех лотов по ИНН и годам можно повторять многократно, даже если уже выполнялись запуски программы по второму и третьему этапам сбора данных
- Если были ошибки (сайт <https://zakupki.gov.ru> не ответил на какие-то запросы) - программу надо запустить вновь, без параметров (режим завершения отложенных заданий). На вкладке `jobs` файла `zakupki.xlsx` у выполненных заданий стоит статус `done`, если у какого-то задания стоит статус `error`, надо запустить программу ещё раз. 
- После этого файл `zakupki.xlsx` надо открыть в редакторе (например, в Excel); в нём можно удалить строки с ненужными лотами, упорядочивать их как угодно. Это позволяет удалить заведомо неинтересные лоты, с учетом специфики данного клиента (как у него принято называть торги с интересующим нас товаром)
- Запускаем программу снова, но уже для второго этапа сбора данных: `zakupki.py 2`. Программа соберет дополнительную информацию по всем неудаленным лотам, и также добавит её в файл `zakupki.xlsx`. В случае успешного сбора информации по конкретному лоту, в колонке `stage2` появится статус `done`. При ошибках сбора данных (не все лоты перешли в данный статус) - программу также можно запустить повторно, с тем же параметром `2`.
- Файл `zakupki.xlsx` снова открываем в редакторе, анализируем информацию, у тех лотов, по которым надо скачать всю документацию - ставим в колонке `stage3` значение `yes`
- Запускаем программу снова, но уже для третьего этапа сбора данных: `zakupki.py 3`. По всем помеченным лотам, программа загрузит все файлы, относящиеся к лоту, и разместит их в отдельной папке. 

## Использованные библиотеки:

- [openpyxl](https://openpyxl.readthedocs.io/en/stable/) - Чтение/запись фалов Excel 2010 xlsx/xlsm
- [Beautiful Soup](https://pypi.org/project/beautifulsoup4/) - Парсинг HTML и XML документов
- [requests](https://docs.python-requests.org/en/latest/index.html) - Выполнение HTTP-запросов
- [argparse](https://docs.python.org/3/library/argparse.html) - Парсер опций и аргументов командной строки
- [flake8](https://flake8.pycqa.org/en/latest/) - Статическая проверка кода
