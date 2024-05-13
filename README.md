https://github.com/DoctorLines/translate_build_api/blob/master/13.api_versioning.md 

### [Подход №1: URL](https://github.com/DoctorLines/translate_build_api/blob/master/13.api_versioning.md#%D0%BF%D0%BE%D0%B4%D1%85%D0%BE%D0%B4-1-url)

Указывание номера версии в URL является очень распространенной практикой среди популярных публичных API.

По сути, вы просто добавляете `v1` или `1` в URL, потому указать следующую версию не составит труда.

> `https://api.example.com/v1/places`

Такой подход довольно часто выбирается API разработчиками в своих проектах благодаря тому, что он имеет большую распространенность среди публичных API. Он достаточно прост и эффективен.

### [Подход №2: Имя хоста](https://github.com/DoctorLines/translate_build_api/blob/master/13.api_versioning.md#%D0%BF%D0%BE%D0%B4%D1%85%D0%BE%D0%B4-2-%D0%B8%D0%BC%D1%8F-%D1%85%D0%BE%D1%81%D1%82%D0%B0)

Некоторые разработчики API в попытке избежать проблем с установкой сервера, где указывается версия в URI, просто указывают номер версии в имени хоста (или поддомена).

> `https://api-v1.example.com/places`

На самом деле, это не решает некоторые другие проблемы. Имея версию в URL в целом (URI или поддомене), перенимаются все те же проблемы для пользователей API, но по крайней мере уменьшаются шансы, что разработчики API будут использовать единую кодовую базу.

#### [Плюсы](https://github.com/DoctorLines/translate_build_api/blob/master/13.api_versioning.md#%D0%BF%D0%BB%D1%8E%D1%81%D1%8B-1)

- Невероятно прост для API разработчиков
- Невероятно прост для API пользователей
- Удобные для URLы для копипаста
- Простое использование DNS для распределения версий на несколько серверов.

#### [Минусы](https://github.com/DoctorLines/translate_build_api/blob/master/13.api_versioning.md#%D0%BC%D0%B8%D0%BD%D1%83%D1%81%D1%8B-1)

- Технически не является RESTful
- Принуждает пользователей API прибегать к сложным и непонятным действиям для поддержания ссылок (links) в актуальном состоянии.

### [Подход №3.1: Тело и параметры запроса](https://github.com/DoctorLines/translate_build_api/blob/master/13.api_versioning.md#%D0%BF%D0%BE%D0%B4%D1%85%D0%BE%D0%B4-31-%D1%82%D0%B5%D0%BB%D0%BE-%D0%B8-%D0%BF%D0%B0%D1%80%D0%B0%D0%BC%D0%B5%D1%82%D1%80%D1%8B-%D0%B7%D0%B0%D0%BF%D1%80%D0%BE%D1%81%D0%B0)

Если вы решили уйти от размещения версии в URI, тогда одно из двух других мест, где её можно указать, это само тело HTTP запроса.

```
POST /places HTTP/1.1
Host: api.example.com
Content-Type: application/json
{
    "version": "1.0"
}
```

Это решает проблему с изменением URL в будущем, но может привести к противоречивому опыту. Если разработчик API постит JSON или похожую структуру данных, тогда это просто, но если ему нужно установить `Content-Type` как `image/png` или даже `text/csv`, то довольно быстро возникают сложности.

Некоторые предполагают, что решение этой проблемы в том, чтобы перемести параметр версии в строку запроса, но тогда версия снова попадает в URL! И тогда сразу многие проблемы первых двух подходов возвращаются.

### [Подход №4: Распределение контента](https://github.com/DoctorLines/translate_build_api/blob/master/13.api_versioning.md#%D0%BF%D0%BE%D0%B4%D1%85%D0%BE%D0%B4-4-%D1%80%D0%B0%D1%81%D0%BF%D1%80%D0%B5%D0%B4%D0%B5%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5-%D0%BA%D0%BE%D0%BD%D1%82%D0%B5%D0%BD%D1%82%D0%B0)

