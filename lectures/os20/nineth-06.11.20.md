# Лекция 9

---

## Взаимодействие процессов

## Что мы понимаем под понятием взаимодействие процессов
* Есть внешнее взаимодействие
  * Это когда мы пытаемся обеспечить процессами возможность обменяться данными
  * Но это проблема, ведь процессы изолированны друг от друга
  * Есть инструменты:
    * Именованные каналы pipe
    * Сокеты (это частный случай пайпа)
    * Можно использовать сигналы
  * Нужно обеспечить буферизацию сигналов или данных для передачи, ведь процессы работают асинхронно
  * Есть значительно большая проблема, когда процессы должны вынужденно выполняться и конкурировать за неразделяемый ресурс
    * Есть принтер, когда процесс работает с принтером, он посылает ему данные
      * Некий процесс р0 сформировал блок данных и послала их принтеру
      * Но процесс скинул лишь часть данных принтеру после чего был вытеснен более приоритетным процессом
      * Этот более значимый процесс тоже захотел отправить данные в принтер
        * Таким образом, принтер не будет знать, что ему делать

---
## Есть проблема: взаимоисключения 
  * Мы должны сделать так, что если 1 процесс владеет ресурсом, то никакой другой процесс не сможет владеть этим ресурсом. 
  * Взаимоисключение - 2 процесса не могу находиться в условиях одной критической секции 
## Решение
  1. Будем блокировать все другие процессы на доступ к неразделяемому ресурсу, когда запустили 1 из процессов. Это не самое лучшее решение по причине того, что оно не гибкое.
  2. Критическая секция кода относительно конкретного ресурса - та часть кода, которая осуществляет взаимодействие с ресурсами. Таким образом 2 процесса не смогут одновременно использовать неразделяемый ресурс.
     1. 2 процесса не могут находится в одних и тех же ресурсах относительно 1 критической секции
---
### Как решить задачу критической секции кода
  * Вводим понятие пролога и эпилога для процесса
    * Пролог
      * Перед тем как процессу перейти в режим критической секции кода, мы вставляем фрагмент кода, который называется прологом, в рамках которого процесс сообщит оси, что он планирует войти в критическую секцию. 
    * Эпилог
      * После того, как операционная система отдала ресурс процессу и он завершил свою работу с критической секцией, в ход вступает эпилог, который говорит операционной системе, что можно отдавать этот ресурс кому-то другому.
      * Как написать пролог и эпилог, чтобы решить проблему взаимоисключения ? 
  * Одновременно с этой задачей, нужно решить задачу прогресса
    * Условия прогресса - это значит, что невозможна ситуация, когда есть свободный ресурс. Процесс, который хочет получить доступ к этому ресурсу. И по каким-то причинам этот процесс не может получить доступ к этому ресурсу. 
  * Так же нужно решить проблему отсутствия голодания процесса
    * Есть 3 процесса, которые хотят использовать один и тот же ресурс
    * Так вот голодание ресурса - это когда алгоритм планирования выстроен так, что какой-то ресурс в этой ситуации будет голодать
  * Более того, нам нужно решить проблему отсутствия тупиков
    * Мы хотим исключить ситуацию, когда 2 процесса не могут продолжить свою работу потому что ресурсы, которые им нужны заблокированы конкурирующим процессом. И при этом эти процессы не могут освободить ресурсы, которые заняли для того, чтобы исключить блокировку этих ресурсов. Пример - кольцевой тупик. 
### Решение задачи критической секции кода
  * __Первый подход - решение на аппаратном уровне__
    * У нас есть вытесняющая многозадачность и нам нужно ее соблюдать
    * В таком случае можно на аппаратном уровне сделать запрет на прерывание 
      * Некий процесс хотел войти в критическую секцию относительно какого-то ресурса
      * В своем прологе он говорит, что хочет войти в критическую секцию, операционная система дает ему разрешение на вход
      * В этот момент операционная система запретила все прерывания
      * Теперь этот процесс не получит прерывания до тех пор пока не вызовет эпилог и выйдет и критической секции относительно нужного ему ресурса.
      * После этого этот процесс вновь станет вытесняемым. 
      * То есть суть этого решения в том, чтобы сделать 2 системных вызова в начале и в конце.
    * __Какие у этого решения проблемы__
      * Это опасно
       1. Мы полностью нарушаем работу планировщиков
       2. Если процесс сломался в критической секции (то есть он не может дойти до эпилога), то все зависнет
  * __Второй подход - программное решение__
    * В чем его сложность ? 
      1. Процессы ничего не знают друг о друге
      2. Таким образом нужны какие-то структуры данных для того, чтобы каждый процесс мог иметь какую-то информацию о том, в каком состоянии находится другой процесс
      3. Поэтому были созданы алгоритмы взаимоисключения
