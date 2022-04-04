≡ домашка Netology / JDPDC-3 / jvCore / JVM / VusualVM / Никитин Андрей Сергеевич

# Задача "Исследование JVM через VisualVM"
***
## Описание
***

Изучение использования памяти через `VisualVM` при загрузке новых классов и создании новых объектов
на основании следующего [проекта](https://github.com/Arsennikum/jvm-visualvm-experience "проект на гите")  

**Цель работы:**  
Следует сопоставить и создать описание зависимости между этапами программы и графиками метрик VisualVM.  
Этапы данной программы выводятся в консоль сообщениями.
Описанию подлежат графики следующих метрик: `Heap`, `Metaspace` и `Classes`

## Решение
***
### Изображения №№1,2 и 3 "Скрины VM"
![Meta](hw2JvmVisualVm/pics/sc_(34)_meta.png "Meta")  
![Heap](hw2JvmVisualVm/pics/sc_(33)_heap.png "Heap")  
![Memory](hw2JvmVisualVm/pics/sc_(32)_15mln_Objects.png "Memory")  

***
### Листинг с пояснениями 
`0:10:00: Executing task 'JvmExperience.main()'...`

`Task :compileJava UP-TO-DATE`  
`Task :processResources NO-SOURCE`  
`Task :classes UP-TO-DATE`  

`Task :JvmExperience.main() `  
данные сообщения, я так думаю, мы рассматривать пока не будем - "типо стартуем"
  
  
`00:10:00.666338400  Please open 'ru.netology.JvmExperience' in VisualVm`  
по сути - старт main


`00:10:30.694640800: loading io.vertx`  
`00:10:30.992827800: loaded 529 classes`
создаем в Stack фрейм loadToMetaspaceAllFrom
записываем в Meta 529 классов NIO client sever фреймворка Nitty
(отражено в Monitor Meta) - M01
(отражено в Monitor Classes) -C01
(отражено в Monitor Heap) -H00

`00:10:33.992986900: loading io.netty`  
`00:10:34.541292500: loaded 2117 classes`
записываем в Meta 2117 классов NIO client sever фреймворка Nitty
(отражено в Monitor Meta) - M02
(отражено в Monitor Classes) -C02

`00:10:37.545175500: loading org.springframework`  
`00:10:37.763820300: loaded 869 classes`
записываем в Meta 869 классов фреймворка "дЖава Упрощенки" Spring
(отражено в Monitor Meta)- M02  
(отражено в Monitor Classes) -C02

`00:10:40.777854600: now see heap`  
`00:10:40.777854600: creating 5000000 objects`  
`00:10:41.027758900: created`  
инициализируем в Stack фрейме main переменную типа List&lt;SimpleObjects&gt;  
создаем в Stack фрейм createSimpleObjects 
инициализируем в Heap 50млн экземпляров объектов типа Integer  
(отражено в Monitor Heap) -H01   

`00:10:44.040976900: creating 5000000 objects`  
`00:10:44.243953300: created`  
инициализируем в Heap еще 50млн экземпляров объектов типа Integer  
(отражено в Monitor Heap)-H02

`00:10:47.307690300: creating 5000000 objects`  
`00:10:47.512290600: created`  
инициализируем в Heap еще 50млн экземпляров объектов типа Integer  
(отражено в Monitor Heap)-H03  
(отражено в Sampler Memory LiveObjects)-LO01

`BUILD SUCCESSFUL in 50s`  
`2 actionable tasks: 1 executed, 1 up-to-date`  
`0:10:51: Task execution finished 'JvmExperience.main()'.`  
тут какбы все заканчивается и хорошо








