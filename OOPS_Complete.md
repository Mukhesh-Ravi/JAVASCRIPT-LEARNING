# OOPs — Complete Notes (OA Level)

## 1. Core Concepts
- **Object** — instance of class with state (attributes) + behavior (methods) + identity.
- **Class** — blueprint.
- **Message Passing** — objects communicate by calling each other's methods.

## 2. Four Pillars (Detailed)

### Encapsulation
- Binds data + methods together, restricts direct access to internal state via access modifiers.
- Achieved with private fields + public getters/setters.
- Benefit: data hiding, validation control, reduces coupling.

### Abstraction
- Hides complexity, shows only relevant features.
- Achieved via abstract classes/interfaces.
- Abstraction = design level ("what"); Encapsulation = implementation level ("how hidden").

### Inheritance
- "IS-A" relationship. Child class inherits fields/methods of parent.
- Types:
  - **Single** — one parent, one child.
  - **Multilevel** — chain (A→B→C).
  - **Hierarchical** — one parent, multiple children.
  - **Multiple** — child inherits from 2+ parents (allowed via classes in C++, NOT allowed via classes in Java — only via interfaces, to avoid Diamond Problem).
  - **Hybrid** — combination of above.
- **Diamond Problem** — if class D inherits from B and C, both inheriting from A, ambiguity arises about which A method D uses. Java solves by disallowing multiple class inheritance; C++ solves via **virtual inheritance**.

### Polymorphism
- **Compile-time (Static)** — Method Overloading, Operator Overloading (C++ only, not Java). Resolved by compiler based on method signature.
- **Runtime (Dynamic)** — Method Overriding. Resolved at runtime via **dynamic method dispatch** / **vtable** (virtual method table) — base class reference, subclass object, method call resolved based on ACTUAL object type not reference type.

## 3. Classes & Objects — Deep Dive

### Constructors
- Same name as class, no return type.
- **Default** — no args, auto-generated if none defined.
- **Parameterized**.
- **Copy Constructor** — explicit in C++ (`ClassName(const ClassName &obj)`); Java doesn't have this natively, uses `clone()` or manual copy constructor pattern.
- Constructor chaining: `this()` (same class) / `super()` (parent class) — must be FIRST statement if used.
- Constructors are NOT inherited, cannot be overridden, CAN be overloaded.

### Destructors / Finalizers
- C++: `~ClassName()` — called automatically on object destruction (stack) or `delete` (heap).
- Java: no destructors; Garbage Collector reclaims unreachable objects automatically; `finalize()` deprecated since Java 9.

### `this` keyword
Refers to current object; used to resolve naming conflicts (constructor parameter vs field), pass current object as argument, method chaining (return `this`).

## 4. Method Overloading vs Overriding (Detailed)
| Aspect | Overloading | Overriding |
|---|---|---|
| Location | Same class | Parent-child (inheritance) |
| Parameters | Must differ (number/type/order) | Must be same |
| Return type | Can differ | Must be same or covariant |
| Binding | Compile-time (static binding) | Runtime (dynamic binding) |
| Access modifier | Can differ freely | Cannot reduce visibility (can increase) |
| `static` methods | Can be overloaded | Cannot be overridden (can be hidden — method hiding) |
| Exceptions | No restriction | Overriding method can't throw broader checked exceptions than parent |

## 5. Abstract Class vs Interface (Detailed)
| Abstract Class | Interface |
|---|---|
| `abstract class` keyword | `interface` keyword |
| Can have both abstract & concrete methods | Traditionally only abstract (Java 8+: default & static methods allowed) |
| Can have constructors | Cannot have constructors |
| Can have instance variables (any type) | Only `public static final` (constants) |
| Single inheritance (`extends`) | Multiple implementation (`implements`) allowed |
| Access modifiers on methods: any | Methods implicitly `public` |
| Use when: sharing common code among closely related classes | Use when: defining a capability/contract across unrelated classes |

## 6. Access Modifiers (Detailed)
| Modifier | Same Class | Same Package | Subclass (diff package) | Everywhere |
|---|---|---|---|---|
| private | ✔ | ✘ | ✘ | ✘ |
| default (package-private) | ✔ | ✔ | ✘ | ✘ |
| protected | ✔ | ✔ | ✔ | ✘ |
| public | ✔ | ✔ | ✔ | ✔ |

## 7. Static & Final Keywords
- **static variable** — class-level, shared, one copy regardless of number of objects.
- **static method** — belongs to class, can't use `this`/`super`, can't access non-static members directly.
- **static block** — executes once when class loaded, before main.
- **final variable** — constant, can't be reassigned once initialized.
- **final method** — can't be overridden.
- **final class** — can't be inherited (e.g. `String` class in Java).

