---
layout: post
title:  "Microservices and timezones"
date:   2024-04-12
categories: jekyll update
tags: [ development, microservices, timezone ] 
---
Когда начинаешь разрабатывать новый сервис, многие вещи отходят на второй план, а иногда и вовсе забываются. Некоторые же, по своей неопытности, разработчик может вообще посчитать константными, которые не изменятся никогда. И чем дальше мы откладываем фиксы этого техдолга, тем больнее будет это сделать в будущем, особенно когда у тебя уже есть платящие клиенты.

Одна из таких мерзких вещей в приложении - таймзона.

На старте разработки про нее забываем, а затем нам приходится либо писать тяжелые миграции, либо подстраивать бизнес-логику под искусственный костыль, который мы сами себе и создали на ровном месте.

Проблема с таймзонами более комплексная, чем кажется на первый взгляд. Допустми, мы живем в Берлине и у нас часовой пояс GMT+2, наш сервер тоже находится в Берлине и у него, соответстенно, тоже GMT+2. Мы спокойно можем сохранять время как в LocalDateTime, так и в OffsetDateTime - ничего не изменится. Мы знаем, что мы существуем в одной таймзоне.

Однако, что же может пойти не так?

Предположим, у нас появляется новый разработчик или мы просто решили по незнанию в миграции базы данных использовать `now()`. Что тогда произойдет? Конечно, мы находимся в одном часовом поясе и вроде бы все должно пройти гладко. Но это до тех пор, пока мы не перенесем сервер в другой регион, в котором часовой пояс ***может*** отличаться.

А еще, у нас может быть распределенная система, в которой базы данных физически находятся в разных серверах, на разных ЦОДах в разных часовых поясах. Представляете, какая начинается проблема по приведению всех дат к одному формату? Повезет, если мы это заметим быстро. В большинстве случаев об этом вспоминают, когда возникают нестыковки, приводящие к потере денег.

Но не только с базами данных могут возникнуть проблемы. Как правило, программисты все же пытаются временами подложить соломки и предусмотреть кастомизацию часового пояса. В Spring это можно сделать настройкой *application.yml* или аргументом при старте приложения. Но фундаментально это не решает проблему.

> Часовой пояс все равно будет отличаться, если забыть про все настройки

Моя позиция макисмально проста - программист должен писать код, он не должен думать про окружение, на котором его код будет запущен. Наши уровни абстракции и так высоки, чтобы помнить про локальные проблемы. Конечно, это можно спихнуть на QA, но ответьте мне на один вопрос. Зачем делать бесполезную работу, если ее можно не делать? Зачем разработчикам, тестировщикам, devops-инженерам помнить про все эти параметры? Это ведь далеко не одна настройка в одном месте, это разнесенная по коду и конфигурациям бомба замедленного действия. Я как lead не хочу увеличивать риск возникновения проблем на runtime из-за неочевидного конфигурации. К счастью, эту проблему можно решить.

На своей практике я выработал решение, которое для меня оказалось настолько простым в поддержке, что я больше не заморачиваюсь с таймзонами и не переживаю за переезд на разные ЦОДы.

> Я просто все перевел в GMT+0 и запретил использовать `now()` в миграциях бд

Это радикальное решение позволило получить несколько преимуществ:

- это единожды настраиваемое системное решение;
- наши приложения конфигурируются один раз с единой таймзоной GMT+0;
- тестирование упрощается, ведь мы сравниваем единое время у пользователя и в базе данных;
- в базу не попадут некорректные значения, и нам совершенно без разницы, как там devops сконфигурировал postgres кластер;
- строгие правила позволяют покрыть их выполнение правилами на линтере или sonarqube.

При разработке я всегда выбираю наиболее системные, единожды настраиваемые решения вместо латания локальных проблем. Сервисы должны быть независимыми и разворачиваться где угодно без мутаций бизнес-логики.