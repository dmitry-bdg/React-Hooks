Lecture Text:
1) useState 
Наверное самый простой хук, механика которого понятна, однако понимание того, как он работает приходит со временем. В аргументы хука мы кладём дефолтное значение. Хук возвращает нам константу с этим значением и функцию для ее перезаписи. Подробнее вы можете почитать в документации. 

В отличие от метода setState, который вы можете найти в классовых компонентах, useState не объединяет объекты обновления автоматически. (Тут пример). 

Вы можете повторить это поведение, используя спред оператор. О таких вещах также предупреждает тайпскрипт (очень рекомендую его использование в место  Js. Помогает избежать многих ошибок, контролировать приложение, а со временем начинает буквально писать код за вас. На ютьюбе достаточно много роликов занимающих часа 2-3 где подробно рассказывается об интеграции тайпскрипта в реакт).Ну а вообще, во избежание таких ситуаций можно написать свой отдельный useState для каждого значения объекта или использовать  useReducer (нет).

Что хотелось бы дополнительно отметить (на примере счетчика ).
Нас интересует строка <p>Counter value: {count}</p>
В нашем примере count — это просто число. Это не «привязка данных», не «объект-наблюдатель» или что угодно другое. Перед нами —  число, вроде этого const count = 43;

React вызывает компонент всякий раз, когда мы обновляем состояние. В результате каждая операция рендеринга «видит» собственное значение состояния counter, которое, внутри функции, является константой. В результате нет никакой магии, строка просто встраивает числовое значение в код, формируемый при рендеринге. Это число предоставляется средствами React. 
Главный вывод, который можно из этого сделать, заключается в том, что count является константой внутри любого конкретного рендера и со временем не меняется. Т.е. меняется компонент, который вызывается снова и снова. Каждый рендер «видит» собственное значение count, которое оказывается изолированным и неизменным для каждой из операций рендеринга.
(Далее пример с setTimeout). 
Также неизменными для каждого рендера остаются и обработчики событий. По сути, в каждом рендере мы используем свою функцию, в которой лежит свой count. Внутри каждого конкретного рендера свойства и состояние всегда остаются одними и теми же. То же самое происходит и с любыми механизмами, использующими их (включая обработчики событий). Они тоже «принадлежат» конкретным рендерам. Поэтому даже асинхронные функции внутри обработчиков событий будут «видеть» те же самые значения count.

2) useEffect
Как вы уже наверное знаете, есть такое понятие как жизненный цикл компонента. В классовых компонентах за выполнение действий на разных этапах жизни компонента отвечают такие методы как copmonentDidMount, componentDidUpdate, componentWillUnmount. В функциональных компонентах для выполнения этих действий используется хук useEffect.(пример)
Сразу бы хотелось отменить: тут нет магии, нет «привязки данных», или «объекта-наблюдателя», который обновляет значение count внутри функции эффекта. 
Сount (как и ранее) представляет собой константу. Обработчики событий (как и ранее) «видят» значение count из рендера, которому они «принадлежат» из-за того, что count — это константа, находящаяся в определённой области видимости. То же самое справедливо и для эффектов! И надо отметить, что это не переменная count каким-то образом меняется внутри «неизменного» эффекта. Перед нами — сама функция эффекта, различная в каждой операции рендеринга!!! (пример под компонентом)
В данный момент хук работает примерно как componentDidUpdate. 
Если мы хотим вызвать метод componentWillUnmount (например отменить подписку), мы должны вернуть коллбэк отвечающий за эти действия. Однако давайте посмотрим что прозойдет если мы не напишем return.
(пример). В контексте информации выше, становится понятно почему это происходит. Каждый алерт видит свою неизменную константу и выводит его значение. Каждый рендер мы вешаем новый ивэнтлстенер на виндоу.

