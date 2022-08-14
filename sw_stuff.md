## Run-time Polymorphism:
```cpp
virtual void land(); // virtual function - interface and default implementation - makes it polymorphic class

virtual void fly() = 0; // pure virtual func - interface only - makes it abstract class
```

Virtual Function Table Pointer (vptr): Polymorphic classların içerisine derleyici tarafından bir pointer gömülür. Bu pointer her derived class içerisinde yer alır. Her polymorphic nesnede ekstradan sizeof(ptr) kadar alan ayrılır.

virtual maliyeti:

- Bellek kullanımı: sizeof(ptr) ekstra alan, heap üzerinde oluşturulan virtual function table alanı.

- Fonksiyon çağrısı başına 2 fazla dereferencing. Önce o nesneye ait vptr adresine ulaşılır. Sonra table üzerindeki çağrılacak fonksiyonun adresi elde edilir.

Base class'ın destructor'ı virtual yapılmazsa aşağıdaki durumda Derived class destructor'ı çağrılmaz.
```cpp
Base *b = new Derived();

delete b;
```
### Dreaded Diamond of Derivation: 
Multiple inheritance yaparken en alttaki derived class içinde iki adet Base class olması durumu.

-> Virtual inheritance yapılarak çözülür. Base sınıftan türetme yaparken virtual keyword eklenir. Böylece Base ctor'ı en alttaki derived class tarafından çağrılmış olur.

### static_cast<>
* static_cast<>() gives you a compile time checking ability, C-Style cast doesn't.
* static_cast<>() is more readable and can be spotted easily anywhere inside a C++ source code, C_Style cast isn't.
* Intentions are conveyed much better using C++ casts.

static_cast, const_cast, reinterpret_cast, dynamic_cast
### Special Member Functions:
* Constructor: Myclass();
* Copy ctor : Myclass(const Myclass&);
* Move ctor : Myclass(Myclass&&);
* Copy assign: Myclass& operator=(const Myclass&);
* Move assign: Myclass& operator=(Myclass&&);
### friend
friend bildirimi: Bir sınıfın private bölümüne sınıf dışından erişimi sağlar. Bu bildirimin sınıfın public, private veya protected alanında yapılması arasında bir fark yoktur.
### Argument Dependent Lookup(ADL):
```cpp
namespace ali{
	class Myclass{};
	void foo(vector<Myclass>);
	void func(int);
}

int main() {
	vector<ali::Myclass> myvec;

	foo(myvec); // geçerli, gönderilen argument sayesinde namespace alanına da bakılır.

	func(67); // geçersiz
}
```
### Const & Pointer:
```cpp
int* const p = &x; // const pointer - top level const (adres değiştirilemez), p = &y geçersiz

const int* p = &x; // pointer to const - low level const (değer değiştirilemez), *p = 3 geçersiz

const int* const p = &x; // hem adres hem değer değiştirilemez
```
### Unique pointer: 
Manages the storage of a pointer, providing a limited garbage collection facility. Stored pointer + Deleter

### Lambda expressions:
```cpp
int x = 10;

auto f = [x] (int a) { return x+a;};

f(5); // output: 15
```

### Undefined behavior: 
Run-time'da ne olacağının garantisi yoktur. E.g.; işaretli türlerde taşma, sıfıra bölme, diziyi taşırma.
### Overloading vs Overriding
* Overloading: When two or more methods in the same class have the same name but different parameters.
* Overriding: When the method signature (name and parameters) are the same in the superclass and the child class.
### Object Orienting Programming
#### Building blocks of OOP;

1) Classes: User-defined data types that act as the blueprint for individual objects, attributes and methods.
2) Objects: Instances of a class created with specifically defined data.
3) Methods: Functions that are defined inside a class that describe the behaviors of an object.
4) Attributes: Defined in the class template and represent the state of an object.

#### Main principles of OOP;

1) Encapsulation: This principle states that all important information is contained inside an object and only select information is exposed.
2) Abstraction: Abstraction means that the user interacts with only selected attributes and methods of an object.
3) Inheritance: Relationships and subclasses between objects can be assigned, enabling developers to reuse common logic while still maintaining a unique hierarchy.
4) Polymorphism: Polymorphism allows the same method to execute different behaviors.

#### Benefits of OOP include;

* Modularity: Encapsulation enables objects to be self-contained, making troubleshooting and collaborative development easier.
* Reusability: Code can be reused through inheritance, meaning a team does not have to write the same code multiple times.
* Productivity: Programmers can construct new programs quicker through the use of multiple libraries and reusable code.
* Easily upgradable and scalable: Programmers can implement system functionalities independently.
* Interface descriptions: Descriptions of external systems are simple, due to message passing techniques that are used for objects communication.
* Security: Using encapsulation and abstraction, complex code is hidden, software maintenance is easier and internet protocols are protected.
* Flexibility: Polymorphism enables a single function to adapt to the class it is placed in. Different objects can also pass through the same interface.

### S.O.L.I.D. Principles

* Single Responsibility: Every class should have only one responsibility.
* Open-Closed: Software entities should be open for extension, but closed for modification. A class should be extendable without modifying the class itself.
* Liskov Substitution: Every derived class should be substitutable for their base class.
* Interface Segregation: A client should never be forced to implement an interface that it doesn’t use, or clients shouldn’t be forced to depend on methods they do not use.
* Dependency Inversion: Entities must depend on abstractions, not on concretions. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions.

### Design Patterns;

#### Creational patterns (5);

* Factory method: Provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created.
* Abstract factory: Produces families of related objects without specifying their concrete classes.
* Singleton: Ensures that a class has only one instance, while providing a global access point to this instance.
* Builder: Constructs complex objects step by step.
* Prototype: Copies existing objects without making your code dependent on their classes.

