# Week1
---
* ## Please explain SOLID principles of object-oriented programming.
  ### 1. Single responsibility principle
  Look at the code below. The Monster could go with specific way, which was X and Y plus one. 
      
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
  
      public Monster(GameObject gameObject)
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
  However, one day, someone requested that could the monster go with different way? The engineer quickly modified the code, which would be like below.    
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
  Unfotunately, someday, his boss requested the monster to go with one hundred new different ways. The engineer was stuck. The tragedy happend because it broke the "Single Responsibility Principle". The Monster class decided not only Monster should go but also how the monster go. The Monster class should leave how the monster go to other class to decide. In that case, we seperate the task or the responsibility to other class. Single responsibility principle encourages that one class should only do one task. Below is a simple code to illustate the idea.
  ```C#
  class Program
  {
      static void Main(string[] args)
      {
          GameObject GO1 = new GameObject();
          MoveBase Mm1 = new MonsterMove_v1(ref GO1);
          Monster M1 = new Monster(ref Mm1);
          M1.Go();
      }
  } 
  
  public class Monster
  {
      private MoveBase _move;
  
      public Monster(MoveBase monsterMove)
      {
          _move = monsterMove;
      }
  
      public void Go()
      {
          IMove_v1 m = _move as IMove_v1;
          m.Foward();
      }
  }
  
  public abstract class MoveBase
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
      public MonsterMove_v2(GameObject gameObject) : base(gameObject) {}
  
      void IMove_v1.Foward()
      {
          _m_GameObject.X = _m_GameObject.X + 2;
          _m_GameObject.Y = _m_GameObject.Y + 2;
      }
  }
  ```
  
  ### 2. Open/closed principles
  The basic idea is that "a module or clsaa is open for extension and close for modification". As you can see from above, Monster class is now responsible for one task. It is now easy to extend new move method without touching the original one. Please read the example below.
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
  
  public class MonsterMove_v2 : MoveBase, IMove_v1
  {
      public MonsterMove_v2(GameObject gameObject) : base(gameObject) {}
  
      void IMove_v1.Foward()
      {
          _m_GameObject.X = _m_GameObject.X + 2;
          _m_GameObject.Y = _m_GameObject.Y + 2;
      }
  }
  
  public class MonsterMove_v1 : MoveBase, IMove_v1
  {
      public MonsterMove_v1(GameObject gameObject) :base(gameObject) {}
  
      public void Foward()
      {
          _m_GameObject.X = _m_GameObject.X + 1;
          _m_GameObject.Y = _m_GameObject.Y + 1;
      }
  }
  ```
  Now we have MonsterMove_v1 and MonsterMove_v2, we only need to decide which method should we use when we initialize Monster.
  
  ### 3. Liskov substitution principle
  Let's look at the following example.
  ```c#
  class Program
  {
      static void Main(string[] args)
      {
          GameObject GO1 = new GameObject();
          GameObject GO2 = new GameObject();
          MoveBase Mm1 = new MonsterMove_v1(GO1);
          Monster Monster = new Monster(Mm1);
          Dog Dog = new Dog(Mm1);
  
          Monster.Go();
          Dog.Go();
      }
  }
  
  public class Monster
  {
      private MoveBase _move;
  
      public Monster(MoveBase monsterMove)
      {
          _move = monsterMove;
      }
      
      public void Go()
      {
          MonsterMove_v1 m = _move as MonsterMove_v1;
          if (null != m) {
              m.Foward();
              Console.WriteLine("I am Monster. I go with " + m.Show());
          }
      }
  }
  
  public class Dog
  {
      private MoveBase _move;
  
      public Dog(MoveBase dogMove)
      {
          _move = dogMove;
      }
      
      public void Go()
      {
          MonsterMove_v1 m = _move as MonsterMove_v1;
          if (null != m) {
              m.Foward();
              Console.WriteLine("I am Dog. I go with " + m.Show());
          }
      }
  }
  
  public class MonsterMove_v1 : MoveBase, IMove_v1
  {
      ...
      
      public string Show()
      {
          return "Monster Walk.";
      }
  }
  ```
  The console will show "I am Monster. I go with Monster Walk." and "I am Dog. I go with Monster Walk.". To some people, there is nothing strange. A dog can walk like a monster is really cool. However, if your design is that a dog would never walk like a monster, there is something wrong on your design. The design breaks "Liskov Substituion Principle", which is "Subtypes must be substitutable for their base types". In this case, Dog should not accept MoveBase parameter type but a more precise parameter type, for example, DogMoveBase. And Monster, vise versa. The contradiction would be solved.
  
  ### 4. Interface segregation principle
  ### * 
  ### 5. Dependency inversion priciple
  ### *
* ## When should we use inheritance, and when should we use composition?
  A property is belong to the class and the property can be shared to many similar classes. In this case, the property would be extracted from the class and keep in the super class. At that time, inheritance is used. Taking below as an example.
  ```c#
  public abstract class Animal 
  {
      object left_front_foot, right_front_foot;
      object left_back_foot, right_back_foot;
  }
  
  public class Cat : Animal
  {
  }
  
  public class Pig : Animal
  {
  }
  ```
  Cat should be born with 4 feet. That is its nature. The feet is not pluged by other and they are rarely changed. Hence, the inheritance is used to deal with this case. 
  Compisition is more like a "Has a" relationship. That's say cats are born with 4 feets (Most of the time). Some of the cats has clothing but some don't. In this scenario, we should use compisition to handle clothing. The clothing is worn by cat but not native to cat. Here is the example.
  ```C#
  public class Clothing
  {
      public int color;
  }
  
  public class Cat : Animal
  {
      private Clothing _m_Clothing;
      public Cat(Clothing clothing)
      {
          _m_Clothing = clothing;
      }
  }
  ```
  The idea of compisition and inheritance is that the object is native to the class, you should use inheritance while the object is not combined with the class, you should use compisition.
  Here is another example to illustrate the idea. We can have a monster class composite with gameObject or inheritant with gameObject. However, the meaning is different.
  ```C#
  public class Monster
  {
      private IMove_v1 _move;
      private GameObject _m_GameObject;
  
      public Monster(IMove_v1 monsterMove, GameObject gameObject = null)
      {
          _move = monsterMove;
          _m_GameObject = gameObject;
      }
  
      public void Go()
      {
          _move.Foward(_m_GameObject);
      }
  }
  ```
  This represent that a monster class has a gameObject or not. If it has gameObject, it can mimic like a gameObject. If it doesn't have gameObject, it can be a real monster or other else.
  ```C#
  public class Monster : GameObject
  {
      private IMove_v1 _move;
  
      public Monster(IMove_v1 monster)
      {
          _move = monster;
      }
  
      public void Go()
      {
          _move.Foward(this);
      }
  }
  ```
  If monster class is subclass of gameObject, it would be part of an gameObject forever. There is no way to be a real monster or else. Even if you do so, it is still a real monster gameObject.
  
* ## What is dependency injection? What benefits does it brings?
  The dependency injection would occur in compisition, that is "has a" case. Take the above as example. Monster class has a gameObject. The key of DI is that who generate the gameObject. Is it Monster class or it is by other class. Let's look at the code below.
  ```C#
  public class Monster
  {
      private IMove_v1 _m_move;
      private GameObject _m_gameObject;
  
      public Monster(IMove_v1 monsterMove)
      {
          _m_move = monsterMove;
          _m_gameObject = new GameObject();
      }
  
      public void Go()
      {
          _m_move.Foward(_m_gameObject);
      }
  }
  ```
  This class can work perfectly, but it is lack of flexibility. As you can see, the class depends on a concreate class, GameObject. What happens if one day the gameObject becomes a different gameObject, for example, gameObject2D? You have to re-code all of the code to make it possible. Here is the modified code.
  ```C#
  public class Monster
  {
      private IMove_v1 _m_move;
      private GameObject _m_gameObject;
  
      public Monster(IMove_v1 monsterMove, GameObject gameObject)
      {
          _m_move = monsterMove;
          _m_gameObject = gameObject;
      }
  
      public void Go()
      {
          _m_move.Foward(_m_gameObject);
      }
  }
  
  public class GameObject2D : GameObject
  {
  }
  
  public static void CreateMonster
  {
      GameObject2D gameObject2D = new GameObject2D();
      MonsterMove_v1 mMonster_v1 = new MonsterMove_v1();
      Monster monster = new (mMonster_v1, gameObject2D);
  }
  ```
  We loose the coupling between GameObject and Monster. We can change the implmentation of gameobject at any time without re-coding. This is powerful. Imagine that you are writing unit test. You want to stub some of the case since it doesn't need to be test during the unit test. With dependency on concrete class, you can't do it, but on base/abstract class, you can.
  
* ## What does coupling means? What’s the value of Law of Demeter?
  As long as a class has the association relationship with the other class, they are coupled. Coupling is used to descripe this situation. Note that assciation includes aggregation, compistion, inheritence, implementation and ...etc.
  The idea of Law of Deter is that one class should know as litter as other class and do not use the other class's member variable to do the other work. Here is the example.
  ```C#
  class Program
  {
      static void Main(string[] args)
      {
          GameObject gameObject = new GameObject();
          MonsterMove_v1 mMove_v1 = new MonsterMove_v1();
          Monster monster = new Monster(mMove_v1, gameObject);
  
          monster.Go();
          monster.Move.Foward(gameObject);
      }
  }
  
  public class Monster
  {
      private IMove_v1 _m_move { get; }
      private GameObject _m_gameObject;
  
      public IMove_v1 Move
      {
          get { return _m_move; }
      }
      public Monster(IMove_v1 monsterMove, GameObject gameObject)
      {
          _m_move = monsterMove;
          _m_gameObject = gameObject;
      }
  
      public void Go()
      {
          _m_move.Foward(_m_gameObject);
      }
  }
  ```
  What is the difference between "monster.Go()" and "monster.Move.Foward(gameObject)"? Actually, the functionality is all the same, but if you draw the UML diagram, you can find that the dependency is different. For "monster.Go()", main has a monster. For "monster.Move.F(gameObject)", main has a monster and use IMove_v1 class. What's the influence? As the denpendency becomes more and more, it would be more difficult to modify the class. That is the problem of high coupling.
  
* ## What is side effects?
* ## What is Test-Driven Development (TDD)?
* ## Why does unit test matter? How does unit test help for refactoring?
* ## By the arguments above, please analyze pros & cons of the singleton pattern.
* ## What is Design by Contracts? Why should we crash as early as possible?
