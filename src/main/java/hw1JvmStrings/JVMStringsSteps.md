≡ домашка Netology JDPDC-3 Никитин Андрей Сергеевич

# Задача "Понимание JVM"
***
## Описание задачи
***

Описание каждой строчки с точки зрения происходящего в JVM

С учетом
* ClassLoader'ов,
* областей памяти (стэк (и его фреймы), хип, метаспейс)
* сборщика мусора


## Код для исследования
  ***
```java
public class JvmComprehension {									// 00

     public static void main(String[] args) {					// 0
        int i = 1;                      						// 1
        Object o = new Object();       							// 2
        Integer ii = 2;                 						// 3
        printAll(o, i, ii);             						// 4
        System.out.println("finished"); 						// 7
    }

     private static void printAll(Object o, int i, Integer ii) {
        Integer uselessVar = 700;                   			// 5
        System.out.println(o.toString() + i + ii);  			// 6
    }
}
```


***
Допускаем что
* используем win10 64 без Vm итд итд,
* создаем в блокноте
* без сборки итд
* запускаем javac и java чз CMD
* на HotSpot
* мы программисты true
* квантовую запутанность не распутать

Без описания
* процессора
* OS
* Ram VirtualMemory
* software
* итд итд


log:

Создаем текстовой файл с расширением .java и соотв.именем  
Вносим и сохраняем текст приведенного класса  
запускаем чз CMD компилятор javac JvmComprehension.java  
он собственно както магически компилирует в javaбайткод или чтото такое  
стартуем java JvmComprehension.class  
абстракция которой не существует "JVM Hotspot" активируется

...на сколько нам пока известно:
какаято часть класса или инфа онем в какойто форме поступает в **?**  
**Classloader**
* тут 3 раздела
  * Loading (подГрузка)
  * Linking(Связывание)
  * Inintialization (Инициализация)
* Loading имеет три "объекта"
  * l_1.1.Aplication
  * l_1.2.Extension &nbsp;  &nbsp;  &nbsp; //собсвтенно тут подгружаются всякие расширения  **?**
  * l_1.3.Bootstrap &nbsp;  &nbsp;  &nbsp; //собсвтенно тут всякие core libs JRE	итд  **?**
  * вобщем инфа о классе пробрасывается туда сюда и в итоге чз Application загружается весь класс
  * стоит отметить, что можно создать пользовательский Classloader но это не сегодня
  * стоит наверно отметить что ClassNotFoundException тут не появился
* 2 раздел Linking(Связвание)  &nbsp;  &nbsp;  &nbsp;  &nbsp;  &nbsp; //тут както готовится наш кот
* собственно тут еще три этапа
  * lnk_2.1 verifing (проверка)  &nbsp;  &nbsp; &nbsp;  &nbsp;  &nbsp; // проверка на валидность
  * lnk_2.2 preparing (подготовка)  &nbsp; &nbsp;  &nbsp;  &nbsp; //подготовка примитивов в стат.полях //инициализ стат.поля класа
  * lnk_2.3 resolving (разрш.симв. ссылок) &nbsp;  &nbsp;  &nbsp; //связывание ссылок на другие классы - наверно тут таких нет	// разрешение символьных ссылок (какието обращения к др. класслоудерам (подготовка др.классов чтоб использовать в нашем классе...
* на этап 3 Inintialization(Иниц.) &nbsp;  &nbsp;  &nbsp; //нач выполнение класса // если есть обращение к стат полям - вовзращение стат. полей // если экземпляр объекта то с помощью ключ. слова new - короч JVM будет создавать экзмпл объекта в памяти
  * //порядок
  * //с начала выполн. все static инициализаторы и инициализаторы static полей
  * //1ым идет static block - это кстати стат. инициализатор
  * //2ым идет static поля и возможно! их методы создания/инициализации
  * //стоит учитывать, что вызов статического инициализатора производится после загрузки класса и фактически до создания самого первого объекта класса.

