# Docker

- [Контейнер vs VM](#контейнер-vs-vm)
- [Dockerfile](#dockerfile)
- [Внутреннее устройство контейнера](#внутреннее-устройство-контейнера)
- [Writable layer, volume, bind mounts](#writable-layer-volume-bind-mounts)
- [Устройство сети](#устройство-сети)

## Контейнер vs VM
Контейнеры и виртуальные машины — это разные способы виртуализации. Только виртуалка реализует её на уровне железа, а 
контейнер — на уровне операционной системы.

Виртуальная машина функционирует как отдельный компьютер с собственным оборудованием и операционной системой. Виртуальные 
компьютеры вполне полноценны. На них можно установить операционную систему любого семейства и работать в ней, 
например, через графический интерфейс в многопользовательском режиме, устанавливая и запуская множество приложений и 
сервисов.

Если цель виртуалки — полностью воспроизвести устройство компьютера, то основная цель контейнера — создать среду для 
одного приложения. Виртуальная среда контейнера запускается внутри операционной системы. Ей не нужно виртуализировать 
оборудование — она использует его через ОС. Поэтому контейнеры Docker потребляют меньше ресурсов, быстрее развёртываются, 
проще масштабируются и меньше весят.

## Dockerfile
Каждому образу Docker соответствует файл, который называется Dockerfile. Его имя записывается именно так — без 
расширения. При запуске команды docker build для создания нового образа подразумевается, что Dockerfile находится в 
текущей рабочей директории. Если этот файл находится в каком-то другом месте, его расположение можно указать с 
использованием флага -f.

В файлах Dockerfile содержатся инструкции по созданию образа. С них, набранных заглавными буквами, начинаются строки 
этого файла. После инструкций идут их аргументы. Инструкции, при сборке образа, обрабатываются сверху вниз. Вот как это 
выглядит:
```
FROM ubuntu:18.04
COPY . /app
```

1) **FROM** — задаёт базовый (родительский) образ.
2) **LABEL** — описывает метаданные. Например — сведения о том, кто создал и поддерживает образ.
3) **ENV** — устанавливает постоянные переменные среды.
4) **RUN** — выполняет команду и создаёт слой образа. Используется для установки в контейнер пакетов.
5) **COPY** — копирует в контейнер файлы и папки.
6) **ADD** — копирует файлы и папки в контейнер, может распаковывать локальные .tar-файлы.
7) **CMD** — описывает команду с аргументами, которую нужно выполнить когда контейнер будет запущен. Аргументы могут быть переопределены при запуске контейнера. В файле может присутствовать лишь одна инструкция CMD.
8) **WORKDIR** — задаёт рабочую директорию для следующей инструкции.
9) **ARG** — задаёт переменные для передачи Docker во время сборки образа.
10) **ENTRYPOINT** — предоставляет команду с аргументами для вызова во время выполнения контейнера. Аргументы не переопределяются.
11) **EXPOSE** — указывает на необходимость открыть порт.
12) **VOLUME** — создаёт точку монтирования для работы с постоянным хранилищем.

## Внутреннее устройство контейнера
Контейнер состоит из операционной системы, пользовательских файлов и метаданных. Как мы знаем, каждый контейнер создается 
из образа. Этот образ говорит docker-у, что находится в контейнере, какой процесс запустить, когда запускается контейнер 
и другие конфигурационные данные.

## Writable layer, volume, bind mounts
**Временное хранение данные**  
По умолчанию файлы, создаваемые приложением, работающим в контейнере, сохраняются в слое контейнера, поддерживающем 
запись (writable layer). Для того чтобы этот механизм работал, ничего специально настраивать не нужно. Однако после того 
как контейнер перестанет существовать, исчезнут и сохраненные данные.

Есть еще один способ, когда требуется более высокий уровень производительности. Если вам не нужно, чтобы ваши данные 
хранились бы дольше, чем существует контейнер, вы можете подключить к контейнеру tmpfs — временное хранилище информации, 
которое использует оперативную память хоста. Это позволит ускорить выполнение операций по записи и чтению данных.

**Постоянное хранение данных**
Volume и Bind mounts - два способы монтирования данных из файловой системы хоста в файловую систему контейнера. Это 
позволяет не ограничивать срок жизни данных сроком жизни контейнера. Однако volume появился позже, гораздо удобнее и 
имеет ряд фич:
1) Можно управлять с помощью CLI или GUI
2) Работают как на Windows, так и Linux
3) Volume могут быть расположены удалено в облаке
4) Volume можно шифровать и давать имена
5) etc

## Устройство сети
Докер представляет следующие настройки уровня сети:
1) Драйвер none
Контейнер в полностью изолированной среде. К нему не могут достучаться извне, он тоже не может постучать вовне.
2) Драйвер bridge
Данный драйвер предоставляет возможность собрать несколько контейнеров в одну сеть и позволяет им общаться между собой.
По умолчанию (если не указать иное) докер определяет все контейнеры в дефолтный мост. То есть если не указать настройку
сети, то такие контейнеры будут видеть друг друга.
3) Драйвер host
Драйвер приравнивает хост и контейнер. То есть контейнер как будто запускается на особых портах localhost.
4) Драйвер overlay
Драйвер для соединения нескольких bridge в одну.
5) Драйвер Macvlan
Данный драйвер присваивает контейнеру MAC адресу, как будто это физическое устройство и к такому контейнеру
так же можно обращаться, как к физическому устройству