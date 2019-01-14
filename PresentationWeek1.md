# Week1 Software Engineering
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
  When class extends an interface, you should garuntee that there is no interface method not used by the class. A interface with unused method can highly represent that you don't seperate the interface coreectly.
  You might break the single responsibility principle. 
  ```C#
  public class MonsterMove : MoveBase, IMove_v1
  {
      public MonsterMove(GameObject gameObject) : base(gameObject) {}
  
      void IMove_v1.Crawl()
      {
          _m_GameObject.X = _m_GameObject.X + 2;
          _m_GameObject.Y = _m_GameObject.Y + 2;
      }
	  
	  void IMove_v1.Fly()
	  {
	      throw new System.InvalidOperationException("Monster can not fly.");
	  }
	  ...
  }
  
  public class BirdMove : MoveBase, IMove_v1
  {
      public BirdMove(GameObject gameObject) :base(gameObject) {}
  
      public void Fly()
      {
          _m_GameObject.X = _m_GameObject.X + 4;
          _m_GameObject.Y = _m_GameObject.Y + 4;
      }

	  public void Crawl()
	  {
		   throw new System.InvalidOperationException("Bird can not crawl.");
	  }	  
	  ...
  }
  
  public interface IMove_v1
  {
      void Walk();
	  void Crawl();
	  void Fly();
  }  
  ```
  This would be awkard since you extend an interface, but you throw exception since the method is not used in the concrete class. This is an example of breaking "Interface Segregation Principle".
  In this case, IMove_v1 interface has too many responsibility than it was designed.
  ```C#
  public interface IMove_v1
  {
      void Walk();
  }  
  
  public interface IMove_CanFly : IMove_v1
  {
      void Fly();
  }
  
  public interface IMove_CanCrawl : IMove_v1
  {
      void Crawl();
  }
  ```
  As long as we seperate the interface to two sub interface, the awkard code would no longer exists and the interface IMove_v1 interface now has only responsibility.
  
  ### 5. Dependency inversion priciple
  High level modules should not depend on low level modules. This can greatly illustrate the principle. What kind of implmentation is that high level modules depends on low level module? Take the code below as example.
  ```C#
  public class Monster
  {
      private IMove_v1 _m_move;
      private GameObject _m_gameObject;
  
      public Monster()
      {
          _m_move = new MonsterMove_v1();
          _m_gameObject = new GameObject();
      }
  
      public void Go()
      {
          _m_move.Foward(_m_gameObject);
      }
  }  
  ```
  Monster class depends on MonsterMove_v1 class and GameObeject class. The idea of the principle is that we want to invert the scenario. The basic method is that instead of high level module creates low level modules,
  high level module holds a reference to low level modules and let other pass the low level modules to high level modules. 
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
  ```
  What are the benefits? High level modules de-couple to low level modules. That is really powerful. As long as a system is less coupling, it is more stable and flexibility.
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
  The dependency injection would occur in compisition, that is "has a" case. Take the above as example. Monster class has a gameObject. The key of DI is that who generate the gameObject. Is it Monster class or other class? Let's look at the code below, which monster class generate gameObject.
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
      Monster monster = new Monster(mMonster_v1, gameObject2D);
  }
  ```
  We loose the coupling between GameObject and Monster. We can change the implmentation of gameobject at any time without re-coding. This is powerful. Imagine that you are writing unit test. 
  You want to stub some of the case since it doesn't need to be test during the unit test. With dependency on concrete class, you can't do it, but on base/abstract class, you can.
  
* ## What does coupling means? What’s the value of Law of Demeter?
  As long as a class has the association relationship with the other class, they are coupled. Coupling is used to descripe this situation. Note that association includes aggregation, compistion, inheritence, implementation and ...etc.
  The idea of Law of Deter is that one class should know as litte as other class and do not use the other class's member variable to do the other work. Here is the example.
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
  What is the difference between "monster.Go()" and "monster.Move.Foward(gameObject)"? Actually, the functionality is all the same, but if you draw the UML diagram, you can find that the dependency is different. 
  For "monster.Go()", main has a monster. For "monster.Move.F(gameObject)", main has a monster and use IMove_v1 class. What's the influence? As the denpendency becomes more and more, it would be more difficult to modify the class. 
  That is the case to casue high coupling and break Law of Demeter. 
  