Заголовок `Accept` спроектирован для того, чтобы попросить сервер выдать ответ для определенного ресурса в определенном формате. Традиционно, многие разработчики задумываются о нем только в случае (X)HTML, JSON, изображения и т.д., но область применения такого заголовка более обширна. Если мы можем спокойно попросить выдать нам данные с контентом в соответствующем формате, имеющий соответствующий синтаксис, тогда почему бы не воспользоваться этим же заголовком и для указания версии тоже?

GitHub до сих пор следует советам многих людей, упомянутых в этой главе, и используют заголовок `Accept` для отдачи различных типов данных.

> Все типы данных GitHub выглядят примерно так:
> 
> `application/vnd.github[.version].param[+json]`
> 
> Самые базовые типы, которые поддерживает API:
> 
> `application/json`  
> `application/vnd.github+json`
> 
> **Источник:** [GitHub, “Media Types”](https://developer.github.com/v3/media/#api-v3-media-type-and-the-future)

#### [Плюсы](https://github.com/DoctorLines/translate_build_api/blob/master/13.api_versioning.md#%D0%BF%D0%BB%D1%8E%D1%81%D1%8B-4)

- Прост для пользователей API (если они знают про заголовки)
- URL остается таким же
- HATEOAS-совместимый
- Кеш-совместимый
- Sturgeon-approved (нуу.. наверно "осетр одобряе")

#### [Минусы](https://github.com/DoctorLines/translate_build_api/blob/master/13.api_versioning.md#%D0%BC%D0%B8%D0%BD%D1%83%D1%81%D1%8B-4)

- Разработчики API могут запутаться (если они не знают про заголовки)
- Версионирование целиком всего API может запутать пользователей (но и все другие походы имеют туже проблему)

GIT
В отличие от художников, IT-команды не используют скетчбуки. Историю их проектов хранит отдельная программа — **система контроля версий** (англ. _**V**ersion **C**ontrol **S**ystem,_ или коротко _**VCS**_) или и **SCM** (от англ. _**S**ource **C**ontrol **M**anagement_ — «система управления исходным кодом»).
Система контроля версий, или VCS, — это программное обеспечение, которое помогает отслеживать изменения в программах, текстовых файлах, больших документах, веб-сайтах и так далее.

Одно изменение или группу изменений в VCS называют **ревизией** или **версией**. Каждая такая ревизия содержит информацию о том, что изменилось, кто внёс изменения, когда это было и иногда комментарии к изменению.
Система контроля версий — общее название ряда продуктов, таких как Git, Mercurial, Subversion и других
Командную строку часто называют **терминалом** или **консолью**.
У программ есть привычный **графический интерфейс** (англ. _**G**raphical **U**ser **I**nterface_, или _GUI_). Это окна, значки, кнопки, выпадающее меню с настройками и прочие элементы.

**Командная строка** (англ. _**C**ommand-**l**ine **I**nterface_, или _CLI_) — тоже интерфейс, только текстовый.

- символ доллара (`$`) — он означает, что программа ждёт ваших команд.
В командной строке вы тоже всегда находитесь в какой-то папке — просто этого не видно. Узнать, где вы сейчас, поможет команда ==`pwd`== (от англ. _**p**rint **w**orking **d**irectory_ — «показать рабочую папку»). Она выводит путь к текущей директории.
Например, для Windows путь начинается с буквы диска C — `/c/` или `c:/`. В Linux нет букв дисков, а домашняя директория находится в `/home` вместо `/Users`.

С помощью терминала вы всегда можете перейти к домашней директории. Для этого нужно ввести команду ==`cd`== (от англ. _**c**hange **d**irectory_ — «сменить директорию») и символ `~` — обозначение домашней директории.

Вывести содержимое директории — ==`ls`==
Сменить директорию — ==`cd`== `cd имя_папки`
Обратите внимание: если в названии папки есть пробелы, при вводе нужно использовать кавычки.

```
$ cd "Фотографии с дня рождения" 
```
Чтобы вернуться в **родительскую директорию** — то есть на уровень выше, — вместо названия папки нужно написать две точки: `..`
Скрипт - небольшая программа для выполнения задачи

Также `cd` позволяет перемещаться сразу через несколько директорий. Для этого нужно разделить их названия знаком `/`.

```
$ pwd
/projects # сейчас мы здесь

$ cd github/open-source-project # переходим через несколько директорий

$ pwd
/projects/github/open-source-project 
```

$ ls # вывели список файлов file.txt photo.png 
==$ ls -a== # вывели список, в котором отображаются скрытые файлы ., .. и .git . .. .git file.txt photo.png

А ещё, как и другие команды, `ls` может работать с символом домашней директории (`~`) и предыдущей директории (`..`). Например, `ls ~` выведет содержимое домашней директории вне зависимости от того, что показывает `pwd`. А `ls ..` покажет содержимое родительской директории.

Создание файлов и директорий — ==`touch`, `mkdir`==

Чтобы создать файл, нужно ввести в консоль команду `touch` (англ. «коснуться») с именем файла в качестве параметра: `touch %ИМЯ_ФАЙЛА%`.

```
$ touch my-new-file.txt # создали файл my-new-file.txt 
$ mkdir new-dir # создали директорию new-dir
```
Можно создать целую структуру директорий одной командой с помощью флага `-p`.

```
$ mkdir -p dir1/dir-inside/dir-deeper-inside
# создали папку dir-deeper-inside в папке dir-inside, которая находится в папке dir1 
```
Также можно использовать обе команды вместе с символом домашней директории (`~`) или родительской директории (`..`). Например, команда `mkdir ~/my-git-projects` создаст папку `my-git-projects` внутри домашней директории.

А команда `touch ../../file.txt` создаст файл `file.txt` на две папки выше по иерархии. Допустим, если вы находитесь в директории `projects/git/hello`, команда `touch ../../file.txt` создаст файл по такому пути: `projects/file.txt`.

Копирование файлов — ==`cp`==

Вы наверняка копировали файлы на своём компьютере через графический интерфейс — диспетчер файлов на Windows или Finder на macOS. Посмотрим, как это делать через консоль.

Для копирования файлов через терминал существует команда `cp` (от англ. _**c**o**p**y_ — «копировать»). В простом виде `cp` принимает два параметра: `что копируем` и `куда копируем`.

```
$ cp что_копируем куда_копируем

$ cp index.html src/
# скопировали index.html в папку src 
```

Но можно указать сразу несколько файлов.

```
$ cp что_копируем что_копируем что_копируем куда_копируем

$ cp index.html style.css script.js src/
# скопировали три файла (index.html, style.css и script.js) в папку src 
```

Перемещение файлов и папок — ==`mv`==

Копирование создаёт копию файла или папки. Но иногда вместо копии нужно удалить файл в одном месте и создать в другом. Для этого есть команда `mv` (от англ. _**m**o**v**e_ — «переместить»).

Синтаксис команды `mv` аналогичен синтаксису `cp`. После имени команды указывают список файлов и папок, которые нужно переместить, а затем — папку, в которую нужно выполнить перемещение.

Чтение файлов — ==`cat`==

Чтобы прочитать файл, в консоль нужно ввести `cat` (от англ. c_on**cat**enate and print_ — «объединить и распечатать») вместе с именем файла. Команда распечатает то, что содержится в нём.

```
$ cat myfile.txt
```
Команда `cat` работает только с текстовыми файлами. Вывести этой командой файл другого типа (например, изображение) не получится.

Удаление файлов и папок — ==`rm`, `rmdir`, `rm -r`==

Чтобы удалить файл, нужно напечатать команду `rm` (от англ. _**r**e**m**ove —_ «удалить») и передать ей имя файла.

```
$ rm example.txt # удалили файл example.txt из текущей папки 
```
Удалить папку можно командой `rmdir` (от англ. _**r**e**m**ove **dir**ectory —_ «удалить директорию»). Не забудьте указать имя папки.
Это защита от случайного удаления нужных файлов. Если папку всё-таки нужно удалить вместе со всем её содержимым, можно использовать команду `rm` так.

```
$ rm -r images
```
**рекурсивно** удаляет файлы и папки. Это значит, что удаление будет последовательно применяться к каждому из элементов в этой папке — пока не сотрёт их все. Затем команда удалит пустую директорию.

### Выполняйте сразу несколько команд

Команды в терминале необязательно вбивать и выполнять по очереди. Их можно указывать не по одной, а сразу списком. Для этого их нужно разделить двумя амперсандами (`&&`).

```
$ mkdir second-project && cd second-project && touch index.html style.css
# создаём папку second-project,
# переходим в папку second-project
# и создаём в ней два файла: index.html и style.css 
```
Необязательно заучивать все команды наизусть. Если нужно найти какую-нибудь из них, достаточно вспомнить, с каких букв она начинается. Можно набрать их в командной строке и дважды нажать клавишу `Tab`. Терминал покажет список всех команд, которые начинаются с этих символов.

`Tab` автоматически дописывает не только команды, но и пути. Начните печатать имя папки или файла (они должны быть в той же директории) и нажмите `Tab`. Терминал заполнит имя автоматически.

Все глобальные настройки Git хранит в файле `.gitconfig` в домашней директории. Команда запишет в этот файл указанные имя и почту. Чтобы убедиться в этом, можно вызвать команду для чтения файлов.

```
$ cat ~/.gitconfig
```

Другой способ проверки — вывести содержимое файла конфигурации Git той же командой `git config` с флагом `--list` (англ. «список»).


```
$ git config --list 
```

### Навигация

- `pwd` (от англ. _**p**rint **w**orking **d**irectory_, «показать рабочую папку») — покажи, в какой я папке;
- `ls` (от англ. _**l**i**s**t directory contents_, «отобразить содержимое директории») — покажи файлы и папки в текущей папке;
- `ls -a` — покажи также скрытые файлы и папки, названия которых начинаются с символа `.`;
- `cd first-project` (от англ. _**c**hange **d**irectory_, «сменить директорию») — перейди в папку `first-project`;
- `cd first-project/html` — перейди в папку `html`, которая находится в папке `first-project`;
- `cd ..` — перейди на уровень выше, в родительскую папку;
- `cd ~` — перейди в домашнюю директорию (`/Users/Username`);
- `cd /` — перейди в корневую директорию.

### Работа с файлами и папками

**Создание**

- `touch index.html` (англ. _touch,_ «коснуться») — создай файл `index.html` в текущей папке;
- `touch index.html style.css script.js` — если нужно создать сразу несколько файлов, можно напечатать их имена в одну строку через пробел;
- `mkdir second-project` (от англ. _**m**a**k**e **dir**ectory_, «создать директорию») — создай папку с именем `second-project` в текущей папке.

**Копирование и перемещение**

- `cp file.txt ~/my-dir` (от англ. _**c**o**p**y_, «копировать») — скопируй файл в другое место;
- `mv file.txt ~/my-dir` (от англ. _**m**o**v**e_, «переместить») — перемести файл или папку в другое место.

**Чтение**

- `cat file.txt` (от англ. _con**cat**enate and print_, «объединить и распечатать») — распечатай содержимое текстового файла `file.txt`.

**Удаление**

- `rm about.html` (от англ. _**r**e**m**ove_, «удалить») — удали файл `about.html`;
- `rmdir images` (от англ. _**r**e**m**ove **dir**ectory_, «удалить директорию») — удали папку `images`;
- `rm -r second-project` (от англ. _**r**e**m**ove,_ «удалить» + _**r**ecursive_, «рекурсивный») — удали папку `second-project` и всё, что она содержит.

### Сделать папку репозиторием — ==`git init`==

Чтобы Git начал отслеживать изменения в проекте, папку с файлами этого проекта нужно сделать **Git-репозиторием** (от англ. _repository_ — «хранилище»). Для этого следует переместиться в неё и ввести команду `git init` (от англ. _**init**ialize_ — «инициализировать»).

Например, создайте папку `first-project` и сделайте её Git-репозиторием: перейдите в неё с помощью команды `cd` и выполните `git init`.


```
$ cd ~/dev/first-project # перешли в нужную папку

$ git init # создали репозиторий 
```
«Разгитить» папку, если что-то пошло не так, — ==`rm -rf .git`==

Если вы случайно сделали Git-репозиторием не ту папку, её можно «разгитить». Для этого нужно удалить скрытую подпапку `.git`.

```
$ cd <папка с репозиторием> # перешли в папку

$ rm -rf .git # удалили подпапку .git 
```
Проверить состояние репозитория — ==`git status`==

После инициализации репозитория запустите команду `git status` (от англ. _status_ — «статус», «состояние») — она показывает текущее состояние репозитория.

Команда `git status` выведет:

- название текущей ветки: `On branch master` или `On branch main`;
- сообщение о том, что в репозитории ещё нет коммитов: `No commits yet`;
- сообщение, которое говорит: «чтобы что-нибудь закоммитить (то есть зафиксировать), нужно сначала это создать» — `nothing to commit (create/copy files and use "git add" to track)`.
Состояние `untracked` значит, что Git ещё не хранит информацию о версиях файла и не может отследить, как он изменялся.


```
$ git add --all # подготовили к сохранению все файлы в репозитории
или 
$ git add . # добавить всю текущую папку
$ git status # проверили статус 
```

Добавлять файлы можно и по одному, без ключа `--all`.

```
$ git add todo.txt
$ git add readme.txt
$ git status 
```

💡 **Чем отличается запоминание от сохранения?**

Команда `git add` не сохраняет содержимое файлов в репозитории. Само сохранение, или фиксацию состояния файлов, называют **коммитом** (от англ. _commit_ — «совершать», «фиксировать»). «Сделать коммит» значит сохранить текущую версию файла.

Если провести аналогию, команду `git add` можно сравнить с добавлением товаров в корзину в интернет-магазине, а коммит — с оформлением и оплатой заказа.

Коммит — это одна из основных сущностей в Git (и в других системах контроля версий). Коммит гарантирует, что изменения будут сохранены в истории и при необходимости к ним можно будет «откатиться». Это как если бы вы могли выполнить операцию `Ctrl+Z` для целой папки (репозитория).

Сделать коммит можно командой `git commit` c ключом `-m` (от англ. _**m**essage_ — «сообщение»), который присваивает коммиту сообщение.

Обычно в таком сообщении поясняется, в чём именно состояли изменения. Это как заметки на полях: благодаря им проще читать и понимать текст. Сообщение коммита выполняет те же функции — улучшает понимание и упрощает навигацию. Оно пишется после ключа `-m` в кавычках.


```
$ git commit -m 'Мой первый коммит!' 
```

После нажатия `Enter` текущая версия файлов будет сохранена в репозитории с сообщением `Мой первый коммит!`. **Коммит** (по названию команды `git commit`) — это по сути список файлов с их контентом.

Команда `git commit` выведет информацию о коммите.

- `[master (root-commit) baa3b6e]` значит:
    - коммит был в ветке `master`;
    - `root-commit` — это самый первый, или «корневой» (англ. _root_), коммит в ветке, у следующих коммитов такой надписи не будет;
    - `baa3b6e` — сокращённый идентификатор коммита (подробнее об этом мы ещё расскажем).
- `2 files changed, 1 insertion(+)` значит:
    - изменились два файла (`readme.txt` и `todo.txt`);
    - одна строка была добавлена (`1. Пройти пару уроков по Git.`).
- Строки вида `create mode 100644 readme.txt` — это более подробная информация о новых (добавленных в Git) файлах.
    - `create` (англ. «создать») говорит, что файл был создан. Если бы файл был удалён, на этом месте было бы слово `delete` (англ. «удалить»).
    - `mode 100644` сообщает, что это обычный файл. Также возможны варианты `100755` для исполняемых файлов (например, `что-нибудь.exe`) и `120000` для файлов-ссылок в Linux. Файлы-ссылки не содержат данных сами по себе, а только ссылаются на другие файлы — как «ярлыки» в Windows.

Сначала вы просите друзей встать в ряд — это команда `git add`. И только после того, как все заняли свои места, поправили волосы и улыбнулись, вы нажимаете кнопку и делаете снимок — это команда `git commit`. Сам получившийся снимок и будет коммитом.

 Просмотреть историю коммитов — ==`git log`==

Обратите внимание, что по умолчанию `git log` выводит коммиты в обратном хронологическом порядке — последние коммиты оказываются первыми сверху.

До этого момента вы использовали Git **локально**: сейчас проект `first-project` хранится только на вашем компьютере. Но одно из ключевых преимуществ Git — удобство командной работы над файлами. Чтобы поделиться репозиторием — например, с коллегами, — нужно завести его **удалённую версию**.

**GITHUB***
![[Pasted image 20240506225038.png]]
[GitHub](https://github.com/) — платформа для хранения IT-проектов и совместной работы над ними с использованием Git. По сути, это сайт, куда можно загрузить файлы своего проекта для обмена с другими людьми.

Git: - консольный инструмент для работы с локальными и удалёнными репозиториями; - проект с открытым исходным кодом.

GitHub: - платформа для размещения удалённых репозиториев; - принадлежит компании Microsoft.

Осталось связать удалённый репозиторий с локальным, который уже есть на вашем компьютере. GitHub предоставляет для этого инструкцию (пункт `…or push an existing repository from the command line`).
**SSH-ключи** (от англ. _**S**ecure **Sh**ell_ — «безопасная оболочка»)
Чтобы получить доступ к репозиторию на GitHub, вам тоже нужно предоставить ключ, который подтверждает вашу личность и права на чтение или изменение данных. Без этого ключа доступ будет ограничен
С помощью этого протокола можно получать данные с удалённого компьютера или отправлять их на него. Трафик шифруется, поэтому протокол безопасен.

SSH использует пару ключей для обеспечения безопасности — публичный и приватный:

- **Приватный ключ** (англ. _private key_) хранится только на вашем компьютере и не должен передаваться кому-либо ещё. Он используется для расшифровки данных.
- **Публичный ключ** (англ. _public key_) доступен всем и используется для шифрования данных. Они могут быть расшифрованы парным приватным ключом.

По умолчанию директория с SSH-ключами находится в домашней директории пользователя. Перейдите в неё.

```
$ cd ~ # перешли в домашнюю директорию 
```

### Инструкция по генерации SSH-ключа

1. Для генерации SSH-пары можно использовать программу `ssh-keygen`. Откройте терминал и введите следующую команду.

Скопировать кодBASH

```
$ ssh-keygen -t ed25519 -C "rim.i20142014@gmail.com" 
```

Используйте электронную почту, к которой привязан ваш GitHub-аккаунт.
Теперь осталось проверить, что ключи действительно сгенерировались. Для этого вызовите эту команду.

Скопировать кодBASH

```
ls -a ~/.ssh #покажет содержимое папки ssh
ls -la ~/.ssh #покажет содержимое папки ssh в виде списка
```

- SSH-ключ — ваш виртуальный идентификатор в GitHub. Как ключ от квартиры, он позволяет получить доступ к GitHub-репозиторию. Также SSH используется для доступа к другим удалённым серверам.


```
# скопировать содержимое ключа в буфер обмена:
$ clip < ~/.ssh/id_rsa.pub
# для ed25519:
$ clip < ~/.ssh/id_ed25519.pub 
```

9. Проверьте правильность ключа с помощью следующей команды.

```
$ ssh -T git@github.com 
```


#### Привязать удалённый репозиторий к локальному — ==`git remote add`==

`origin` (англ. «источник») — стандартный псевдоним, с помощью которого можно обращаться к главному удалённому репозиторию (обычно такой репозиторий один). Это значительно упрощает работу.

```
$ git remote -v
origin    git@github.com:%ИМЯ_АККАУНТА%/%ИМЯ-ПРОЕКТА%.git (fetch)
origin    git@github.com:%ИМЯ_АККАУНТА%/%ИМЯ-ПРОЕКТА%.git (push) 
```

В выводе вы должны увидеть две строчки, аналогичные тем, что показаны выше.

Флаг `-v` — короткая форма флага `--verbose` (англ. «подробный»). Он позволяет показать больше информации в выводе.

Вы зарегистрировались на GitHub, сгенерировали SSH-ключ и привязали локальный репозиторий к удалённому. Самое сложное позади! Теперь разберём, как выкладывать свои правки на удалённый репозиторий. Но сначала немного о ветках.

Если коммит — это снимок состояния файлов, то ветка — временна́я шкала, на которой расположены эти снимки. Ветка всегда начинается от одного из коммитов.

В репозитории может существовать сразу несколько веток — параллельных историй изменений. Также они могут соединяться друг с другом.

#### Отправить изменения на удалённый репозиторий — ==`git push`==

Вы уже прошли весь «цикл коммита»: подготовили файлы с помощью `git add`, закоммитили их с комментарием командой `git commit -m`. Осталось загрузить содержимое локального репозитория на GitHub. За это отвечает команда `git push` (от англ. _push_ — «толкать»).

В первый раз эту команду нужно вызвать с флагом `-u` и параметрами `origin` (имя удалённого репозитория) и `main` или `master` (название текущей ветки). Флаг `-u` свяжет локальную ветку с одноимённой удалённой.

```
$ git push -u origin main # Если команда приведёт к ошибке, попробуйте 
                          # заменить main на master. 
```

В дальнейшем при работе с удалённым репозиторием флаг `-u` можно опустить и писать просто `git push`.


Чтобы другие пользователи, а также потенциальные клиенты или работодатели могли понять, что представляет собой проект, его нужно описать. Такое описание принято указывать в файле ==`README.md`== (от англ. _read_ — «прочитай» и _me_ — «меня»).

## Как создать и оформить `README.md`

`README.md` — текстовый файл, который можно создать командой `touch`, а затем редактировать так же, как и любой другой текстовый документ. Например, в блокноте.

Преимущество `README.md` в том, что средства командной работы (такие, как GitHub) могут отображать его содержимое в браузере в виде удобной разметки. Для этого нужно не просто залить текст, но и настроить шрифт, заголовки и отступы с помощью markdown. **Маркда́ун** — это специальный язык разметки. Он позволяет красиво отформатировать текстовый документ.

### Заголовки, абзацы и перенос

- **Заголовки** разных уровней создают решётками.

```
# H1 — заголовок первого уровня, самый большой
## H2 — заголовок второго уровня, поменьше
### H3
#### H4
##### H5
###### H6 — заголовок шестого уровня, самый маленький 
```

- Можно добавить **черту под заголовком или абзацем**.

```
#### Заголовок 4

Текст над чертой

---

Текст под чертой 
```

- Чтобы сделать **разрыв строки**, нужно поставить два пробела (в примере ниже они обозначены точками `⋅⋅`) или сочетание символов `<br>`.

```

Текст до переноса⋅⋅  
Текст после переноса <br>
Текст после второго переноса 
```

- Чтобы начать **новый параграф**, в конце предыдущей строки должно стоять два символа переноса. Для этого нужно нажать `Enter` два раза.

```
line

another line  
```
### Выделение текста

- Чтобы выделить текст **курсивом** (`*текст*`), его заключают в звёздочки (астериски) или нижние подчёркивания.

```
Курсив — это *звёздочки* или _подчёркивания_. 
```

- Чтобы выделить текст **полужирным шрифтом** (`**текст**`), его окружают двойными звёздочками или двойными нижними подчёркиваниями.

```
Полужирный шрифт — двойные **звёздочки** или двойные __подчёркивания__.
Можно совместить выделение **звёздочки и _подчёркивания_**. 
```

- Чтобы **зачеркнуть текст** (`~~текст~~`), его окружают двойными волнистыми линиями — тильдами.

```
~~Зачёркнутый текст.~~ 
```
### Списки

- Для оформления **нумерованного списка** достаточно поставить в начало строки цифры с точкой.

```
1. Первый пункт нумерованного списка.
2. Второй пункт. 
```

- **Ненумерованный список** создаётся звёздочкой с пробелом в начале строки либо дефисом с пробелом.

```
* первый пункт ненумерованного списка;
* второй пункт ненумерованного списка

- первый пункт ненумерованного списка;
- второй пункт ненумерованного списка 
```

### **Ссылки**

- Чтобы сделать ссылкой часть текста, его заключают в квадратные скобки, а затем указывают нужный адрес в круглых скобках.

```
[Яндекс](https://www.yandex.ru) 
```

- Также можно добавить ссылке **тайтл** (от англ _title_ — «название», «заголовок»). Тайтл — это всплывающая подсказка, которая появляется при наведении мыши на ссылку. Тайтл нужно заключить в кавычки и указать внутри скобок после адреса.

```
[Яндекс](https://www.yandex.ru "Я Yandex!") 
```
### **Код**

Чтобы оформить текст как код, нужно окружить его тройками косых кавычек — грависов. После первой тройки грависов указывают язык программирования, на котором написан код. В маркдауне есть поддержка синтаксиса почти всех популярных языков и инструментов.

````
```bash
ls - la
```
```html
<h1>А я просто текст</h1>
``` 
````
