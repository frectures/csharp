# C# for Java programmers

## Hello World

```csharp
// import System.*;
using System;

// package Tutorial;
namespace Tutorial
{
    class Program
    {   //          main String
        static void Main(string[] args) // private (!)
        {
            // System.out.println
            Console.WriteLine("Hello World!");
        }
    }
}
```

## History

- 1995 Sun releases Java 1.0
- 1996 Microsoft releases Visual J++
  - proprietary, incompatible Java implementation
- 1998 Sun files civil lawsuit against Microsoft
- 2000 Anders Hejlsberg begins work on C#
  - Turbo Pascal
  - Delphi
  - TypeScript

![](img/hejlsberg.jpg)

| 2002        | 2005      | 2008    | 2010      | 2012    | 2015   | 2017          | 2019 |
| ----------- | --------- | ------- | --------- | ------- | ------ | ------------- | ---- |
| Properties  | Generics  |         | Named &   | `async` |        | Tuples        |      |
| Delegates   | `foreach` |         | optional  | `await` |        | Destructuring |      |
| Events      | `yield`   |         | arguments |         | Roslyn |               | `T?` |
| `interface` |           | Lambdas |           |         |        | Pattern       |      |
| `class`     |           | `var`   | `dynamic` |         |        | matching      |      |
| `struct`    |           | LINQ

- 2001 **.NET Framework** (Windows)
- 2004 **Mono** (Windows, macOS, Linux)
- 2016 **.NET Core** (Windows, macOS, Linux)
- 2020 retire .NET Framework
- 2020 rename .NET Core to **.NET**

## Primitive Types

| Java      | C#       |                    Minimum |                    Maximum |
| --------- | -------- | -------------------------: | -------------------------: |
| `boolean` | `bool`   |                    `false` |                    `true`  |
| `char`    | `char`   |                 `'\u0000'` |                 `'\uFFFF'` |
| `byte`    | `sbyte`  |                       -128 |                       +127 |
|           | `byte`   |                          0 |                        255 |
| `short`   | `short`  |                    -32,768 |                    +32,767 |
|           | `ushort` |                          0 |                     65,535 |
| `int`     | `int`    |             -2,147,483,648 |             +2,147,483,647 |
|           | `uint`   |                          0 |              4,294,967,295 |
| `long`    | `long`   | -9,223,372,036,854,775,808 | +9,223,372,036,854,775,807 |
|           | `ulong`  |                          0 | 18,446,744,073,709,551,615 |
| `float`   | `float`  |              1.4 * 10^ -45 |              3.4 * 10^ +38 |
| `double`  | `double` |              4.9 * 10^-324 |              1.8 * 10^+308 |

## `class` vs. `struct`

```csharp
using System;

namespace Tutorial
{
    class K
    {
        public int balance;
    }

    struct S
    {
        public int balance;
    }

    class Program
    {
        static void Main(string[] args)
        {
            K a = new K();
            K b = a;
                              a.balance = 97;
            Console.WriteLine(b.balance); // 97

            S c = new S();
            S d = c;
                              c.balance = 97;
            Console.WriteLine(d.balance); // 0
        }
    }
}
```

- `class` variable stores object *reference* (or `null`)
  - Assignment copies object reference

![](img/java.svg)

- `struct` variable stores struct *directly*
  - Assignment copies entire struct

## Value parameters vs. `ref` parameters

```csharp
using System;

namespace Tutorial
{
    class Program
    {
        static void swap(object a, object b)
        {
            Console.WriteLine($"{a} {b}"); // world hello
            object c;
            c = a;
            a = b;
            b = c;
            Console.WriteLine($"{a} {b}"); // hello world
        }

        static void Main(string[] args)
        {
            string s = "world";
            string t = "hello";
            Console.WriteLine($"{s} {t}"); // world hello
            swap(s, t);
            Console.WriteLine($"{s} {t}"); // world hello
        }
    }
}
```

