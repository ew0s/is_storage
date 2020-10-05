# Лекция 4
## SOLID принципы
* SRP - принцип единой ответственности
* OCP - The Open Closed Principle
  * Система должна быть открыта для расширения, но закрыта для модификации
  * Имеемтся в виду использование полиморфизма, то есть расшиирение через новую имплементацию интерфейса, либо через расширение интерфейса
  * Примеры
```CSharp
class Shape
{
    public string Type {get; set;}
}

class Circle : Shape
{
    public Circle()
    {
        Type = "Circle";
    }
}

class Rectangle : Shape
{
    public Rectangle
    {
        Type = "Rectangle";
    }
}

class AreaCalculator
{
    public double Calc(Shape s)
    {
        if (s.Type == "Circle") return 1; //pi * r^2
        else if (s.Type == "Rectangle") return 2; // a * b
        return 0;
    } // Тут ошибка
}

class Program
{
    static void Main()
    {
        var c = new Circle();
        var r = new Rectangle();
        var ac = new Area
    }
}
```
```CSharp
interface IShape
{
    double CalcArea();
}

class Circle : IShape
{
    private double _r;

    public Circle(double r)
    {
        _r = r;
    }

    public double CalcArea()
    {
        return Math.PI * _r * _r;
    }
}

class Rectangle : IShape
{
    private double _a;
    private double _b;

    public Rectangle(double a, double b)
    {
        _a = a;
        _b = b;
    }

    public double CalcArea()
    {
        return _a * _b;
    } 
}

class AreaCalculator
{
    public double Calc(IShape s)
    {
        return s.CalcArea();
    }
}

class Program
{
    static void Main()
    {
        var c = new Circle();
        var r = new Rectangle();
        var ac = new AreaCalculator();
        ac.Calc(c);
    }
}
```
* Принцип подстоновки Лисков
  * Производный класс должен быть взаимосвязен с базовым классом.
```CSharp
interface IShape
{
    double CalcArea();
}

class Rectangle : IShape
{
    public virtual int Width {get; set;};
    public virtual int Height {get; set:};

    public Rectangle(int w, int h)
    {
        Width = w;
        Heght = h;
    }

    public double CalcArea()
    {
        return 2;
    } 
}

public Square : Rectangle
{
    public override int Height
    {
        get => base.Height;
        set
        {
            base.Height = value;
            base.Width = value;
        }
    }

    public Square(int w, int h) : base(w, h)
    {
        Width = w;
        Heght = h;
    }

    public Square(int a) : base(a, a)
    {}
}

class AreaCalculator
{
    public double Calc(IShape s)
    {
        return s.CalcArea();
    }
}

class Program
{
    static void Main()
    {
        //var r = new Rectangle(4, 8);
        var r = new Square(5);

        r.Heght = 12;
        r.Width = 5;
        Console.WriteLine(r.CalcArea() == 60);
    }
}
```
```CSharp
interface IShape
{
    double CalcArea();
}

class Rectangle : IShape
{
    public virtual int Width {get; set;};
    public virtual int Height {get; set:};

    public Rectangle(int w, int h)
    {
        Width = w;
        Heght = h;
    }

    public double CalcArea()
    {
        return 2;
    } 
}

class Square
{
    public int Side {get; set;};

    public Square(int w, int h)
    {
        Width = w;
        Heght = h;
    }

    public Square(int a) : base(a, a)
    {}
}

class AreaCalculator
{
    public double Calc(IShape s)
    {
        return s.CalcArea();
    }
}

class Program
{
    static void Main()
    {
        //var r = new Rectangle(4, 8);
        var r = new Square(5);

        r.Heght = 12;
        r.Width = 5;
        Console.WriteLine(r.CalcArea() == 60);
    }
}
```
  * Предварительные условия не могут быть усилины в подтипе
  * Постусловия не могут быть ослаблены в подтипе
  * Инварианты супертипа должны быть сохранены в подтипе.
    >Клиент не должен знать, использует он базвый класс или какой-то из его подтипов(поведение должно быть ожидаемым)
* ISP - Interface Segregation Principle - принцип разделения интерфейсов
```CSharp
interface IHaveWork
{
    double CalcWork();
}

interface IHaveSleep
{
    double CalcSleep();
}

class Human : IHaveSlepp, IHaveWork
{
    double CalcWork();
    double CalcSleep();
}

class Robot : IHaveWork
{
    double CalcWork();
}
```
* DIP - The Dependency Inversion Principle - Принцип инверсии зависимостей
  * Зависимости строятся на абстракциях (интерфейсах).
  * Модули более высоких уровней (бизнес - логики) не должны от модулей более низкого уровня
```CSharp
class MySqlConnection
{
    public MySqlConnection()
    {

    }

    public void Connect()
    {
        Console.WriteLine("Connetcted to MySQL")
    }
}

class SomeClass
{
    MySqlConnection _connecttion;

    public SomeClass (MySqlConnection connection)
    {
        _connecttion = connection;
        _connecttion.Connect();
    }
}

class Programm
{
    static void Main(string[] args)
    {
        MySqlConnection conn = new MySqlConnection();
        SomeClass
    }
}