## 8. Binding
- **Static/Early Binding** — resolved at compile time (overloading, static/private/final methods).
- **Dynamic/Late Binding** — resolved at runtime (overriding, via vtable/virtual dispatch).

## 9. Association, Aggregation, Composition
- **Association** — general "uses-a" relationship, objects have independent lifecycles (Teacher-Student).
- **Aggregation** — "has-a" WEAK relationship, child can exist without parent (Department has Employees — Employee exists even if Department is deleted).
- **Composition** — "has-a" STRONG relationship, child CANNOT exist without parent (House has Rooms — Room destroyed when House destroyed). Denoted by filled diamond in UML (vs hollow diamond for aggregation).

## 10. Coupling & Cohesion
- **Coupling** — degree of dependency between modules; LOW coupling is desirable.
- **Cohesion** — degree to which elements within a module belong together; HIGH cohesion is desirable.
- Goal: Low coupling + High cohesion = maintainable, modular design.

## 11. Exception Handling (Java/C++ style)
- `try` — block that might throw exception.
- `catch` — handles specific exception type.
- `finally` — always executes (cleanup), even if exception occurs or method returns.
- `throw` — explicitly throw an exception.
- `throws` — declares exceptions a method might throw (Java, for checked exceptions).
- **Checked Exceptions** — checked at compile time (must be handled or declared) — e.g. IOException.
- **Unchecked Exceptions (Runtime)** — not checked at compile time — e.g. NullPointerException, ArithmeticException.
- **Custom Exceptions** — user-defined by extending `Exception`/`RuntimeException`.
- Exception hierarchy (Java): `Throwable → Exception / Error`; `Exception → RuntimeException (unchecked) + other checked exceptions`.

## 12. Generics / Templates
- **Templates (C++)** — compile-time generic programming; function templates, class templates.
- **Generics (Java)** — type-safety at compile time, avoids ClassCastException, uses type erasure at runtime (`List<Integer>` becomes `List` at bytecode level).
- Benefits: code reusability, type safety, eliminates casting.

## 13. Memory Management
- **Stack** — stores method calls, local variables, primitive types; LIFO; automatic allocation/deallocation; fast.
- **Heap** — stores objects; managed manually (C++: `new`/`delete`) or automatically (Java: Garbage Collector).
- **Garbage Collection (Java)** — automatically reclaims memory of unreachable objects. Common algorithms: Mark-and-Sweep, Generational GC (Young/Old generation).
- **Memory Leak** — objects no longer needed but still referenced, preventing GC from reclaiming (common even in GC languages via lingering references, e.g. in collections/listeners).
- **Dangling pointer (C++)** — pointer referencing memory that's been freed.

## 14. Object Cloning
- **Shallow Copy** — copies object but nested object references are shared (both point to same nested object).
- **Deep Copy** — copies object AND all nested objects recursively (fully independent copy).

## 15. UML Basics (often asked conceptually)
- **Class Diagram** — shows classes, attributes, methods, and relationships (association, aggregation, composition, inheritance, dependency).
- Relationship arrows: Inheritance (hollow triangle + solid line), Interface implementation (hollow triangle + dashed line), Aggregation (hollow diamond), Composition (filled diamond), Dependency (dashed arrow).

## 16. SOLID Principles
- **S**ingle Responsibility — one class, one reason to change.
- **O**pen/Closed — open for extension, closed for modification.
- **L**iskov Substitution — subclasses must be substitutable for their base classes without breaking correctness.
- **I**nterface Segregation — prefer many small specific interfaces over one large general interface.
- **D**ependency Inversion — depend on abstractions, not concrete classes.

## 17. Common Design Patterns (conceptual, often in OA MCQs)
- **Singleton** — ensures only one instance of a class exists globally.
- **Factory** — creates objects without specifying exact class.
- **Observer** — one-to-many dependency, when one object changes state, dependents are notified (used in event handling).
- **Strategy** — defines a family of algorithms, encapsulates each, makes them interchangeable.
- **Builder** — constructs complex objects step by step.

## 18. Common Traps / OA Gotchas
- Constructors CANNOT be `abstract`, `final`, `static`, or `synchronized`.
- `static` methods CANNOT be overridden (only hidden) — calling via subclass reference still calls based on reference type, not object type (unlike instance methods).
- In Java, ALL non-static methods are virtual by default (dynamic dispatch); in C++, you must explicitly mark a method `virtual` for dynamic dispatch — otherwise it uses static binding even with base class pointer.
- Overloaded methods resolved at COMPILE time based on reference type/argument types, not actual object.
- A class can implement multiple interfaces even if they have same default method signature — must override to resolve conflict.
- `private` methods and fields are NOT inherited (not accessible directly in subclass), though they still exist in memory.