#### Structural patterns (7);

* Adapter: Allows objects with incompatible interfaces to collaborate.
* Bridge: Splits a large class or a set of closely related classes into two separate hierarchies, abstraction and implementation.
* Composite: Composes objects into tree structures and then work with these structures as if they were individual objects.
* Decorator: Attaches new behaviors to objects by placing these objects inside special wrapper objects that contain the behaviors.
* Facade: Provides a simplified interface to a library, a framework, or any other complex set of classes.
* Flyweight: Fits more objects into the available amount of RAM by sharing common parts of state between multiple objects.
* Proxy: Provides a substitute or placeholder for another object.

#### Behavioral patterns (11);

* Chain-of-responsibility: Passes requests along a chain of handlers.
* Command: Turns a request into a stand-alone object that contains all information about the request.
* Interpreter: Defines a grammatical representation for a language and provides an interpreter to deal with this grammar.
* Iterator: Traverse elements of a collection without exposing its underlying representation (list, stack, tree, etc.).
* Mediator: Reduce chaotic dependencies between objects.
* Memento: Save and restore the previous state of an object without revealing the details of its implementation.
* Observer: Define a subscription mechanism to notify multiple objects about any events that happen to the object they’re observing.
* State: Lets an object alter its behavior when its internal state changes.
* Strategy: Define a family of algorithms, put each of them into a separate class, and make their objects interchangeable.
* Template method: Defines the skeleton of an algo. in the superclass but lets subclasses override specific steps of the algo. without changing its structure.
* Visitor: Separate algorithms from the objects on which they operate.

### Data Structures and Algorithms

Big-O Notation (O-notation): Represents the upper bound of the running time of an algorithm, gives the worst-case complexity of an algorithm.

#### Searching Algorithms:

1) Linear Search:

* It is used for unsorted and unordered small list of elements.

* It has a time complexity of O(n), which means the time is linearly dependent on the number of elements.

2) Binary Search:

* It is applied on the sorted array or list of large size.

* It's time complexity of O(log n) makes it very fast as compared to other sorting algorithms.

#### Sorting Algorithms:

1) Bubble Sort:

* Compares all the element one by one and sort them based on their values.

* Time complexity of Bubble Sort is O(n2).

2) Insertion Sort:

* It starts from the index 1(not 0), and each index starting from index 1 is like a new card, that you have to place at the right position in the sorted subarray on the left.

* Time complexity of Insertion Sort is O(n2).

* It is better than Selection Sort and Bubble Sort algorithms.

3) Selection Sort:

* It repeatedly selects the next-smallest element and swaps it into the right place.

* Time complexity of Selection Sort is O(n2).

4) Quick Sort:

* Based on the concept of Divide and Conquer. Also called partition-exchange sort.

* Time complexity of Quick Sort is O(n2).

5) Merge Sort:

* Follows the rule of Divide and Conquer to sort a given set of numbers/elements, recursively, hence consuming less time.

* Time complexity of Merge Sort is O(n*Log n).

6) Heap Sort:

* Involves building a Heap data structure from the given array and then utilizing the Heap to sort the array.

* Time complexity of Heap Sort is O(n*Log n).

#### Data Structures:

Hash table data structure stores elements in key-value pairs where;

* Key: unique integer that is used for indexing the values.

* Value: data that are associated with keys.

In a hash table, a new index is processed using the keys. The element corresponding to that key is stored in the index. This process is called hashing.

Linear Data Structures: array, stack, queue, linked list.

1) Stack: Abstract data type with a bounded(predefined) capacity.

* FILO(First In Last Out)

* Push Operation : O(1) Pop Operation : O(1) Top Operation : O(1) Search Operation : O(n)

* e.g. Call stack.

2) Queue: Abstract data type.

* FIFO(First in First Out)

* Enqueue: O(1) Dequeue: O(1) Size: O(1)

* e.g. Serving requests on a single shared resource, like a printer, CPU task scheduling etc.

* e.g. Handling of interrupts in real-time systems.

a) Circular Queue: Extended version of a regular queue where the last element is connected to the first element.

e.g. CPU scheduling, memory management etc.

b) Priority Queue: Special type of queue in which each element is associated with a priority value, elements are served on the basis of their priority.

c) Double Ended Queue(Deque): A type of queue in which insertion and removal of elements can either be performed from the front or the rear.

e.g. To store history in browsers.

3) Linked List: A linear data structure that includes a series of connected nodes, each node stores the data and the address of the next node.

* Search Operation: O(n) Insert Operation: O(1) Delete Operation: O(1)

a) Singly Linked List: Each node has data and a pointer to the next node.

b) Doubly Linked List: Each node contains a data part and two addresses, one for the previous node and one for the next node.

b) Circular Linked List: Last node of the list holds the address of the first node hence forming a circular chain.

Array vs Linked List:

* Array supports Random Access. Linked List supports Sequential Access.

* In an array, elements are stored in contiguous memory location. In a linked list, new elements can be stored anywhere in the memory.

* For array, memory is allocated at compile time(static memory allocation). For linked list, at runtime(dynamic memory allocation).

* Array gets memory allocated in the Stack section. Whereas, linked list gets memory allocated in Heap section.

* For array, accessing elements is fast. For linked list, insertion and deletion is fast, O(1) constant.

Nonlinear Data Structures: graph, tree.

1) Tree: A nonlinear hierarchical data structure that consists of nodes connected by edges.

* Different tree data structures allow quicker and easier access to the data.

2) Graph: A graph data structure is a collection of nodes that have data and are connected to other nodes.