Функция useEffect принимает вторым аргументом массив зависимостей. В этот массив мы можем поместить переменные, и useEffect будет следить за этими значениями и отрабатывать в случае их изменения. (пример c count2: изменяем count и count2 и смотрим как отрабатывает useEffect при пустом массиве, если добавим count, если добавим count2)  
Если мы захотим использовать useEffect как copmonentDidMount - мы оставляем массив пустым, и в таком случае useEffect отработает однократно, в момент загрузки приложения. Но вообще - это не рекомендуется.
Объявлять функции использующиеся в useEffect вне useEffect не безопасно. Старайтесь объявлять эти функции внутри. (раскомментируем пример 2 и вносим функцию в useEffect) В данном случае функция объявлена снаружи, и мы должны положить ее в массив зависимостей (ведь мы помним, что она отличается при каждом рендере). После этого мы будем получать срабатываение useEffect при каждом рендере, об этом нас предупреждает eslint и предлагает либо внести функцию в useEffect либо обернуть ее в useCallback.(перемещаем функцию в useEffect) Как видим , в зависимости автоматом залетает переменная count, и теперь useEffect работает корректно.
Вообще тема useEffect-а очень объёмна, и в рамках стрима ее не рассмотреть. Для выполнения тасков этой информации будет достаточно, а позднее, когда вы уже будете чувствовать себя увереннее, я бы ОЧЕНЬ рекомендовал вам вот эту статью. https://habr.com/ru/company/ruvds/blog/445276/
И последний пример, который подведет нас к следующей теме, а заодно и еще раз доказывающий что методы жизненного цикла классов отличаются от useEffect: ( 4 setTimeout)
Функциональный компонент: как и ожидается, console.log выводит нам значение count из замыкания.
Однако классовый компонент , в отличие от функционального будет выводить нам какраз таки последнее значение. (Эти отличия достаточно часто усложняют миграцию с классов на хуки).
По хорошему, необходимо помнить эти отличия, и писать свой код с учетом их, однако иногда нам становится необходимо чтобы useEffect функционального компонента работал как componentDidUpdate классового. В этом нам поможет хук useRef. Однако данные действия обычно сигнализируют о том, что вы что-то делаете не так и использовать данный подход нужно только в крайнем случае.

3) useRef
Стандартный вариант использования useRef - работа с домом в императивном стиле. Как мы помним, React декларативен, и работать напрямую с домом через getElementById/querySelector крайне не рекомендуется. Именно для работы с домом были придуманы рефы. По сути, useRef похож на «коробку», которая может содержать изменяемое значение в своём свойстве .current. Он удобен для сохранения любого мутируемого значения. Это возможно, поскольку useRef создаёт обычный JavaScript-объект. Имейте в виду, что useRef не уведомляет вас, когда изменяется его содержимое. Мутирование свойства .current не вызывает повторный рендер.

Итак, рассмотрим стандартный случай использования рефа - работа с домом. До этого eventListener мы вешали на window. Но что, если нам необходимо повесить его на кнопку? Для этого создадим реф, и присвоим его дом элементу и отконсолим в useEffect. Итак мы видим объект, в котором в поле current лежит ссылка на дом элемент. Теперь мы можем с ним взаимодействовать. (1)(присваиваем buttonRef.current в переменную внутри useEffect и заменяем window).

Бывают ситуации, когда нам нужно передать реф в компоненты ниже. Можно сделать это с помощью пропсов. (пример buttonRef)

Относительно недавно появилась возможность передачи рефа от дочернего компонента к родительскому с помощью forwardRef. 
Пример:
создадим дочерний компонент, например NeApp и с помощью forwardRef передадим туда ref (buttonRef2) и убедимся, что это работает. 
Для чего это нужно? Например нам нужно динамически получать какие-то данные о элементе в родительском компоненте. 

Ну , и возвращаясь к теме useEffect: с помощью рефа мы можем сообщать useEffect самое свежее значение нашей переменной count и заставить useEffect работать как componentDidUpdate. Пример кода (2)