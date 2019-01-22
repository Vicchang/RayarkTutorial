# Week 3 C#
---
* ##What’s different between struct and class?
    Age           | Time  | Food | Gold | Requirement
    --------------|:-----:|-----:| ----:|------------------------
    Feudal Age    | 02:10 |  500 |    0 | Dark Age building x 2
    Castle Age    | 02:40 |  800 |  200 | Feudal Age building x 2
    Imperial Age  | 03:30 | 1000 |  800 | Castle Age building x 2  
    
  1. Struct can't have explicit parameterless constrcutor.
  2. Struct is value type. Class is reference type.
  3. Struct can't be abstract. Class can be abstract.
  4. Struct is sealed type as default. Class can be delcared as sealed type.
  5. Struct can't inheritance. Class can inheritance.
  6. Struct can't dipose. Class can dipose.
  7. Struct support interface. Class support interface.

* ##What happens if you assign a struct to an interface variable?
* ##What’s different between abstract class and interface?
* ##What is the benefits brought by using StringBuilder instead of direct string concatenation?
* ##What does IDisposable interface do?
* ##What’s the advantage / disadvantage between GC and reference counting?
* ##What’s the difference among Interpreter, Compiler, and JIT? What’s the pros and cons of JIT?
* ##Does delegate hold any reference?