вобщем блаблабла стартует область **JVM Memory / Runtime Data Area **
(шаг 00 в комментариях кода)  
в **Meta** загружается
* инфа о классе
* Константы   &nbsp;   &nbsp; // тут собственно таких вроде на первый взгляд нет **?**
* и еще какието 2000 классов !!! **?**
* //отсюда кстати рефлекшен все достает // размер меты регулируется XX MaxMetaspaceSize из консоли или vmoptions //собствено прямопропорционально съедает Ram


***
собственно вуаля  
jvm в какойото момент  **?** нашла точку входа т.е. main

(шаг 0 в комментариях кода):  
в момент вызова  main  создается frame(-кадр-) в **Stack** и кудато загружаются аргументы программы **?**  
кадр создается на всю информацию области видимости метода

(шаг 1 в комментариях кода):   
в Stack frame_main создается значение 1 и присваивается примитивной переменной i&nbsp;  &nbsp;// есть версия что значение прямо в регистрах процессора **?**

(шаг 2 в комментариях кода):     
в Heap резервируется место под экземпляр объекта типа Object&nbsp;  &nbsp; //  или он уже создается  **?**
в Heap создается ссылочная переменная на данный экземпляр  
в Stack frame_main создается переменная  о  типа Object и ей присваивается соответствующая ссылочна переменная

(шаг 3 в комментариях кода):  
аналогчино 2  
в итоге в Stack frame_main есть ii типа Integer

(шаг 4 в комментариях кода):  
в момент вызова  printAll  создается frame(-кадр-) в **Stack**  frame_printAll
создание нового примитива i равного i(main)  
создание ссылочных типов в Heap на экземпляры типов Object и Integer и возврат их в Stack_frame_printAll  
создание в Stack_frame_printAll переменных типов на Object и Integer и присвоением им полученных ссылочных типов (или вот этот шаг на шаг раньше **?**)

(шаг 5 в комментариях кода):  
резервирование места в Heap под  экземпляр объекта Integer и его созадние
создание переменной типа Integer в Stack_frame_printAll и возврат в нее соответствующего ссылоного типа из HEap

(шаг 6 в комментариях кода):
в момент вызова  System.out.println  создается frame(-кадр-) в **Stack**  frame_println
создается примитив переменная i в нее копируется значение i(print_all)
в Heap создаются переменные ссылочные переменные у экз  Object и Integer
в  Stack_frame_println создаются переменные типов Object и Integer и им возвращаются соотв ссылки из Heap

создается в Stack frame_toString  
создается в Heap экз объекта String и его ссылочная и переменная во фрейме и туда передается ссылочная
исполняется frame_toString
удаляется frame_toString


создается новый объект String для первой конкатенации
создается фрейм concat с двумя перменными
исполняетя
удаляетяся фрейм  concat
создается новый объект String для второй конкатенации
создается фрейм concat2 с двумя перменными
исполняется
удаляется фрейм  concat2
и если GC то помечаются String на удаление в соотв.моменты (когда нет ссылок)

удаляется фрейм  Stack_frame_println включая его примтив и перменную uselessVar
если GC запускается то ссылочная перменная и эземпляр Integer(700) маркируется на удаление // когда он удалится это вопрос  **?**  
удаляется  фрейм  Stack_frame_printAll c его примитивом i

(шаг 7 в комментариях кода):  
в момент вызова  System.out.println  создается frame(-кадр-) в **Stack**  frame_println  
в Heap создается экземпляр с соотв параметром String(finished)
в println создается перменная типа String и в нее возвращается соотв переменная ссылочного типа   
исполняется логика println
удалется frame_println с перменной String
если GC запускается то ссылочная перменная и эземпляр String(finished) маркируется на удаление

(после шаг 7 в комментариях кода):  
если GC запускается то ссылочные перменные и эземпляры Object и Integer маркируется на удаление  
наверно удаляется фрейм main c примитивом i и перменными типа Object и Integer  
или гдето тут exit(0) **?**
***
если повезет при какомто предыдущем вызове GC удаляться объекты **?** и/или  Object перемещается выше итд
***
в данной программе получается, что именно наш класс интерпретируется строка в строку, поскольку вроде так проще  
осталные 2000 классов наверно тоже или нет **?**

***

![BOOM](https://akaneovo.com/img/JVM.png "Вот такая штука эта JVM")

 