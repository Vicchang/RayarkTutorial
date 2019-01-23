# Week 2 Programming Patterns
---
* ## What’s different between value type and reference type?
  The easiest way to distinguish them in C# is that value type usually is in stack while, reference type is always in heap.
  ![Value Type vs Reference Type](https://github.com/Vicchang/RayarkTutorial/blob/master/TypeCmpDiagram.jpg)
  
  However, that is not absolutely right. A value type could be in heap, too. Below is the detail description about value type and reference type.
  ### Value Type: 
  1. Usually stored in stack.
  2. A value type usually is not null. In C#, a value type could be defined as "Type?" to  nullable type. It is still value type since nullable types are instance of System.Nullable<T> struct.
  3. In C#, struct, int and other primitive types, except string are all value type.
  4. In C#, the copy constructor of value type is deep copy (It is due to immutable type, instead of value type).
  5. In C#, a value type argument is always called by value, except using "ref" keyword. The "ref" keyword would force the argument to be passed by reference. Let's see the code below.
  ```C#
  // Example without "ref" keyword
  static void Main(string[] args)
  {
      int a = 10;
      addOne(a);
      Console.WriteLine("a is " + a); // a is 10;
  }
  
  static void addOne(int a)
  {
      a += 1;
  }
  ```
  ```C#
  // Example with "ref" keyword.
  static void Main(string[] args)
  {
      int a = 10;
      addOne(ref a);
      Console.WriteLine("a is " + a); // a is 11;
  }
  
  static void addOne(ref int a)
  {
      a += 1;
  }
  ```
  6. A reference type assigns to a value type would still be deep copy since it uses the copy constructor of value type. 
  ```C#
  class Program
  {
      static void Main(string[] args)
      {
          int a = 10;
          A m_A = new A(ref a);
          Console.WriteLine("m_A is " + m_A); // a is 10;
          a += 1;
          Console.WriteLine("m_A is " + m_A); // a is 10;           
      }
  
  }
  public struct A
  {
      public int Value { get; set; }
      public A(ref int value)
      {
          Value = value;
      }
  }
  ```    
  7. A value type could be in heap. The main factor about in heap or stack is by the creator. Below are two examples for value type in heap and in stack.
  ```C#
  static void Main(string[] args)
  {
      int a = 10; // a is value type and a is in stack.
  }
  ```    
  ```C#
  class Program
  {
      static void Main(string[] args)
      {
          A m_A = new A(10); 
      }
  
  }
  
  public struct A
  {
      private int _value; // _value is a value type and _value is stored in heap.
      public A(int value)
      {
          _value = value;
      }
  }    
  ```
  7. In C#, A value type in stack will be deleted immediately as long as the stack pop-up, while a value type in heap would be deleted by "Garbage Collection".
  ### Reference Type: 
  1. Always stored in heap.
  2. In C#, a reference type could be null. A reference type without initialization is assigned to null.
  3. In C#, class and string are reference type.
  4. In C#, the copy constructor of a reference type, except string, is shadow copy.
  5. In C#, a reference type argument is always call by value, except using "ref" keyword. Any one holds the reference and modify the object would affect the other reference holder since arguments deep copy the paramter and the paramter only hold the address of the object, instead of the object instace. This cause the object instacne is not deep copied but shared. Take below as example.
  ```C#
  class Program
  {
      static void Main(string[] args)
      {
          A m_A = new A(10);
          Console.WriteLine("m_A.Value is " + m_A.Value); // m_A.Value is 10;
          A m_B = m_A;
          Console.WriteLine("m_B.Value is " + m_B.Value); // m_B.Value is 10;           
          m_A.Value = 20;
          Console.WriteLine("m_A.Value is " + m_A.Value); // m_A.Value is 20;
          Console.WriteLine("m_B.Value is " + m_B.Value); // m_B.Value is 20; 
      }
  }
  public class A
  {
      public int Value { get; set; }
      public A(int value)
      {
          Value = value;
      }
  }
  ```
  * Note: If you use VS debug to check the m_A and m_B, you would find that both of them point to the same address. Note that the address of m_A in stack is 0x00000075e078e0e0, while address of m_B is 0x00000075e078e0e8. Both of them point to 2088014197656(0x000001e627583398).
  
  ![Address Debug](https://github.com/Vicchang/RayarkTutorial/blob/master/ReferenceDebugging.jpg)   
  ![Memory Diagram](https://github.com/Vicchang/RayarkTutorial/blob/master/ReferenceTypeDiagram.jpg)
  
  6. The "ref" keyword to reference type is to pass the reference of the reference. If you pass a reference type to a function and new the object, it would not affect the original one. Since paramters are passed by value, it is a differenct object which hold the same reference to the reference object. However, if you pass the reference of reference type to the function by "ref", you can control the real reference and change the object it refer to. Take below as example.
  ```C#
  static void Main(string[] args)
  {
      A m_A = new A(10);
      Console.WriteLine("m_A.Value is " + m_A.Value); // m_A.Value is 10;
      NewA(m_A);                                      // Pass by value. 
      Console.WriteLine("m_A.Value is " + m_A.Value); // m_A.Value is 10;
  }
  
  static void NewA(A instance) // local variable hold the copy of m_A reference in this case, also holds the reference to the m_A instacne.
  {
      instance = new A(20); // local variable new. m_A is still the same because this is not m_A but a copy of it.
  }    
  ```
  ```C#
  static void Main(string[] args)
  {
      A m_A = new A(10);
      Console.WriteLine("m_A.Value is " + m_A.Value); // m_A.Value is 10;
      NewA(ref m_A);                                      // Pass by value. 
      Console.WriteLine("m_A.Value is " + m_A.Value); // m_A.Value is 20;
  }
  
  static void NewA(ref A instance) // local variable refers of m_A reference and m_A reference holds the reference to the m_A instacne.
  {
      instance = new A(20); // local variable refers to m_A and trigger m_A to new a new instance and point to.
  }       
  
* ## What’s immutable type? And why should string be immutable?
  Immuatable type can be explained by its name lol. A immutable object would never be changed, except initialization. What's the benefit? Some object is designed to be unchanged. With this attribute, it can help engineer not to modify the object accidently.
  
  The string in C# is immutable is due to that the string is a reference type. A reference type means that you could modify the value any time as long as you hold the reference. It is really risky if string is not immutable. Imagine the case below.
  ```C#
  static void Main(string[] args)
  {
      System.Text.StringBuilder str = new System.Text.StringBuilder();
      str.Append("123");
      str.Append("11111111");
      Strconstructor();

      string strA = "456";
      Console.WriteLine("Before strA is " + strA);  //Before strA is 456
      strA.Replace('4','6');
      Console.WriteLine("strA is " + strA);         //Before strA is 456


      hi A = new hi(1);
      Console.WriteLine("Before A is " + A._a);     / Before A is 1
      A._a = 2;
      Console.WriteLine("A is " + A._a);            // A is 2
  }
  ```
  ~~If string is not immutable type, the console should print "strA is 656". That is terrible. A class is just share the string to other class, but somehow, other class can modify the string and effect the sender class. Fortunately, it shows "str is 123" due to the string in fucntion refers to the new string, instead of, modifying the object it refering. ~~
  
* ## How coroutine works behind the scene? What is the benefit of coroutines over state machines?
  Let's talk about what is coroutine first. 
  > Coroutines generalize subroutines for non-preemptive multitasking, by allowing multiple entry points for suspending and resuming execution at certain locations
  
  Basicly, the mission of coroutine is to construct multitasking ecosystem. Isn't that the familar idea with multi-thread? It's true that the goal is similar; however, there are lots of difference between them.
  1. Coroutine is non-preeptive multitasking, while multi-thread is preeptive multitasking. Briefly saying, Non-preeptive multitasking is to manully schedule the tasks, and preeptive multitasking is to schedule by the system.
  2. Without system involving, there is no context switch in coroutine. Hence, there is less performance impact on coroutine.
  3. There is no deadlock in coroutine due to the schedule of tasks is defined maunully.
  4. Coroutine is not parallelly running but concurrently. Multi-threads could run parallelly or concurrently depends on system.
  5. Coroutine is complished by only one thread.
  
  How could coroutine be multitasking but with only one thread? Coroutine allows multiple entry points for suspending and resuming execution. Briefly saying, you could execute a function. During that time, you can pause the execution of that function and run other task.
  After couple of time, you could go back to the function and continue the rest of it. Isn't that sounds like multi-tasking? When you have done the first task, the other has been done, too. You could see the flow diagram below to get a clear view.
  
  ![Value Type vs Reference Type](https://github.com/Vicchang/RayarkTutorial/blob/master/CoroutineThreads.jpg)
  
  Let's talk about how unity implment coroutine and what happens when you call startcoroutine function in unity.
  1. Unity should (not open source) implment coroutine by iEnumerator in C#. That is the reason why every coroutine should be declared as iEnumerator.
  2. To my understanding, iEnumerator is to enumerate code blocks. The idea is that a coroutine could be seprated by "yield" keyword. Calling "MoveNext" in an iEnumerator object would trigger the code block and iterate the pointer to next pointer.
  3. If there are subcoroutine, it could be implmented by stack. That is as long as the subroutine code blocks are all disposed, the coroutine code block in stack then get called.
  4. The time of resuming coroutine in unity is after "update". Since "update" is called every frame, coroutine is called every frame, too.
  
  This is [data flow](https://docs.unity3d.com/Manual/ExecutionOrder.html) diagram from unity.
  
  What is the benefit of coroutines over state machines? First, I'd like to claim it is benefit to hardware-constrained state machine since it has less performance impact. Second, It really makes the state transition clean. 
  * Imaging how could you implment a state machine with only one thread.
  There must be lots of if-else statement since a state transition is always combined with many contidions. To fulfill the conditions, there must be lots of subtasks to do. Without coroutine, you have to set lots of flags in order to know which state it is now and then switch to the corresponding task. Take the example below.
  ```C# 
  // without coroutine
  void HandleMove()
  {
      if (canMove)
      {
          // Detection keyboard
      }

      // Rotatioin
      if (canRotate)
      {
          Vector3 rot = new Vector3(_m_nextTile.transform.position.x - this.transform.position.x, 0, _m_nextTile.transform.position.z - this.transform.position.z);
          Quaternion newRotation = Quaternion.LookRotation(rot, Vector3.up);
          rb.rotation = Quaternion.RotateTowards(transform.rotation, newRotation, 360f);
      }

      // Move
      if (Vector3.Distance(_toMovedistance, Vector3.zero) <= 0.002)
      {
          _m_character.m_curTile = _m_nextTile;
          _m_nextTile = null;
          canMove = true;
          canRotate = true;
          IsMoving = false;
      }
      else
      {
          rb.MovePosition(this.transform.position + _moveDistance * walkSpeed * Time.deltaTime);
          _toMovedistance -= _moveDistance * walkSpeed * Time.deltaTime;
          canMove = false;
          IsMoving = true;
      }

  }  
  ```
  This code uses "canMove" and "canRotate" to controll the flow, which really makes the logic complicated. Imgae that if there are more states, the code would be unreadable.
  ```C#
  // with coroutine
  private IEnumerator CmdDetectionAndMove()
  {
      while (true)
      {
          // detect keyboard
          if (_nextTile)
          {
              yield return StartCoroutine(Move());
          }

          yield return null;
      }
  }
  private IEnumerator Move()
  {
      IsMoving = true;

      // Rotate
      Vector3 rot = new Vector3(_nextTile.transform.position.x - this.transform.position.x, 0, _nextTile.transform.position.z - this.transform.position.z);
      Quaternion newRotation = Quaternion.LookRotation(rot, Vector3.up);
      _rb.rotation = Quaternion.RotateTowards(transform.rotation, newRotation, 360f);

      // Move
      _nextPos = new Vector3(_nextTile.transform.position.x, this.transform.position.y, _nextTile.transform.position.z);
      while (Vector3.Distance(this.transform.position, _nextPos) > 0.2)
      {
          _rb.transform.position = Vector3.Lerp(this.transform.position, _nextPos, _speed * Time.deltaTime);
          yield return null;
      }
      _character.m_curTile = _nextTile;
      _nextPos = Vector3.zero;
      _nextTile = null;
      IsMoving = false;
  }  
  ```
  The logic is more clear. First, detect the keyboard. If detected, start the subcoroutine "move". The "move" will be done as long as it reachs the destination.
  
  This is the power of coroutine. It helps to code exactly like what the state diagram and have a clear view about state transition.
* ## What’s different between delegate and function pointer?
  1. Delegate is a reference type while function pointer is a pointer type.
  2. There is no signature on function pointer while delegate guaruntee to have signature. Here is the example.
  ```C++
  typedef void (*Callback)(void*,int,int);
  
  class DelegatePerf {
  public:
      bool Register(void* holder, Callback cb);
      void run();
      
  private:
      map<string, data> m_holders;
  };
  
  class PerfCounter {
  public:
      static void Callback(void* holder, int x, int y) // must be static in C++
      {
          PerfCounter* temp = (PerfCounter*)holder;
          temp->perfCount(x, y);
      }
      
      ...
      // Register in contructor
  private:
      int perfCount(int x, int y) {
          cout << "123" << endl;
      };
      
      DelegatePerf* _m_df;
  };
  
  bool DelegatePerf::Register(void* holder, Callback cb) {
      data d = {holder, cb};
      m_holders.insert(pair<string, data>("PerfCounter", d));
  }
  
  void DelegatePerf::run() {
      for (auto obj : m_holders)
      {
          obj.second.cb(obj.second.holder, 1, 2);
      }    
  }
 
  int main()
  {
      DelegatePerf* df = new DelegatePerf();
      PerfCounter* pf = new PerfCounter(df);
  
      df->run();
  
      return 0;
  }  
  ```
  ```C#
  class Program
  {
      public delegate int DelegatePerfCounter(int x, int y);

      static void Main(string[] args)
      {
          PerfCounterClass pfc = new PerfCounterClass();
          DelegatePerfCounter Perf = new DelegatePerfCounter(pfc.PerfCounter); // can pass obj function to delegate

          Perf(3, 10);
      }
  }
  
  class PerfCounterClass
  {
      public int PerfCounter(int x, int y) // no need to be static
      {
          Console.WriteLine("456");
          return x + y;
      }
  }
  ```
  3. In C#, a delegate could contain multiple methods, while in C++, a function pointer could could not point to multiple methods. Here is the example about delegate.
  ```C#
  class Program
  {
      public delegate int DelegatePerfCounter(int x, int y);

      public static int PerfCounter(int x, int y)
      {
          Console.WriteLine("123");
          return x + y;
      }

      static void Main(string[] args)
      {
          PerfCounterClass pfc = new PerfCounterClass();
          DelegatePerfCounter Perf = new DelegatePerfCounter(pfc.PerfCounter);  // add first function
          Perf += PerfCounter;                                                  // add second function
          Perf(3, 10);
      }
  }

  class PerfCounterClass
  {
      public int PerfCounter(int x, int y)
      {
          Console.WriteLine("456");
          return x + y;
      }
  }  
  ```
* ## What is a closure and what is variable capture?
  * Closure
  > A closure is a persistent local variable scope.
  Usually, a local varialbe is destroyed when leaving the scope. If a local variable is defined in closure, it's lifecycle would be extend as the same as the closure. Here is an example in C#.
  ```C#
  static void Main(string[] args)
  {
      var tuple = CreateShowAndIncrementActions();
      var show = tuple.Item1;
      var increment = tuple.Item2;
      show(); // Prints 0
      show(); // Still prints 0
      increment();
      show(); // Now prints 1
  }
  static System.Tuple<Action, Action> CreateShowAndIncrementActions()
  {
      int counter = 0; // lifecycle is expanded due to closure
      Action show = () => { Console.WriteLine(counter); };
      Action increment = () => { counter++; };
      return System.Tuple.Create(show, increment);
  }  
  ```
  If a language doesn't support closure, the "counter" would be detroyed as long as leaving the function since it's a local variable. However, in C#, delegate is a way to declare closure. The "counter" is used by delegate. In order to 
  keep the value consistency, the life cycle of couter is extended to the same as "show" and "increment". In other words, a clousre is like an object which contains functions and variables. If the object is not destroyed, the 
  variables and the functions would keep alive.
  
  * Variable Capture
  > A variable capture is a mechanism that when a closure happens, the local variable would be captured into the closure.
  
  The local variable is captured to closure and the life cycle of it is extended. In this case, the local variable should be used carfully. Here is example of misusing.
  ```C#
  void Foo()
  {
      List<Action> actions = new List<Action>();
      for (int i = 0; i < 10; ++i) // i is a local variable and is captured by closure
      {
          actions.Add(() => Console.WriteLine(i));
      }
      
      foreach(Action a in actions) a(); // print 10 for ten times.
  }
  ```
  If there is no closure happened, the output should be 0 to 9. However, the local variable i is captured. The lifecycle of i is extend and this cause that i++ always modify the same i, instead of different i. Here is how we can do it write 
  with closure.
  ```C#
  void Foo()
  {
      List<Action> actions = new List<Action>();
      for (int i = 0; i < 10; ++i) // it is a local varaible but is not captured
      {
          int captured_i = i; // captured_i is captured by closure, the lifecycle of it is within each loop
          actions.add(() => Console.WriteLine(captured_i));
      }
      
      foreach(Action a in actions) a(); // print 0 to 9.
  }
  ```  
  The "captured_i" is captured in each loop. However, its lifecycle is the same as each loop. That means the capture_i in the first loop is different from the second loop and so on.
* ## What is RAII? Why should we prefer RAII over plain function calls?
  RALL is the abbreviation of resource acquisition is initialization. The local variable should be tied to obeject lifetime. On the other hand, if a local variable is created within a scope, it should be destroyed as long as leaving the scope. In that case, there are no resource leaks.
  If a implmentation follows RAII, it benefits from following perspective.
  1. Clean concept: local variable now is combined to the life cycle of the object. You don't need to worry about the destruction of the local variable because it detroys itself automatically as the object is destroyed.
  2. Exception safty: When exception occurs, the call stack would be broken. It has great chance to cause resource leak without the implmentation of RAII. A well-designed RAII could prevent this case.
  3. Immediation of resource releasing: GC will release resource automatically in the future, but RALL can guaruntee the resource would be released as long as the object is destroyed. In severe environment, it is better to release memory as quickly as possible.

  It is prefered to RALL over plain function calls is due to the third reason, immediation of resource release. In C#, GC needs to consume some resource. As long as GC not taking place, the performance of the system would be better. Here
  is an exmple to illustrate plain function calls with RAII and without RAII.
  ```C#
  static void Main(string[] args)
  {
      int x = 1, y = 2;
      Add_RAII(x,y); // no GC involved
      Add(x, y); // GC involved
  }
  static int Add_RAII(int x, int y) // x and y are destroyed while leaving the function
  {
      return x + y;
  }
  static int Add(int x, int y) // Op is not destroyed but stay there waiting for GC
  {
      Opt op = new Opt();
      return op.Add(x, y);
  }    
  ```