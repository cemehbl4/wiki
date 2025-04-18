---
title: Использование SimpleX
weight: 4
date: 2025-03-27
---

## Установка

- Скачать клиент для смартфона: https://simplex.chat/
- Скачать клиент для ПК: https://simplex.chat/downloads/#desktop-app

## Настройки клиента на Android

- После создания профиля, нажмите иконку профиля в левом нижнем углу приложения > **Настройки**.
- Если вы хотите всегда получать уведомления, даже если приложение закрыто, выберите **Уведомления** > **Сервис уведомлений** > **Всегда включен**.

{{% image src="img/privacy/simplex-1.webp" /%}}

- Система управления питанием Android может ограничивать работу приложений в фоновом режиме. Рекомендуется установить следующую настройку для SimpleX: перейдите в настройки Android > **Приложения** > **Показать все приложения** > **SimpleX** > **Батарея** > выберите опцию **Без ограничений** (не ограничивать расход заряда батареи, когда приложение работает в фоновом режиме). Местоположение опции может отличаться в зависимости от версии Android на вашем устройстве.

- Для лучшего качества синхронизации в настройках перейдите в раздел **Сеть и серверы** > активируйте серверы **Flux** для получения и доставки сообщений.

{{% image src="img/privacy/simplex-2.webp" /%}}

- Для получения более привычного интерфейса перейдите в раздел настроек **Интерфейс** и выключите опции **Доступная панель приложения** и **Доступная панель чата**. Изменить цвет темы можно с помощью опции **Режим цветов**.
- Если наблюдаются проблемы с прокруткой, попробуйте отключить опцию **Хвост** в параметрах интерфейса.

{{% image src="img/privacy/simplex-3.webp" /%}}

## Резервное копирование чатов

Так как мессенджер SimpleX является децентрализованным, все данные чатов находятся на стороне пользователей. Поэтому важно регулярно делать резервную копию ваших чатов. Восстановить данные получится только на дату создания резервной копии.

> Без резервной копии восстановить данные невозможно.

- В настройках перейдите в раздел **База данных** и задайте пароль, выбрав опцию **Пароль базы данных**.
- Сохраните пароль на бумажном носителе.
- После создания пароля нажмите **Экспорт архива чата**. В памяти смартфона будет сохранен зашифрованный файл базы данных.

{{% image src="img/privacy/simplex-4.webp" /%}}

- Создайте копию файла базы данных на другом устройстве, например на вашем ПК или в облачном хранилище.

## Особенности использования

- Мессенджер не требует регистрации.
- Данные хранятся на устройствах пользователей.
- Не рекомендуется использовать одну и ту же базу данных на разных устройствах. Если вы хотите использовать SimpleX на двух устройствах, например смартфоне и ПК, создайте отдельный профиль на каждом из них.
- Вы должны находиться онлайн, чтобы собеседник мог присоединиться к вашему чату.
- Если вы не можете вступить в чат по ссылке, нажмите иконку карандаша на главном экране приложения, выберите **Сканировать / Вставить ссылку** > **Нажмите, чтобы вставить ссылку** (из буфера обмена).
- Для тестирования приложения вы можете присоединиться к чату команды разработчиков SimpleX на экране **Новое сообщение**.

{{% image src="img/privacy/simplex-5.webp" /%}}

- Если после перехода / вставки ссылки, чат находится в состоянии **Запрошено соединение**, просто подождите. Присоединение к чату зависит от того, находится ли создатель или администратор чата онлайн.

## Устранение проблем

- Для мониторинга состояния соединения с серверами нажмите на значок сети рядом с заголовком "Чаты" на основном экране приложения, чтобы попасть в меню **Информация о серверах** > включите опцию **Показывать процент**.
- В этом же меню внизу находится опция **Переподключить все серверы**, которая может быть полезна, если вы не получаете часть сообщений. Здесь же находится статистика по ошибкам соединений.

{{% image src="img/privacy/simplex-6.webp" /%}}

- Также рекомендуется сделать активными все доступные серверы. Для этого в настройках перейдите в раздел **Сеть и серверы** > **Simplex Chat** или **Flux** > выберите неактивный сервер в списке **Серверы сообщений** или **Серверы файлов и медиа** > в настройках сервера включите опцию **Использовать для новых соединений**.

{{% image src="img/privacy/simplex-7.webp" /%}}

- Если вы долго не заходили в приложение, то синхронизация групповых чатов может занять длительное время. Рекомендуется ознакомиться со статистикой ошибок соединений и использовать опцию **Переподключить все серверы** при возникновении проблем. После синхронизации перейдите в **Личные заметки** и снова вернитесь в нужный чат для устранения проблемы с порядком сообщений.
- Для сброса счетчика непрочитанных сообщений сделайте длинный тап на нужном чате > **Прочитано**.
- Наилучшее качество связи достигается с использованием VPN или Tor.