- Value parameters are just copies of their respective arguments
- In the above example, the *object references* are copied
  - `a` and `s` share the same string object
- Assignments to value parameters are invisible to the caller
  - `a` is rebound, but `s` is not

![](img/outside.svg)
![](img/before.svg)
![](img/after.svg)

- Mutation *through* `a` would be noticeable through `s`
  - But `object` has no mutator methods
  - And `string`s are immutable
- `ref` parameters alias their respective *argument variables*:

```csharp
using System;

namespace Tutorial
{
    class Program
    {
        static void swap(ref object a, ref object b)
        {
            object c;
            c = a;
            a = b;
            b = c;
        }

        static void Main(string[] args)
        {
            string s = "world";
            string t = "hello";
            // cannot convert from "ref string" to "ref object"
            swap(ref s, ref t);
        }
    }
}
```

- In expected OOP fashion, `string`s are `object`s
- But `string` *variables* are not `object` *variables*
  - Otherwise, we could rebind `string` variables to non-`string` objects:

```csharp
    string s = "hi";
ref object o = ref s; // cannot convert from "ref string" to "ref object"
           o = 42;
```

- Either hardwire `swap` for `string` variables:

```csharp
        static void swap(ref string a, ref string b)
        {
            string c;
            c = a;
            a = b;
            b = c;
        }
```

- Or generify `swap`:

```csharp
        static void swap<T>(ref T a, ref T b)
        {
            T c;
            c = a;
            a = b;
            b = c;
        }
```

## Properties

- Properties are accessed like fields
- But they are implemented with `get`/`set` methods

```csharp
using System;

namespace Tutorial
{
    struct Duration
    {
        // Constants are not stored anywhere at runtime.
        // Usages compile away to their actual value.
        public const int SECONDS_PER_MINUTE = 60;
        public const int SECONDS_PER_HOUR = 60 * 60;

        private long _seconds;

        public long Seconds
        {
            get => _seconds;

            set => _seconds = value;
        }

        public long Minutes
        {
            get
            {
                return _seconds / SECONDS_PER_MINUTE;
            }

            set
            {
                if (value * SECONDS_PER_MINUTE / SECONDS_PER_MINUTE != value)
                    throw new ArgumentException($"{value} is too big");

                _seconds = value * SECONDS_PER_MINUTE;
            }
        }

        public long Hours
        {
            get => _seconds / SECONDS_PER_HOUR;

            set
            {
                if (value * SECONDS_PER_HOUR / SECONDS_PER_HOUR != value)
                    throw new ArgumentException($"{value} is too big");

                _seconds = value * SECONDS_PER_HOUR;
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            Duration dur = new Duration();
            dur.Hours = 2;
            // 120 minutes is 7200 seconds
            Console.WriteLine($"{dur.Minutes} minutes is {dur.Seconds} seconds");
        }
    }
}
```

## Object-oriented programming

- `extends C implements I, J, K` translates to `: C, I, J, K`
  - The default is `: object`
- `super(...);` or `this(...);` translate to `: base(...)` or `: this(...)`
  - The default is `: base()`
- Default constructor is generated in the absence of explicit constructors
- Only `abstract` and `virtual` methods are overridable
- Overriding requires `override` keyword
  - Missing `override` does not compile, unlike Java `@Override`
  - `override` interface method not compile, unlike Java `@Override`

### Java in C#

```csharp
using System;

namespace Tutorial
{
    abstract class AbstractList // : object
    {
        // public AbstractList() : base() {}
            
        public abstract int Count();

        public virtual bool IsEmpty()
        {
            return Count() == 0;
        }
    }

    class ArrayList : AbstractList
    {
        private object[] data;
        private int count;

        public ArrayList() // : base()
        {
            data = new object[2];
            count = 0;
        }

        public override int Count()
        {
            return count;
        }
        
        public void Add(object element)
        {
            int capacity = data.Length;
            if (count == capacity)
            {
                capacity += capacity / 2;
                Array.Resize(ref data, capacity);
            }
            data[count++] = element;
        }

        public object Get(int index)
        {
            return data[index];
        }

        public void Set(int index, object element)
        {
            data[index] = element;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            ArrayList al = new ArrayList();
            al.Add("hello");
            al.Add("world");
            al.Add("and");
            al.Add("then");
            al.Add("goodbye");
            al.Set(0, "hi");
            Console.WriteLine(al.Get(4));
        }
    }
}
```

