# Week 2 Programming Patterns
---
* ## What’s different between value type and reference type?
  The easiest way to distinguish them in C# is that value type usually is in stack while, reference type is always in heap.
  ![Value Type vs Reference Type](https://github.com/Vicchang/RayarkTutorial/blob/master/TypeCmpDiagram.jpg)
  
  However, that is not absolutely right. A value type could be in heap, too. Below is the detail description about value type and reference type.
  ### Value Type: 
  1. Usually stored in stack.
  2. A value type usually is not null. In C#, a value type could be defined as "Type?" to  nullable type. It is still value type since nullable types are instance of System.Nullable<T> struct.
  3. In C#, struct, int and other primitive types are all value type.
  4. In C#, the copy constructor of value type is deep copy.
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
  4. In C#, the copy constructor of a reference type is shadow copy.
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
  Immuatable type can be explained by its name lol. A immutable object would never be changed, except initialization. What's the benefit? Some object is designed to be unchanged. With this attribute, it can help enginer not to modify the object accidently.
  
  The string in C# is immutable is due to that the string is a reference type. A reference type means that you could modify the value any time as long as you hold the reference. It is really risk if string is not immutable. Imagine the case below.
  ```C#
  static void Main(string[] args)
  {
      string str = "123";
      ModifyStr(str);
      Console.WriteLine("str is " + str); // str is 123
  }

  static void ModifyStr(string str)
  {
      str = "456";
  }
  ```
  If string is not immutable type, the console should print "str is 456". That is terrible. A class is just share the string to other class, but somehow, other class can modify the string and effect the sender class. Fortunately, it shows "str is 123" due to the string in fucntion refers to the new string, instead of, modifying the object it refering. 
  
* ## How coroutine works behind the scene? What is the benefit of coroutines over state machines?
  Let's talk about what is coroutine first. 
  > Coroutines generalize subroutines for non-preemptive multitasking, by allowing multiple entry points for suspending and resuming execution at certain locations
  Basicly, the mission of coroutine is to construct multitasking ecosystem. Isn't that the familar idea with multi-thread? It's true that the goal is similar; however, there are lots of difference between them.
  1. Coroutine is non-preeptive multitasking, while multi-thread is preeptive multitasking. Briefly saying, Non-preeptive multitasking is to manully schedule the tasks, and preeptive multitasking is to schedule by the system.
  2. With system involving, there is no context switch in coroutine. Hence, there is less performance impact on coroutine.
  3. There is no deadlock in coroutine due to the schedule of tasks is defined maunully.
  4. Coroutine is not parallelly running but concurrently. Multi-threads could run parallelly or concurrently depends on system.
  5. Coroutine is complished by only one thread.
  
  How could coroutine be multitasking but with only one thread? Coroutine allows multiple entry points for suspending and sesuming execution. Briefly saying, you could execute a function. During that time, you can pause the execution of that function and run other task.
  After couple of time, you could go back to the function and continue the rest of it. Isn't that sounds like multi-tasking? When you have done the first task, the other has been done, too. You could see the flow diagram below to get a clear view.
  
  ![Value Type vs Reference Type](https://github.com/Vicchang/RayarkTutorial/blob/master/CoroutineThreads.jpg)
  
  
* ## What’s different between delegate and function pointer?
* ## What is a closure and what is variable capture?
* ## What is RAII? Why should we prefer RAII over plain function calls?