---
title: MuSig2 и FROST
weight: 6
date: 2025-02-06
---

<center>Объяснение схем мультиподписи в Taproot</center>

> [Оригинал статьи на английском](https://blog.bitbox.swiss/en/musig2-and-frost-explaining-multisignature-schemes-on-taproot/)

Давайте рассмотрим, как схемы с несколькими подписями, построенные на базе Taproot, могут улучшить приватность и снизить комиссии за транзакции.

Более трех лет назад, в ноябре 2021 года, [софтфорк Taproot](https://blog.bitbox.swiss/en/taproot-planting-the-seeds-for-bitcoin-applications/) принес с собой новые возможности для транзакций и более эффективные подписи Шнорра. Несмотря на то, что поначалу принятие нового формата Taproot происходило довольно медленно, на сегодняшний день на него приходится около [20 % всех UTXO в Биткоине](https://transactionfee.info/charts/inputs-types-by-count/), и все больше приложений начинают использовать более продвинутые возможности Taproot.

В этой статье мы хотим заглянуть в будущее, и посмотреть на новые схемы мультиподписи, такие как [MuSig2](https://eprint.iacr.org/2020/1261.pdf) и [FROST](https://eprint.iacr.org/2020/852.pdf), которые позволяют создавать кошельки с несколькими подписями, использующие преимущества повышенной приватности и эффективности Taproot. Чтобы понять, как они работают и откуда берутся их преимущества, мы сначала кратко рассмотрим скрипты в Биткоине и Taproot, а затем погрузимся в схемы подписи более подробно.

## Биткоин-скрипты

Когда вы получаете биткоины, отправитель **создает новый выход (output), который блокирует биткоины с помощью скрипта** — это технически точный способ сказать, что "он отправляет средства на ваш Биткоин-адрес". Скрипт в Биткоин — это небольшой и простой программный код, который определяет, можно ли потратить данную транзакцию; именно это и проверяют все участники сети при верификации транзакций. Например, стандартный скрипт Native Segwit (P2WPKH) проверяет, соответствуют ли предоставленные публичный ключ и цифровая подпись хешу публичного ключа, уже записанному в блокчейне Биткоин — и всё. Проще говоря: скрипт убеждается в том, что у вас есть правильные ключи от Биткоин-адреса.

{{% image src="img/bitcoin/musig2-1.webp" /%}}

Однако существуют и более сложные способы использования скриптов в Биткоин — **с добавлением дополнительных условий для расходования средств**. Один из популярных примеров — это Multisig-кошельки, которые требуют более одной подписи для отправки монет. При трате средств с Multisig-адреса такой, более сложный, скрипт должен быть раскрыт — чтобы подтвердить, что подписи действительны, а также чтобы другие могли убедиться, что адрес действительно соответствует этому скрипту (и, следовательно, условиям мультиподписи).

{{% image src="img/bitcoin/musig2-2.webp" /%}}

У нынешнего положения дел с Multisig-кошельками есть два основных недостатка. Во-первых, это не идеальный вариант для обеспечения приватности, поскольку все могут видеть детали вашей конфигурации (например, используете ли вы схему 2-из-3, 3-из-5 или более сложный подход). Во-вторых, чем больше условий расходования средств вы добавляете, тем больше становится скрипт и тем выше комиссия за транзакцию.

## Трата через ключ и скрипт: два пути в Taproot

При трате монет с Taproot-адреса можно использовать **один из двух путей**: через ключ (key path) или через скрипт (script path). Каждый из них имеет свои преимущества в определенных ситуациях и позволяет делать транзакции более приватными и эффективными.

### Трата через ключ

Стандартный или "предпочтительный" способ использования Taproot — это трата через ключ (key path spend), и он довольно прост для понимания: Taproot-выход содержит один публичный ключ, и его можно потратить с помощью одной подписи. Если при попытке потратить Taproot-выход вы предоставляете только подпись, без какого-либо скрипта, — значит, вы совершаете трату через ключ. Обычная транзакция с Taproot-адреса является именно тратой через ключ.

{{% image src="img/bitcoin/musig2-3.webp" /%}}

Трата через ключ хороша тем, что полностью устраняет из блокчейна всю ненужную информацию — требуется только один публичный ключ и цифровая подпись. Это делает все Taproot-транзакции, использующие key path, внешне одинаковыми. Невозможно определить, какие именно условия были необходимы для создания подписи, в раскрытии условий нет необходимости. Пока подпись действительна, транзакция считается действительной. Не важно, стоит ли за этой подписью обычная подпись, мультиподпись или даже Lightning-канал.

### Трата через скрипт

Трата через скрипт (script path spend) похожа на текущую модель, при которой условия траты раскрываются и проверяются напрямую в блокчейне Биткоина. Такой способ можно использовать как альтернативный вариант для расходования выхода, например, если обычное условие для траты через key path не выполняется. Под _обычным условием_ здесь подразумевается возможность подписать транзакцию основным ключом — например, с вашего аппаратного кошелька. В свою очередь, альтернативным вариантом может быть резервный ключ, хранящийся отдельно в другом месте, который используется только в случае, если доступ к основному кошельку невозможен.

Тем не менее, траты через скрипт в Taproot тоже имеют свои преимущества, потому что нужно раскрывать **только ту часть условий, которая действительно используется**. Это снова открывает возможности для лучшей приватности и меньших размеров транзакций, поскольку не вся информация записывается в блокчейн — только та, которая реально имеет значение для конкретной транзакции.

Самое крутое в Taproot — это то, что **можно использовать оба способа одновременно**! Taproot-выход с одним публичным ключом может быть потрачен либо с помощью одной подписи (key path), **либо** через альтернативный путь — с помощью скрипта (script path). Нетрудно догадаться, что публичный ключ в Taproot — это не просто обычный ключ, а весьма хитро сконструированная сущность с криптографической точки зрения. И это только начало!

## Знакомство с MuSig

Теперь, когда у нас есть базовое понимание Биткоин-скриптов и того, как работают различные пути расходования, мы наконец можем углубиться в различные схемы мультиподписи и то, как они используют возможности Taproot. В одном из предыдущих разделов мы уже упомянули возможность "скрыть" Multisig-кошелек за простой тратой через ключ — но как это возможно? Как одна подпись может представлять сразу нескольких подписантов?

### Агрегация подписей

Подписи Шнорра, введенные с обновлением Taproot, обладают привлекательным свойством, называемым "линейность", что означает, что подписи можно легко **сложить**, "агрегируя" несколько подписей от разных участников в одну.

Исходя из этого принципа, Биткоин-эксперты и криптографы разработали элегантную схему мультиподписи. MuSig2 использует агрегацию ключей и подписей, чтобы реализовать **n-из-n Multisig-кошельки** (например, 4-из-4, как показано на схеме ниже), не раскрывая при этом в сети Биткоин информацию об индивидуальных подписях.

{{% image src="img/bitcoin/musig2-4.webp" /%}}

Никто вне этой сессии подписания не знает, что Алисе, Бобу, Сатоши и Кэрол пришлось собраться вместе и создать подпись совместно. Более того, никто даже не узнает, что транзакция происходит из Multisig-кошелька, **что улучшает приватность за счет маскировки среди других Taproot-транзакций**. Кроме того, такая транзакция имеет тот же размер, что и обычная транзакция с одной подписью, поскольку ни один из четырех индивидуальных ключей или подписей не публикуется, **что снижает комиссию за транзакцию** и делает Биткоин **более эффективным**.

### Взаимодействие

Как и большинство вещей в жизни, MuSig2 также сопровождается определенными компромиссами, которые в основном сводятся к необходимости взаимодействия между подписантами. В традиционных Multisig-кошельках подписанты просто обмениваются подписями, которые включаются в финальную транзакцию, тогда как в MuSig2 им нужно несколько раз обмениваться данными, чтобы безопасно вычислить агрегированные ключи и подписи.

Первая версия MuSig требовала трех раундов взаимодействия и была немного доработана по соображениям безопасности. Именно поэтому сегодня используется ее преемник — MuSig2, которому требуется всего два раунда взаимодействия. Тем не менее, это добавляет сложности для пользователей, хотя преимущества в плане приватности и размера транзакции, вероятно, перевешивают это неудобство для многих.

Другой аспект — это ограничение конфигурацией **n-из-n** без возможности напрямую задать порог (например, 2-из-3), как предпочитает большинство пользователей Multisig-кошельков — на случай потери одного из ключей. Чтобы добавить резервный вариант, можно задать дополнительные условия траты через скрипт с “запасной комбинацией” ключей. Это по сути вводит пороговую схему, но также требует более сложного скрипта и настройки. Но, как вы, возможно, уже догадались — на этом история не заканчивается!

## FROST

Протокол _"Гибкая, оптимизированная по раундам пороговая подпись Шнорра"_, или просто **FROST** (Flexible Round-Optimized Schnorr Threshold Signature), — это еще одна схема мультиподписи, аналогичная MuSig2, по крайней мере с точки зрения создания и агрегации подписей. Однако она использует другую конструкцию генерации ключей, чтобы **поддерживать пороговые схемы**.

Чтобы реализовать это безопасно и, например, позволить любым двум подписантам из четырех (2-из-4) создать действительную агрегированную подпись, существует два варианта создания ключей перед подписанием:

1. Доверенная сторона генерирует и распределяет ключи — это простое решение, но оно требует доверия.
2. Проведение интерактивной сессии между подписантами для совместной генерации ключей — также известно как [распределенная генерация ключей (DKG)](https://en.wikipedia.org/wiki/Distributed_key_generation), при которой необходимы защищенные каналы связи между участниками.

Иными словами, добавление **возможности задать порог увеличивает сложность**, но зато позволяет создавать более гибкие и отказоустойчивые схемы. При этом трата через FROST будет выглядеть как обычная Taproot-транзакция — с тем преимуществом для приватности, что никто не узнает, какая именно комбинация из доступных подписантов участвовала в подписании и трате средств.

{{% image src="img/bitcoin/musig2-5.webp" /%}}

## Заключение

Если перестать рассматривать эти концепции по отдельности и начать думать о том, как они могут сочетаться и использоваться вместе с другими механизмами, открываются практически безграничные возможности. Пользователи смогут тонко настраивать свои схемы — например, с использованием таймлоков и резервных путей восстановления, как это уже возможно с [кошельком Liana](https://blog.bitbox.swiss/en/exploring-bitcoin-miniscript-with-liana-and-the-bitbox02/), — при этом комбинируя их и получая преимущества от продвинутых схем, о которых мы только что узнали. Такие решения масштабирования, как сеть Lightning, также могут использовать схемы вроде MuSig2, чтобы сделать управление каналами более эффективным и приватным.

> Liana — кошелек, который использует Taproot и Miniscript для создания гибких и безопасных схем.
>
> Он сочетает:
>
> -  **Основной путь траты** (key path) — это простая схема, например:
>  
>		- Одиночная подпись
>		- MuSig2 (требуется участие всех подписантов)
> -  **Резервные пути траты** (script path) — на случай потери ключа, недоступности владельца и т.п., через Miniscript:
>  
>		- Например, можно потратить средства другим ключом, но только спустя 6 месяцев
>		- Или разрешить трату, если доступны любые 2 из 3 ключей — с помощью скрипта, реализующего такую логику
>
> — Прим. переводчика.

Преимущества MuSig2 и FROST особенно проявляются в сложных конфигурациях кошельков и при большом количестве подписантов, что делает их особенно интересными для крупных организаций с особыми требованиями. Но даже в личном использовании они могут быть полезны — за счет экономии на комиссиях и повышения приватности. Хотя сейчас все еще ранний этап, Taproot и его преимущества уже закрепились, и, скорее всего, масштаб распространения и удобство использования схем мультиподписи, вроде MuSig2 и FROST, будут только расти.

---

## Часто задаваемые вопросы (FAQ)

**Что такое мультиподпись (Multisig)?**  
Мультиподпись — это функция безопасности, требующая несколько приватных ключей для подтверждения Биткоин-транзакции, что снижает риск, связанный с уязвимостью одного ключа.

**Как MuSig2 улучшает Multisig?**  
MuSig2 повышает приватность и эффективность, объединяя несколько публичных ключей в одну подпись, совместимую с Taproot, делая транзакции с мультиподписью неотличимыми от обычных.

**Что такое FROST?**  
FROST (Flexible Round-Optimized Schnorr Threshold) — это интерактивная схема пороговой подписи, позволяющая части владельцев ключей подписывать транзакции, оптимизируя скорость и гибкость Multisig.

**Почему Taproot важен для Multisig?**  
Taproot улучшает приватность и эффективность Биткоин-транзакций, делая сложные условия траты (включая мультиподпись) неотличимыми от стандартных транзакций с одной подписью.

**MuSig2 лучше традиционного Multisig?**  
Да, MuSig2 обеспечивает лучшую приватность, более низкие комиссии и лучшую масштабируемость за счет уменьшения объема данных в транзакциях с мультиподписью.

**Чем FROST отличается от MuSig2?**  
FROST поддерживает пороговые подписи, позволяя части подписантов авторизовать транзакцию, тогда как MuSig2 требует участия всех участников для создания одной агрегированной подписи.

**Отменяет ли Taproot необходимость в Multisig?**  
Нет, Taproot лишь улучшает мультиподпись, делая ее более приватной и эффективной, но не заменяет ее. Multisig остается важным инструментом безопасности для Биткоин-кошельков и решений для хранения.