* ## What is side effects?
  The definitino is 
    A function or expression is said to hava a side effect if it modifies some state outside is local envirnment, that is to say has an observable interaction with the outside world besides returning a value.
  To understand it, we could look at "pure function", which is the opposite of "side effect". A pure function is that
    A function's return value is the same for the smae arguments and its evaluation has no side effects.
  Below is the example of pure function.
  ```C#
  class Program
  {
      static void Main(string[] args)
      {
          int a = 10;
          int b = 20;
          int sum = addOnenSum(a, b);
      }
  
      public static void addOnenSum(int a, int b) 
      {
          a = a + 1;
          b = b + 1;
          return a+b;
      }
  }
  ```
  From above, after executing "addOnenSum", variable a and variable would stiil the same, that is "a" is still 10 and "b" is still 20. In this case, "addOnenSum" function is a pure function. Here is the example of side effect.
  ```C#
  class Program
  {
      static void Main(string[] args)
      {
          int a = 10;
          int b = 20;
          int sum = addOnenSum(ref a, ref b);
      }
  
      public static void addOnenSum(ref int a, ref int b) 
      {
          a = a + 1;
          b = b + 1;
          return a+b;
      }
  }  
  ```
  After executing "addOnenSum", variable a is 11 now and variable b is 21. the "addOnenSum" function has modified the state of a and b variable outside of its scope. That is side effect.
* ## What is Test-Driven Development (TDD)?
  In order to show the idea of TDD. We need to know what is unit test first. A unit test is that set of tests to test the smallest testable part of an application. TDD is to develope according to the set of tests so as to create solid program.
  TDD usually includes four steps. First, define a test set for the unit. Second, create the unit tests and make them fail. Third, implement the program logic. Forth, verify the implementation of the unit makes the tests succeed.
  
* ## Why does unit test matter? How does unit test help for refactoring?
  The advantage of unit test can describe why does unit test matter. 
  1. Unit test can solid your idea. If your implementation can pass the unit tests, you have better chance to be in the correct path. 
  2. Unit test is like a safty net. While you extend your functionality, unit test is there to garuntee that your original function work as usual. This helps you focus on your new implementation.
  3. Unit test can help you implement a more flexable system. A unit testable code means that it has less coupling, which is hgih flexibility.
  
  The most terrible thing for refactoring is that you have no idea that the program works as usual after refactoring. As mentioned above, unit test stands a great role in refactoring. It helps you quickly notice that if the program could work as usual or not.
  