---
### Алгоритмы взаимоисключения
#### 1. __Алгоритм замка__
     1. Есть 2 конкурирующих процесса p0 и p1
     2. Алгоритм может работать с большим количеством процессов
```
shared int lock = 0;
P[i] () {...
  while (lock) { continue; }
  lock = 1;
  {critical section}
  lock = 0;
  ...}
```
##### В чем проблема этого алгоритма
* Два процесса могут находиться в состоянии race condition, когда они практически одновременно подошли к моменту, когда им нужно использовать критическую секцию кода. 
* При вытесняющем многозадачности, прерывание может произойти в любой момент, и если оно произойдет на этой строке кода
```
  while (lock) { continue; }
  lock = 1;
```
* P[0] процесс подошел и увидел, что lock равен 0 и готов выполнять следующую команду, так как счетчик команд инкриминируется
* В этот момент произошло прерывание
* Другой процесс так же дойдет до этих строчек кода
```
  while (lock) { continue; }
  lock = 1;
```
* И сделает lock = 1 и перейдет в критическую секцию кода
* В этот момент внутри критической секции происходит прерывание
* Процесс, который работал до этого вновь вступает в работу и так же переходит в критическую секцию, так как его счетчик команд уже прошел while
* В таком случае нарушится условие взаимоисключения, так как 2 процесса оказались в критической секции

---
#### 2. __Строгое чередование__
```
shared int turn = 0; // Это значение того процесса, которому мы будем передавать ход
P[i] {...
  while (turn != i) { continue; }
    { critical section; }
    turn = 1 - i;
...}
```
##### В чем проблема этого алгоритма
* Может нарушиться условие прогресса
  * 0 процесс выполнился и передал выполнение 1 процессу
  * 1 процесс не хочет входить в критическую секцию (sleeping mode)
  * В это время P[0] принял какой-то сигнал и ему вновь нужно выполнять критическую секцию
  * Но поскольку он отдал ход 1 процессу, то 0 процесс встанет в очередь до тех пор, пока 1 процесс не выполнит критическую секцию
  * Таким образом может получиться бесконечное ожидание 0 процесса и отсутствие прогресса
---
#### 3. __Флаги готовности__
```
shared int ready[2] = {0, 0}
P[i]() {...
  ready[i] = 1;
  while (ready[1 - i]) { continue; }
  { critical section; }
  ready[i] = 0;
...}
```

##### В чем проблема этого алгоритма
  * Может возникнуть ситуация, в которой произойдет тупик в этой части кода
  ```
  P[i]() {...
    ready[i] = 1;
  ```
  * В этот момент, когда 0 процесс поднял флаг в единицу и был прерван
  * Процесс 1 поднял свой флаг в единицу и видит, что у 0 процесса флаг тоже поднят, поэтому этот процесс будет ожидать
  * Теперь работа вновь вернулась к 0 процессу и он тоже видит, что у 1 процесса флаг поднят в единицу
  * Таким образом произойдет тупик
---
#### 4. __Алгоритм Петерсона (алгоритм взаимной вежливости)__
```
shared int ready[2] = {0, 0}
shared int turn = 0;
P[i]() {...
  ready[i] = 1;
  turn = 1 - i;
  while(ready[1 - i] && turn == 1 - i) { continue; }
  { critical section; }
  ready[i] = 0;
...}
```
##### В чем проблема этого алгоритма
* Что произойдет, если процессов станет несколько
  * Если есть 3 процесса, то передачу turn придется делать по кругу
  * Если процессов будет __n__, то накладные расходы для этого алгоритма могут стать неподъемными для операционной системы

#### В конченом итоге пришли к выводу о том, что решить задачу взаимоисключений процессоров на программном уровне сложно и нужно вновь посмотреть на аппаратный уровень
#### Какое аппаратное решение придумали
* Было замечено, что опасно делать прерывания только в каких-то конкретных местах алгоритмов
* Было бы хорошо сделать некоторые наборы команд атомарными
* Решили на уровне процессора добавить команду, которая будет выполнять такие команды атомарно
  * Она будет опрашивать значение какой-то переменной
  * Если значение равно 0, то в ответ будет возвращаться true и одновременно меняться значение этой переменной
  * Если значение переменной 1, то мы будем возвращать false и ничего не менять
* Такая команда получила название __test and set__ или __аппаратная поддержка взаимоисключения__
```
shared int lock = 0;
P[i] {...
  while (Test_And_Set (& lock)) { continue; }
  { critical section; }
  lock = 0;
...}
```