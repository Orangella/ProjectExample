В репозитории на данный момент находится один пример законченной программы на Python, где можно ознакомиться с кодом (игра Arkanoid). 

Также мной создан бот на Python 3 (графическая часть реализована на PyQT5 с использованием дизайнера), который может делать конкурсы вида "сделай репост - получи приз" в социальной сети "В Контакте" (используя официальное API), на данный момент в базе накоплено достаточно статистики для полностью автономной работы. 
Ниже приведено его описание и пример функции, код в открытый доступ не выложен по объективным причинам. 

### Основные модули: 
- утилита для поиска конкурсов, удовлетворяющих заданным условиям 
- утилита для репоста конкурсов, которые будут разыграны в конкретную дату (репост с учетом особых требований организаторов, если такие есть) 
- утилита для проверки итогов конкурсов 

### Вспомогательные модули: 
- утилита для удаления прошедших конкурсов 
- утилиты для просмотра конкурсов из базы (используется sqlite) по заданным параметрам, мелкие утилиты для работы с базой 
- утилита для выхода из ненужных групп (так как ВК есть некоторые проблемы в случае, если человек вступил в число групп, превышающее ~1100) 
- утилита для добавления правил по итогам статистики 

### Утилита для поиска конкурсов 
![search](https://orangella.github.io/ProjectExample/search.png) 
Возможности: 
1. Поиск новых записей с конкурсами
2. Проверка каждой записи и принятие решения о том, конкурс ли эта запись и является ли он нужным (на основе таблиц правил (в том числе правила на особые условия), таблиц "хороших" и "плохих" групп, таблиц "хороших" и "плохих" фраз и т.п; такие таблицы формируются оператором в процессе работы с утилитой). 
3. Определение даты/дат (если несколько) розыгрыша (включая случаи написания слов "сегодня", "с 12 по 18 июня" и т.п.). Определение, нужно ли делать репост, или по условиям достаточно лайка и вступления в группу. 
4. Записи, которые не удается классифицировать (таких **остается около 3% от общего числа**) выводятся для принятия решения оператором, ключевые сведения выделяются, возможен просмотр прикрепленных к записи картинок 
5. Дополнительно для принятия решения выводятся все записи, которые были классифицированы как конкурсы с особыми условиями (написать порядковый номер, держать не ниже какого-то места, проголосовать и т.п) 

### Утилита для репоста конкурсов 
Возможности: 
1. Поиск в тесте всех ссылок на группы и людей и вступление/добавление в друзья. Если в тексте слишком много ссылок людей, выводится предупреждение (например, может случайно попасться запись с объявлением итогов конкурса и ссылками на 50 человек) 
2. Выполнение всех особых условий (в том числе ставит порядковый номер в комментарии и проверяет корректность, в случае проблем выводит предупреждение). В случае, если есть, к примеру, пять конкурсов с условием "разместить не ниже третьего места" - сразу после запуска выводится предложение расставить приоритеты. Учет записей, которые требуют размещения "не ниже" на текущую дату (если, например, запущены конкурсы с датой розыгрыша на следующий день). 

### Утилита для просмотра итогов 
![results](https://orangella.github.io/ProjectExample//results.png) 
Возможности: 
1. Поиск записей с ключевыми словами от администрации группы ("итог", "победител" и т.п.) для конкурсов, которые должны были быть разыграны на текущую дату. 
2. Вывод найденных итогов, выделение ключевых сведений (имена людей, адрес текущего конкурса). 
3. Если конкурс перенесен - вывод предложения изменить дату. Если несколько дат розыгрыша - это учитывается, конкурс не помещается на удаление 
4. Автоматический автономный режим, в котором происходит поиск только ссылок и имен людей, использующих данную программу 

Во всех утилитах адреса записей/групп, которые выводятся для принятия решения, автоматически помещаются в буфер обмена (чтобы можно было посмотреть в браузере при необходимости) 

База данных ![bd](https://orangella.github.io/ProjectExample/bd.png) 

Пример: функция, производящая в тексте поиск 
- фраз вида `[id123456|Павел Иванов]`
- адреса текущего конкурса 

```
def persons_and_wall_positions(text): 
    wall = str(results[current_result][RFROM_ID]) + '_' + 
            str(results[current_result][RID]) 
    result = [] 
    text = text.lower() 
    search_str = '\\[id[\d]*\\|([\w\s\\-(\)]*)\\]' 
    reg = re.compile(search_str) 
    for n in re.finditer(reg, text): 
       result.append([n.start(1), n.end(1)]) 
    reg = re.compile(wall) 
    for n in re.finditer(reg, text): 
       result.append([n.regs[0][0], n.regs[0][1]]) 
    result.sort(key=lambda elem: int(elem[0])) 
    return result
  ```