* ## By the arguments above, please analyze pros & cons of the singleton pattern.
  Singleton pattern has its benefits while programing. It garuntees that the object would never be created twice. That is quite powerful. If a object is designed to create only once, there would be many undefined behaviours happen if you create it twice and those are really hard to debug. 
  The other benefit is that you can access the object all over the program. That is you can use the object or its method any time you want. Isn't that cool? However, it is a benefit but it is also a disadvantage. A class using the singleton object means that the class is coupled to a 
  concreate object. It is really harmful since it forbids unit test and makes the class inflexible. If a class depends on a concrete class, it is impossible to divide to testable unit and hence, hard to test and extend. Take below as example. Assuming we have a singleton Cat class.
  ```C#
  class Program
  {
      static void Main(string[] args)
      {
          Cat UniqueCat = Singleton<Cat>.getInstance();

          ChangeName();
      }

      public static void ChangeName()
      {
          Cat SuperCat = Singleton<Cat>.getInstance();
          SuperCat.ChangeName("Super Cat");

          SuperCat.ShowName();
      }
  }  
  
  public class Singleton<T>
  {
      static T instance;
      private static readonly object padlock = new object();

      public static T getInstance()
      {
          if (null == instance)
          {
              lock (padlock)
              {
                  if (null == instance)
                  {
                      Type type = typeof(T);
                      ConstructorInfo ctor = type.GetConstructor(
                                                  BindingFlags.Instance | BindingFlags.NonPublic,
                                                  null, CallingConventions.Any, new Type[0], null);
                      instance = (T)ctor.Invoke(new object[] { });
                  }
              }
          }

          return instance;
      }
  }
  ```
  The ChageName function could not be tested since we directly access the singleton object in the function. This causes the function couples to concrete class. Fortunately, we can use denpendency injection to solve the problem. Check the code below.
  ```C#
  public static void ChangeName(Animal animal)
  {
      animal.ChangeName("Super Cat");

      animal.ShowName();
  }
  
  public class Cat : Animal
  {
      private string _name = "cat";
      private Cat() { }

      public override void ShowName()
      {
          Console.WriteLine("I am a " + _name);
      }
      public void ChangeName(string strInput)
      {
          _name = strInput;
      }
  }
  ```
  Now ChangeName function de-couples to the concrete class but couples to abstract class. If you want to test the unit, you can simply inject a stub and focus on the functionality of the function itself. 
  The other problem of singleton is due to the "static" attribute. If a object is describe as static, it can be accessed withing the scope of the library or execute file. That says if a static variable is declare in one dll. It can not be used in the otehr dll.
  For example, A process loads B dll and C dll. B and C share a same static library, which declares static variable D. Variable D in B and Variable D in C are different. Singleton has the same issue since it is implemented by static variable.
  
* ## What is Design by Contracts? Why should we crash as early as possible?
  While implementing a feature, the most important thing is to do things right. We'd like to garuntee that engineers can code the same as what they think. They can think the same as what the specs define.
  Hence, there are lots of tool to help engineer to do things right. For example, we have detailed desgin document, code review, spec documnet, unit test, quality assurance and ... etc.
  Design by contracts is another concept or framework to help engineers to code correctly. Whenever, engineer design implement a new class or function. There are preconditions, invariants and postconditions to
  the function or class. The Dbc would like engineer to design those conditions before implementation. By doing so, you have more confidents on what you code. Basicly, it is not a new idea. 
  Image the case that you are passing a string to a function, what you do is to write a if-else to check the input is null or not. Constract is somthing similar to that. It would write log on run time, 
  throw exception on compile time or write as comments by different implementation of the framework. Take the below case from MSDN as example.
  ```C#
  public static string GetCustomerPassword(string customerID)
  {
      Contract.Requires(!string.IsNullOrEmpty(customerID), "Customer ID cannot be Null");
      Contract.Requires<ArgumentNullException>(!string.IsNullOrEmpty(customerID), "Exception!!");
      Contract.Ensures(Contract.Result<string>() != null);
      string password = "AAA@1234";
      if (customerID != null)
      {
          return password;
      }
      else
      {
          return null;
      }
  }
  ```
  The contract checks the precondition that if the input string is null. If it is null, it would throw exception on compile time. This could help the engineer to find the logic defect quickly. Here is another example 
  about invariants.
  ```C#
  [ContractInvariantMethod]
  protected void ObjectInvariant()
  {
      Contract.Invariant(!string.IsNullOrEmpty(customerId));

  }
  ```
  This garuntees that no matter before or after the function call, customerId is always not null. 
  
  Why should we crash as early as possible? I think as long as you can define when would a crash happen. The anwser would be trivial. A crash happens when the system is in an unrecoverable state. 
  So if we can perfectly write down a system to finite state machine diagram. We can find that there is sink state. In that state, there is no way to transfer to other states. At that case, we crash since no matter what
  you do, you are still in that state and no way to leave. Why is this useful? A crash doesn't say that "oh! The system throws exception. We should crahs it since something abnormal happens!" but that " The system now is 
  in an unrecoverable state, please restart the system." There is another benefit for crashing earlier. It can leave more clues for trouble shootings. Since the system just make the transfer to the sink state,
  it should have more clues comparing to the time after.