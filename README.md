# Лабораторная работа по теме "Планировщик задач cron в Linux"
## Цель работы:
Ознакомление с планировщиком задач cron, изучение принципов его работы, настройка и управление задачами.

## Задание для тренировки:
Настроить задачу cron, которая будет создавать резервную копию каталога /home/user/myfiles каждую ночь в 23:59.

## Ход работы:
1. Чтобы добавить новую задачу в cron, используем команду crontab с ключом -e для редактирования:

```crontab -e```

Если вы до этого не редактировали crontab, система предложит выбрать текстовый редактор.

2. Создание задачи

Каждая запись в crontab состоит из шести полей:

```Минуты Часы День_месяца Месяц День_недели Команда```

Где:

**Минуты:** от 0 до 59

**Часы:** от 0 до 23

**День месяца:** от 1 до 31

**Месяц:** от 1 до 12 (или именованные значения jan-dec)

**День недели:** от 0 до 7 (где 0 и 7 обозначают воскресенье), либо именованные значения sun-sat

**Команда:** команда, которую нужно выполнить

Для нашего примера мы хотим запускать команду каждую ночь в 23:59. Поэтому наша запись будет выглядеть так:

```59 23 * * * tar -czvf /path/to/backup.tar.gz /home/user/myfiles```

Разберем эту запись:

```59```: минуты (59 минут)

```23```: часы (23 часа)

```* * *```: оставляем звездочки, чтобы задача выполнялась ежедневно независимо от дня, месяца и дня недели

```tar -czvf /path/to/backup.tar.gz /home/user/myfiles```: команда для архивации каталога /home/user/myfiles в файл /path/to/backup.tar.gz

3. После добавления записи сохраните файл и выйдите из редактора. Теперь ваша задача настроена и будет выполняться согласно расписанию.

## Задание для выполнения
Настроить задачу cron, которая будет отправлять отчет о состоянии системы (загрузка CPU, использование памяти, свободное дисковое пространство) администратору по электронной почте каждое утро в 8:00.

## Ход работы:
1. Сначала создадим скрипт, который будет собирать необходимую информацию о системе. Назовем его system_report.sh и поместим в каталог /usr/local/bin/.

```#!/bin/bash
#Собираем данные о загрузке CPU
cpu_load=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d '.' -f1)

#Используемая память
mem_used=$(free -h | grep Mem | awk '{print $3}')
total_mem=$(free -h | grep Mem | awk '{print $2}')

#Свободное дисковое пространство
free_disk=$(df -h | grep '/$' | awk '{print $4}')

#Формируем отчет
report="System Report\nCPU Load: ${cpu_load}%\nMemory Used: ${mem_used}/${total_mem}\nFree Disk Space: ${free_disk}"

#Отправляем отчет по email (можно указать свой)
echo "$report" | mail -s "Daily System Report" admin@example.com
```
Не забудьте сделать скрипт исполняемым:

```chmod +x /usr/local/bin/system_report.sh```

2. Добавьте следующую запись в crontab:

```0 8 * * * /usr/local/bin/system_report.sh```
Эта запись означает, что скрипт system_report.sh будет выполняться каждый день в 08:00 утра.

3. Проверьте, выполняется ли скрипт (пришло ли на почту письмо)

## Ресурсы
Официальная документация по cron: https://man7.org/linux/man-pages/man5/crontab.5.html
Руководство по использованию cron: https://www.admin-magazine.com/article/a_beginners_guide_to_cron_jobs
Статья о настройке cron: https://opensource.com/article/17/11/how-use-cron-linux
Инструкция по работе с почтой через mailx: https://www.tecmint.com/send-mail-from-command-line-in-linux/
