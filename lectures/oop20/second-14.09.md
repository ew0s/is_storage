# Лекция 2

## Зачем стали писать в ООП ?

### Возбмем серивис каршеринга
* Несколько типов аренды
* Информация об аренде
* Сама аренда (состоит из множества аренд)

###  Задача - нужно выстовить счет и начислить бонусы клиенту за поездку

### Как это делать в процедурном стиле
* Тут надо вставить картинку
####  Проблемы:
* Дублирование кода
* Неудобство разработки
* Непонятно, кто с кем связан, нет логической связности

### Переносим эту задачу в ООП
1. Инкапсуляция
2. Сделаем методы по подсчетам **Calc**

### Наследование и полиморфизм - в нашем коде есть проблема (switch)
* Должна быть basic-rent
  * hour - rent
  * day - rent
  * ...

* Создадим класс FixRentInfo, который наследник RentInfo
*  Сделаем метод Clac как virtual
   *  В наследниках делаем override этого метода

### Преимущества - 
* Инкапсуляция - связали данные и логику
* Тип представляется внутри класса, ввели иерархию через наследование и за счет этого избавились от enum
* Избавились от switch за счет полиморфизма

