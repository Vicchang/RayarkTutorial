# Week1
---
## Please explain SOLID principles of object-oriented programming.
### 1. Single responsibility principle
#### Look at the code below. The Monster could go with specific way, which was X and Y plus one. 
    
```C#
class Program
{
    static void Main(string[] args)
    {
        GameObject GO = new GameObject();
        Monster M = new Monster(ref GO);
        M.Go();
    }
}

public class Monster
{
    private GameObject _m_gameObject;

    public Monster(ref GameObject gameObject)
    {
        _m_gameObject = gameObject;
    }

    public void Go()
    {
        _m_gameObject.X = _m_gameObject.X + 1;
        _m_gameObject.Y = _m_gameObject.Y + 1;
    }
}
```
#### However, one day, someone requested that could the monster go with different way? The engineer quickly modified the code, which would be like below.    
```C#
class Program
{
    static void Main(string[] args)
    {
        GameObject GO = new GameObject();
        Monster M = new Monster(ref GO);
        bool go_method_0 = false; bool go_method_1 = true;

        if (true == go_method_0) M.Go();
        else if (true == go_method_1) M.Go_v1();
    }
}    

public class Monster
{
    ...
    
    public void Go_v1()
    {
        _m_gameObject.X = _m_gameObject.X + 2;
        _m_gameObject.Y = _m_gameObject.Y + 2;
    }
}    
```
#### Unfotunately, someday, his boss requested the monster to go with one hundred new different ways. The engineer was stuck. The tragedy happend because it broke the "Single Responsibility Principle". The Monster class could decide if the class should go or not but should not decide how the monster go. The Monster class should leave how the monster go to other class to decide. In that case, we seperate the logic or the responsibility to other class. Below is a simple code to illustate the idea.
```C#
class Program
{
    static void Main(string[] args)
    {
        GameObject GO1 = new GameObject();
        GameObject GO2 = new GameObject();
        MoveBase Mm1 = new MonsterMove_v1(ref GO1);
        MoveBase Mm2 = new MonsterMove_v2(ref GO2);
        Monster M1 = new Monster(ref Mm1);
        Monster M2 = new Monster(ref Mm2);
        M1.Go();
        M2.Go();
    }
} 

public class Monster
{
    private MoveBase _move;

    public Monster(ref MoveBase monsterMove)
    {
        _move = monsterMove;
    }

    public void Go()
    {
        IMove_v1 m = _move as IMove_v1;
        m.Foward();
    }
}

    public class MoveBase
{
    protected GameObject _m_GameObject;

    public MoveBase(GameObject gameObject)
    {
        _m_GameObject = gameObject;
    }
}

public interface IMove_v1
{
    void Foward();
}

public class MonsterMove_v2 : MoveBase, IMove_v1
{
    public MonsterMove_v2(ref GameObject gameObject) : base(gameObject) {}

    void IMove_v1.Foward()
    {
        _m_GameObject.X = _m_GameObject.X + 2;
        _m_GameObject.Y = _m_GameObject.Y + 2;
    }
}

public class MonsterMove_v1 : MoveBase, IMove_v1
{
    public MonsterMove_v1(ref GameObject gameObject) :base(gameObject) {}

    public void Foward()
    {
        _m_GameObject.X = _m_GameObject.X + 1;
        _m_GameObject.Y = _m_GameObject.Y + 1;
    }
}
```
    
### 2. Open/closed principles
### *
### 3. Liskov substitution principle
### *
### 4. Interface segregation principle
### * 
### 5. Dependency inversion priciple
### *
## When should we use inheritance, and when should we use composition?
## What is dependency injection? What benefits does it brings?
## What does coupling means? What’s the value of Law of Demeter?
## What is side effects?
## What is Test-Driven Development (TDD)?
## Why does unit test matter? How does unit test help for refactoring?
## By the arguments above, please analyze pros & cons of the singleton pattern.
## What is Design by Contracts? Why should we crash as early as possible?