### Property and Indexer

```csharp
using System;

namespace Tutorial
{
    abstract class AbstractList
    {
        public abstract int Count { get; }

        public virtual bool IsEmpty()
        {
            return Count == 0;
        }
    }

    class ArrayList : AbstractList
    {
        private object[] data;
        private int count;

        public ArrayList()
        {
            data = new object[2];
            count = 0;
        }

        public override int Count
        {
            get => count;
        }

        public void Add(object element)
        {
            int capacity = data.Length;
            if (count == capacity)
            {
                capacity += capacity / 2;
                Array.Resize(ref data, capacity);
            }
            data[count++] = element;
        }

        public object this[int index]
        {
            get => data[index];

            set => data[index] = value;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            ArrayList al = new ArrayList();
            al.Add("hello");
            al.Add("world");
            al.Add("and");
            al.Add("then");
            al.Add("goodbye");
            al[0] = "hi";
            Console.WriteLine(al[4]);
        }
    }
}
```

## Collections

| Java         | C#                 |
| ------------ | ------------------ |
| `Iterable`   | `IEnumerable`      |
| `List`       | `IList`            |
| `ArrayList`  | `List`             |
| `LinkedList` | `LinkedList`       |
| `Set`        | `ISet`             |
| `HashSet`    | `HashSet`          |
| `TreeSet`    | `SortedSet`        |
| `Map`        | `IDictionary`      |
| `HashMap`    | `Dictionary`       |
| `TreeMap`    | `SortedDictionary` |
| `Comparable` | `IComparable`      |

## Iterators

```csharp
using System;
using System.Collections.Generic;
using System.Numerics;

namespace Tutorial
{
    class Program
    {
        static IEnumerable<BigInteger> fibonacciSequence()
        {
            BigInteger a = 0;
            yield return a;

            BigInteger b = new BigInteger(1);
            yield return b;

            while (true)
            {
                BigInteger c = a + b;
                yield return c;

                a = b;
                b = c;
            }
        }

        static void Main(string[] args)
        {
            foreach (BigInteger fib in fibonacciSequence())
            {
                if (fib >= 1000) break;
                Console.WriteLine(fib);
                // 0
                // 1  
                // 1  
                // 2  
                // 3  
                // 5  
                // 8  
                // 13 
                // 21 
                // 34 
                // 55 
                // 89 
                // 144
                // 233
                // 377
                // 610
                // 987
            }
        }
    }
}
```

## Language INtegrated Query (LINQ)

```csharp
using System.Linq;

        static void Main(string[] args)
        {
            foreach (BigInteger fib in fibonacciSequence().TakeWhile(x => x < 1000))
            {
                Console.WriteLine(fib);
            }
        }
```

```csharp
IEnumerable<string> adultNames = people.Where(p => p.Age >= 18).Select(p => p.Name);
```

```csharp
IEnumerable<string> adultNames = from p in people
                                 where p.Age >= 18
                                 select p.Name;
```

## Delegates

- Note the code duplication in `fixCos` and `fixSqrt`:

```csharp
using System;
using System.Collections.Generic;

namespace Tutorial
{
    class Program
    {
        static IEnumerable<double> fixCos()
        {
            double x = 0;
            do
            {
                yield return x;
            } while (x != (x = Math.Cos(x)));
        }

        static IEnumerable<double> fixSqrt()
        {
            double x = 0.5;
            do
            {
                yield return x;
            } while (x != (x = Math.Sqrt(x)));
        }

        static void Main(string[] args)
        {
            foreach (double x in fixCos())
            {
                Console.WriteLine(x);
                // 0
                // 1
                // 0.5403023058681397
                // 0.8575532158463934
                // 0.6542897904977791
                // 0.7934803587425656
                // 0.7013687736227565
                // ...
                // 0.7390851332151608
                // 0.7390851332151606
                // 0.7390851332151607
            }
            foreach (double x in fixSqrt())
            {
                Console.WriteLine(x);
                // 0.5
                // 0.7071067811865476
                // 0.8408964152537146
                // 0.9170040432046712
                // 0.9576032806985736
                // 0.9785720620877001
                // 0.9892280131939755
                // ...
                // 0.9999999999999997
                // 0.9999999999999998
                // 0.9999999999999999                
            }
        }
    }
}
```

- Can we refactor `fixCos` and `fixSqrt` into one method?
  - Abstract over initial number (`0` or `0.5`) with `double x` parameter
  - Abstract over applied method (`Math.Cos` or `Math.Sqrt`) with delegate parameter:

```csharp
using System;
using System.Collections.Generic;

namespace Tutorial
{
    class Program
    {
        delegate double F(double x);

        static IEnumerable<double> fix(F f, double x)
        {
            do
            {
                yield return x;
            } while (x != (x = f(x)));
        }

        static void Main(string[] args)
        {
            foreach (double x in fix(Math.Cos, 0))
            {
                Console.WriteLine(x);
            }
            foreach (double x in fix(Math.Sqrt, 0.5))
            {
                Console.WriteLine(x);
            }
        }
    }
}
```

- `Func`tion delegates are already provided by the standard library:
  - `Func<TResult>`
  - `Func<T, TResult>`
  - `Func<T1, T2, TResult>`
  - `Func<T1, T2, T3, TResult>`
  - ...
  - `Func<T1, T2, T3, T4, T5, T6, T7, T8, T9, T10, T11, T12, T13, T14, T15, T16, TResult>`

```csharp
        static IEnumerable<double> fix(Func<double, double> f, double x)
```

- Also `Action`s for `void` result types:
  - `Action`
  - `Action<T>`
  - `Action<T1, T2>`
  - `Action<T1, T2, T3>`
  - ...
  - `Action<T1, T2, T3, T4, T5, T6, T7, T8, T9, T10, T11, T12, T13, T14, T15, T16>`

## Events

- Delegates can store (multiple!) observers:

```csharp
using System;
using System.Threading;

namespace Tutorial
{
    class SubjectExample
    {
        public Action<DateTime> HellFrozeOver;

        public void FreezeHell()
        {
            Console.Write("Freezing hell... ");
            Thread.Sleep(2000);
            Console.WriteLine("done!");

            if (HellFrozeOver != null)
            {
                HellFrozeOver(DateTime.Now);
            }
        }
    }

    class ObserverExample
    {
        public void Log(DateTime when)
        {
            Console.WriteLine("Hell froze over on " + when);
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            var subject = new SubjectExample();
            var first = new ObserverExample();
            var second = new ObserverExample();

            subject.HellFrozeOver += first.Log;
            subject.HellFrozeOver += second.Log;
            subject.FreezeHell();

            Console.WriteLine("--------------------------------------");
            var third = new ObserverExample();
            // Whoops, accidental = instead of +=
            subject.HellFrozeOver = third.Log;
            // Whoops, delegate invocation outside of subject
            subject.HellFrozeOver(new DateTime(1912, 4, 15));
        }
    }
}
```

- The `event` keyword prevents such accidental misuse:

```csharp
    class SubjectExample
    {
        public event Action<DateTime> HellFrozeOver;

        public void FreezeHell()
        {
            // ...
        }
    }

            // The event SubjectExample.HellFrozeOver can only appear
            // on the left hand side of += or -=
            // (except when used from within the type SubjectExample)
            subject.HellFrozeOver = third.Log;
            // ditto
            subject.HellFrozeOver(new DateTime(1912, 4, 15));
```
