# COURSE 01
C++ is a multi-paradigm language, supporting;
 * procedural,
 * object-oriented,
 * generic,
 * functional programming.
#### C in C++
* C dilinde aşağıdakilerin ikisi farklı anlamda, C++'de ikisi de parametre değişkeni yok anlamında.
```cpp
void func() 	-> parametre değişkenleri hakkında bilgi vermiyor.
void func(void) -> parametre değişkeni yok.
```

* Karakter sabitlerinin (e.g. 'A') C dilinde türü int iken C++ dilinde türü char.

* C dilinde empty class'a izin verilmez. C++ için ise geçerlidir.
```cpp
struct Data{ };
  ```
* C dilinde struct içinde fonksiyon bildirimi yapılamaz. C++ için ise geçerlidir.
```cpp
struct Point {
	void foo(int, int);
};
```
* C'de ilk değerini sabit ifadesi ile almış const değişkenler sabit ifadesi gereken yerlerde kullanılamaz. C++'da sorun yok.
```cpp
const int x = 10;
int a[x];
 ```
* C dilinde const T* => T* implicit type conversion var, C++'da geçerli değil.
```cpp
  const int x = 20; // C dilinde external linkage (static ile nitelenirse => internal)
	            // C++ dilinde internal linkage (sadece olduğu dosyada geçerli)
```
#### const & pointer
```cpp
int x = 10;
int* const p1 = &x;		  // const pointer - top level const (adres değiştirilemez)
*p1 = 45;		          // p1 = &y; geçersiz
const int* p2 = &x;		  // pointer to const - low level const (değer değiştirilemez)
p2 = &y;		          // *p2 = 56; geçersiz
const int* const p3 = &x; // hem adres hem de değer değiştirilemez
```
  
* undefined behavior: run-time'da ne olacağının garantisi yok.
  e.g. işaretli türlerde taşma, nullptr'ı dereference etmek, diziyi taşırma, sıfıra bölme etc.
* unspecified behavior: derleyici ayarlarına göre sonuç değişebilir.

* C++ dilinde aritmetik türlerden ve pointer türlerinden bool türüne implicit type conversion vardır.
```cpp
int x = -12;
bool b1 = x; // b1 = true (nonzero)
int* ptr = &x;
bool b2 = p; // b2 = true (nullptr değil)
``` 
* C++ dilinde farklı adres türleri arasında ve aritmetik türler ile adres türleri arasında implicit type conversion yoktur.
```cpp
int x = 127;
char* p1 = &x;  // geçersiz
int* p2 = x;	// geçersiz
```

# COURSE 02
#### Name Lookup
* İsim arama belirli bir sırayla yapılır.
* İsim arama bir kez yapılır. Aranan isim bulunursa isim arama sona erer ve bir daha başlamaz.
 ```cpp 
void func();
int main(){
	int func = 10;
	func(); // syntax error, due to the integer above
}
```
* name lookup -> context control -> access control(C'de yok; private, public, protected)
* initialization vs assignment
```cpp
int x = 5;  // initialize, = is not an operator
x = 10;	    // assign, = is an operator
```
#### Storage Class
| automatic                          | static                                                       | dynamic |
|------------------------------------|--------------------------------------------------------------|---------|
| void func() {<br>  int x = 5;<br>} | int g; // g = 0<br>void func() {<br>  static int y = 1;<br>} |         |
* automatic local variable default init ile initialize edilirse garbage(indetermined) value alır.
```cpp
void func(){
	int x; // default init
	y = x; // undefined behavior
}
```
#### Variable Initialization
```cpp
int x;     // default init
int x{};   // value init => zero init garanti edilir
int x = 5; // copy init
int x(10); // direct init
int x{10}; // direct list init, brace init, uniform init
```
 * Brace initialization 'most vexing parse' problemini ortadan kaldırır. Narrowing conversion'ın implicit yapılmasını engeller.

* Operator overloading: Bir sınıf nesnesi bir operatörün operandı olduğunda derleyici ifadeyi bir fonksiyon çağrısına dönüştürür.
```cpp
std::cout << x;	// << operatörü overloaded
```
* scope(kapsam): Bütün isimlerin bir kapsamı vardır. Kapsam, o ismin erişilebilir olduğu kod aralığı.
  * file scope: global isim alanında bildirilen scope. kaynak dosyada bildirildiği noktadan dosyanın sonuna kadar her yerde kullanılabilir.
  * block scope: tanımlanan blok içerisinde kullanılabilir. e.g. curly brace içi
  * function prototype scope: e.g. void func(int x);
  * function scope: goto ile kullanılır.
  * namespace scope
  * class scope
  
C:   file, block, function prototype, function
C++: namespace, class, block, function prototype, function

Değişkenlerin kapsamları mümkün olduğu kadar daraltılmalıdır. 
-> Prevent scope leakage!
```cpp 
int x = 10;
int main(){
	int x = 5;
	std::cout << x << '\n';	// x = 5
	std::cout << ::x << '\n'; // x = 10, :: operatörü global alana bakılmasını sağlar
}
```  
x 	         -> unqualified name
Data::x -> qualified name

# COURSE 03
#### enum & enum class
* C++ dilinde enum türünün underlying type'ı seçilebilir. Eğer seçilmezse enumeration constant değerlerine bakarak derleyici seçim yapar.
```cpp
enum Color : unsigned int {White, Red, Black};
```
* C++ dilinde aritmetik türlerden (int, double etc.) enum türlerine implicit tür dönüşümü yoktur.
```cpp  
Color mycolor = White;
mycolor = 2; // geçersiz
```
enum class: Aşağıdaki sorunlara çözüm olarak dile eklenmiştir.
```cpp
enum ScreenColor {White, Red, Black};
enum TrafficLight {Yellow, Red, Green};
 ```
```cpp
Color mycolor = Red;
int ival;
ival = mycolor; // geçerli ama hataya açık bir durum yaratıyor
```
```
enum ile forward declaration yapılamıyordu. 
```
#### Reference Semantics
```cpp  
ptr = &x;  /* & adres operatörü */		|	int x = 10;
*ptr = 10; /* * dereferencing operatörü */	|	int* p = &x; // * declarator
						|	int& r = x;  // & L value ref
```
* Referanslar assembly düzeyinde bir kavram değil, sonuçta derleyici pointer kodu üretir. Referanslar sadece kod yazımına yönelik kolaylık sağlar.
```cpp
int x = 10;
int& r = x;	// or int* const ptr = &x;
r = 20;		// or *ptr = 20;
```
* Referans bir değişkenin isminin yerine geçer; referansın adresi kullanılamaz.
* Elemanları referans olan diziler oluşturulamaz.
#### Value Category of an Expression
* C dilinde L value(e.g. x, ptr, *ptr), R value(e.g. x+4, &x, ++x)
* C++ dilinde L value, PR value(pure R), X value(eXpiring)
  * Değişkenlerin isimleri olan ifadeler her zaman lvalue (e.g. int x)
  * Operatörlerle oluşturulan ifadeler bazı istisnalar dışında prvalue (e.g. x+5)
	- İstisnalar: x[2], *ptr
  * C++'da prefix(e.g. ++x) lvalue, suffix(e.g. x--) prvalue, C dilinde ikisi de rvalue
  * C++'da virgül operatörü(e.g. (x,y)) lvalue, C dilinde rvalue
  * C++'da koşul operatörü(e.g. a>5 ? x:y) lvalue, C dilinde rvalue
 #### const correctness
```cpp
void func(T* ptr);	// or func(T& r);  		setter(mutator) function
void foo(const T* ptr);	// or foo(const T& r);  getter(accessor) function
```
```cpp
int const& r = x; // or const int& r = x; const reference
```
```cpp
int& const r = x;      // reference zaten const olduğu için derleyici ignore eder
void bar(const int x); // manasız, const bir şey katmıyor
```
```cpp
int a[3] = {1, 4, 7};
int* ptr = a; 	   // a dizisinin ilk elemanını gösteriyor
int(*ptr)[3] = &a; // a dizisinin kendisini gösteriyor e.g. (*ptr)[1] = 5;
int(&ra)[3] = a;   // a dizisinin kendisini gösteriyor e.g. ra[2] = 7;
int* ptr = a + 2;  // or int* ptr = &a[2];
int& r = *(a+2);   // or int& r = a[2]; 
```
# COURSE 04
```cpp
  double dval = 1.2;
  const int& r1 = dval;	// internally: int temp_val{dval};	const int& r = temp_val;
/* derleyici arka planda geçici bir değişken oluşturup referansı ona bağlıyor. geçici nesne, referansın ömrü boyunca hayatını sürdürür */
  const int& r2 = 10; // syntax hatası yok
```
* null reference diye bir kavram yoktur.
* type deduction haricinde "reference to reference" yoktur.
* dangling pointer: gösterdiği nesne artık yaşamıyor. Tipik olarak dinamik bellek yönetiminde olur.
  ```cpp
  ptr = (int) malloc(n * sizeof(int));
  free(ptr); // dangling pointer haline geldi
  ```
* nullptr karşılığı reference için olmadığından fonksiyonun geri dönüş değeri std::optional yapılırsa
benzer bir kullanım sağlanabilir.
* array decay: array to pointer conversion, the loss of type and dimensions of an array.
* type deduction: programın çalışma zamanı ile ilgisi yoktur, derleme zamanında yapılır. Bir değişke-
nin türünü yazmasanız bile derleyici koda bakarak türün ne olması gerektiği çıkarımını yapar.

 * AAA: Almost Always Auto
```cpp
int a[10]{};
auto x = a; // or int* x = a;  array decay
  
const int x = 10;
auto y = x; // or int y = x;	  const düşer
  
int x = 10;
int& r = x;
auto y = r; // or int y = r;   reference düşer
 
auto x = "alican";  // or const char* x = "alican";		array decay
auto& x = "alican"; // or const char(&x)[7] = "alican";	

int a[]{1,2,3};
auto& x = a; // or int(&x)[3] = a;	dizinin tamamına reference olur
  
int func(int);
int(&fref)(int) = func; // function reference
int(*fptr)(int) = func; // function pointer
```
#### Forwarding Reference (Universal Reference)
```cpp  
int x{};
auto&& r = x;	    // or int& r = x; 		int& && => int&
auto&& r = x + 5;   // or int&& r = x+5;	int &&  => int&&
```
Reference collapsing: Normalde C++ dilinde "reference to reference" olmaz. Ancak tür çıkarımı yapılan durumlarda tür çıkarım sonucunda oluşabilir. Bu durumda derleyici reference collapsing uygular.
```cpp  
  T&& &	 => T&		// auto&& x = variable(e.g. y);   => T&
  T&  &	 => T&		// auto& x = expr;	=> x always T&
  T&  && => T&		// auto& x = expr;	=> x always T&
  T&& && => T&&		// auto&& x = constant(e.g. 67);  => T&&
```
decltype: type deduction için kullanılır.
* decltype operatörünün operandı isim ise;
```cpp
    const int b[3];
    decltype(b) a; // or const int[3] a;
```
 *  decltype operatörünün operandı isim formunda değil ifade ise;
  1. parantez içindeki ifade PR value ise decltype yerine gelen tür T
```cpp
    int x = 10;
	decltype(x+5) y; // or int y;
```
  2. parantez içindeki ifade L value ise decltype yerine gelen tür T&
 ```cpp   
    int* ptr = &x;
	decltype(*ptr) z = y; // or int& z = y;
```
  3. parantez içindeki ifade X value ise decltype yerine gelen tür T&&

* Unevaluated context: Bazı durumlarda yazılan kodda bir ifade olmasına rağmen derleyici dilin kurallarına göre o ifade için bir işlem kodu üretmez.
```cpp  
  decltype(++x) y = z;         // ++x işlemi yapılmaz, x'in değeri aynı kalır
  auto t = sizeof(func());     // func() çağrısı yapılmaz
  auto s = typeid(x++).name(); // x'in değeri aynı kalır
```
# COURSE 05
#### constexpr
```cpp
  const int x = 10; 	// constant expression ile ilk değer
  const int y = foo();  // fonksiyon dönüşü ile ilk değer
  int a[x] = {0};
  int b[y] = {0};       // geçersiz, y sabit bir ifade ile ilk değer almıyor
```
* const türün bir parçası iken constexpr o nesnenin sabit olarak kullanılacağını gösterir. Eğer bir
constexpr nesnenin tanımı geçerli ise sabit ifadesi gereken her yerde kullanılabilir.
```cpp
  constexpr int x = 10;
  constexpr int y = foo();	// geçersiz
  constexpr const int* p = &x;
```
* Eğer bir constexpr fonksiyona yapılan çağrıda gönderilen tüm argümanlar sabit ifadesi ise fonksiyonun geri dönüş değeri derleme zamanında elde edilir.
```cpp
  constexpr int square(int x) { return x*x; }
  int x = 5;
  constexpr int y = 8;
  square(x); // run-time
  square(y); // compile-time
```
Bazı kodların tanımlama(definition) olmalarına rağmen başlık(header) dosyalarında bulunması gerekir.
*** inline fonksiyonlar, constexpr fonksiyonlar, template kodlar, inline değişkenler.
#### Default Arguments for Functions
```cpp
  void func(int, int, int = 10);	// declaration yaparken bildirilir
  int foo(int x = 5);
  int bar(int y = foo());
```
* C dilinde yoktur.
* Derleme zamanında belirlenir, run-time maliyeti yoktur.
* Çağrıların çok büyük kısmında belirli parametreler için hep aynı değerler argüman olarak gönderiliyorsa kullanım kolaylığı sağlar.
* Eğer bir fonksiyonun bildiriminde default argument belirtilmiş ise onun sağındaki tüm parametreler için de default argument belirtilmesi gerekir.
```cpp 
  int foo(int x, int y = 10, int z);	// geçersiz
```  
* Fonksiyon bildirimleri kümülatif olarak ele alınır.
```cpp  
  void func(int, int, int = 20);
  void func(int, int = 10, int); // function redeclaration, eğer bu üstte olsaydı geçersizdi!
```  
### Function Overloading
* Kodları farklı olmasına rağmen soyutlama yapıldığında özünde aynı işi yapan fonksiyonların aynı
isimli olmasını sağlayarak programcının işini kolaylaştırır.
  * e.g. absolute fonksiyonu: C++ => abs(); C => abs(), fabs(), labs() etc. 
* Derleme zamanında(Static/Early Binding) yapılır, run-time(Dynamic/Late Binding) maliyeti yoktur.
* Function overloading için iki kriterin sağlanması gerekir.
  1) Aynı scope içinde birden fazla fonksiyonun isimleri aynı olmalı,
  2) Aynı scope içindeki aynı isimli fonksiyonların imzaları(signature) farklı olmalı.
     * Signature: Bir fonksiyonun geri dönüş türü haricindeki parametrik yapısına denir.
```cpp
  int func(int);
  int main(){
	int func(double);	// name hiding, no overload
  }

  int foo(int);
  double foo(int);	// geçersiz, imzalar aynı
  
  void bar(int);
  void bar(const int); // redeclaration, no overload, nesnenin kendisinin const olması sayılmaz
  
  void boo(int*);
  void boo(const int*); // function overload, referans için de geçerli
  
  void ehmp(int[]);
  void ehmp(int*);	// redeclaration, no overload due to array decay
```
#### Function Overload Resolution
Derleyici tarafından 3 aşamada ele alınır;
 1) Candidate functions: Fonksiyon çağrısının yapıldığı noktada görülebilir olan aynı isimli bütün
fonksiyonlar bir kümeye konulur. Argümanlardan bağımsız.
 2) Viable functions: Aday fonksiyonlar içerisinde fonksiyon çağrısında kullanılan argümanlarla geçerli olarak çağrılabilen fonksiyonlar ele alınır. "Eğer bu fonksiyon tek başına olsaydı bu çağrı geçerli olur muydu?" sorusuna cevaptır.
    * Argüman-parametre sayılarının aynı olması,
    * Her bir argümandan ilgili parametreye geçerli bir tür dönüşümü olması gerekir.
 3) Best viable function: Argümandan parametreye yapılan dönüşme göre belirlenir.
    * Variadic conversion: En düşük öncelikli olan dönüşümdür.
    * User defined conversion: Programcının tanımladığı bir fonksiyon ile yapılır.
    * Standard conversion: Dilin kurallarına göre uygun diğer dönüşümlerdir(e.g. double->int).
      * Eğer birden fazla standard conversion varsa aşağıdaki sıraya göre belirlenir;
	  a) Conversion: e.g. float->long double, unsigned int->int, int->double
	  b) Promotion: float->double ve integral promotions(e.g. char->int, bool->int)
	  c) Exact match: Argüman ve parametre türünün aynı olduğu durum. İstisnalar;
	    - array decay ile yapılan dönüşüm
		- const conversion
		- function to pointer conversion
```
Candidate functions => Viable functions => Best viable function
		    -> No match		-> Ambiguity
```					
```cpp
  void func(long double);	// Viable => Conversion
  void func(char);		// Viable => Conversion
  void func(int, int);		// Candidate
  void func(int*);		// Candidate
  func(67);	                // 1. ve 2. Viable, ikisinde de Conversion => Ambiguity
```
# COURSE 06
Function overload resolution için özel durumlar;
 1) Fonksiyonların varsayılan argüman alması
 ```cpp
   void func(int x, int y = 10);
   void func(int x);
   func(21);	// ambiguity
```
 2) const overloading
 ```cpp
   void func(int&);
   void func(const int&);
   int x{10};
   const int y{5};
   func(x); // ikisi de viable, void func(int&) çağrılır
   func(y); // sadece ikinci viable, void func(const int&) çağrılır
```
 3) const int& vs int&&
```cpp
   void func(const int&);
   void func(int&&);
   int x{34};
   func(x);	// sadece birinci viable, void func(const int&) çağrılır
   func(34);	// ikisi de viable, void func(int&&) çağrılır
```
 4) null pointer conversion
```cpp
   void func(int* p);
   void func(int);
   func(0);	// ikisi de viable, ilki conversion ikincisi exact match, void func(int) çağrılır
```
 5) void pointer conversion
```cpp
   void func(void* vptr);
   void func(bool);
   int x{14};
   func(&x); // ikisi de viable, void func(void* vptr) çağrılır
```
* Overload fonksiyonların birden fazla parametresi olması durumunda bir fonksiyonun seçilebilmesi
için en az bir parametrede diğerlerine üstün gelmesi ve diğer parametrelerde daha az öncelikli olmaması gerekir. Eşitlik halinde ambiguity oluşur.
```cpp
  void func(int, double, long);
  void func(long, int, bool);
  void func(double, float, int);
  func(12, 5u, 2.3);	// void func(int, double, long) çağrılır
  func(12, 5u, 8);	// 1. ve 3. eşit => Ambiguity
  func(12u, 65L, true); // void func(long, int, bool) çağrılır
```
  
Sabit gösterimleri:
    * e.g. 764 -> int, 82u -> unsigned int, 23L -> long, 3.4 -> double, 2.7f -> float
    * Scientific notation: 3e2(3*10^2) -> double, 3e2f -> float
 
### CLASSES
* class members: data member, member function, type member(nested type)
```cpp
class Data{};
sizeof(Data);
```
* access specifiers(public, private, protected) ayrı scopelar oluşturmaz.
* Sınıfın member functionları için function redeclaration yapılamaz.
* Sınıfın non-static member functionları gösterilmeyen bir parametre değişkenine sahiptir. Bu parametre değişkeni ile ilgili sınıf türünden pointer'dır. Assembly kodunda ikisi de aynı işi yapmasına rağmen, sınıf içinde olması scope ve kullanım kolaylığı sağlar.
```cpp
  class Myclass {
	void func(int); // 
  }
  void func(Myclass* ptr, int x);
```

# COURSE 07
Name lookup in classes: block, enclosing blocks, class scope, namespace scope

Sınıfın üye fonksiyonu içinde o sınıfa ait herhangi bir sınıf nesnesinin private bölümüne erişilebilir.
```cpp
  Myclass gx;
  void Myclass::foo(){
	  Myclass x;
	  gx.a = 10;
	  x.a = 20;
  }
```
```cpp
  class Alican{
  public:
	void func(int x);
  private:
    void func(double x);
  };
  int main(){
	Alican ax;
	ax.func(1.2);	// önce function overload resolution ile void func(double x) seçilir.
  }			// access control en son yapıldığından private fonksiyona yapılan çağrı geçersiz
```  
 [1] const üye fonksiyonlar içinde sınıfın non-static veri elemanları değiştirilemez.

 [2] const üye fonksiyonlar içinde sınıfın non-const üye fonksiyonları çağrılamaz.
 
 [3] const sınıf nesneleri için sınıfın non-const üye fonksiyonları çağrılamaz.
```cpp
  class Myclass{
	void func(int);		// setter, gizli pointer Myclass*
	void foo()const;	// getter, gizli pointer const Myclass*
	int mx;
  };
  void Myclass::func(int x){
	mx = x;
  }
  void Myclass:foo()const{
	mx = 20;	// geçersiz, [1]
	func(5); 	// geçersiz, [2] const Myclass* to Myclass* dönüşümü
  }
  int main(){
    const Myclass my;
    my.func33);	// geçersiz, [3]
  }
```
#### mutable keyword
Bir üye değişkenin const üye fonksiyonlar içinde değişebilmesini sağlamak için mutable kullanılır.
```cpp
  class Fighter{
	int get_age()const;
	mutable int debug_call_count{};
  };
  int Fighter::get_age()const{
	++debug_call_count;
  }
```
#### this pointer
Değeri non-static üye fonksiyon hangi nesne için çağrıldıysa o nesnenin adresidir. PR value expr.
```cpp  
  void Myclass::func(){
	mx = 10;		// all three are same
	Myclass::mx = 10;	// all three are same
	this->mx = 10;		// all three are same
  }
```

Önemli kullanım yerleri;
 1) Global bir fonksiyona hangi nesne için çağrıldıysa o nesnenin adresinin gönderilmesi.
 ```cpp
  void g(Myclass* p);
  void Myclass::func(){
	g(this);
  }
```
 2) Üye fonksiyonun geri dönüş değerinin, hangi nesne için çağrıldıysa o nesne olmasını sağlamak.
 ```cpp
  class Myclass{
  public:
    Myclass& func();
	void foo();
  };
  Myclass& Myclass::func(){
	return *this;
  }
  int main(){
	Myclass my;
	my.func().foo();
  }
```

Special member functions: Bu fonksiyonların kodları belirli koşullar sağlandığında derleyici tarafından yazılır.
```
 1) default constructor		2) destructor			3) copy constructor
 4) move constructor		5) copy assignment		6) move assignment
```

Constructor: Bir sınıf nesnesini hayata getiren, sınıfın non-static ve non-const üye fonksiyonudur.
 * Overload edilebilir.
 * Bir sınıfın parametre değişkeni olmayan, tüm parametreleri(default argument) alan constructor'ına
default constructor denir.

Destructor: Bir sınıf nesnesinin hayatını sonlandıran, sınıfın non-static ve non-const üye fonksi-
yonudur. Parametre değişkeni almaz. Overload edilemez.

Constructor ve destructor'ın çağrılma sırası FILO mantığına göre yapılır.

# COURSE 08
RAII(Resource Acquisition Is Initialization)
#### constructor initializer list (member initializer list)
```cpp
  class Myclass{
  public:
    Myclass();
  private:
    int m_a, m_b; // önce m_a hayata gelir, constructor initializer list'teki sıra önemli değil!
  };
  Myclass::Myclass : m_a(10), m_b{20}{ // ilk değeri vermek çok daha verimli
	m_a = 30; // assignment. initializer list'e göre farklı bir assembly kodu üretir
  }
  Myclass::Myclass(int a, int b) : m_a{a}, m_b{b}{}
```
*  Parametrenin sınıf türünden olduğu durumda ctor initializer list kullanılmazsa önce default ctor
çağrılır, daha sonra copy assignment fonksiyonu çağrılır.

* Derleyici tarafından yazılan(default edilen) default constructor, sınıfın inline ve public olan bir
üye fonksiyonudur. Sınıfın tüm veri elemanlarını default initialize eder(e.g. int x -> garbage).
```cpp
default member initializer (in-class initializer):
  class Myclass{
  private:
    int mx{10};
	int my = 20;
	int mz(30);	// geçersiz
  };
  Myclass::Myclass : mx(50){} // declaration'dan gelen değeri ezer, mx -> 50
```
ODR(One Definition Rule): Only one definition of any variable, function, class type, enumeration
type or template is allowed in any one translation unit.
 * Aşağıdaki varlıkların birden fazla kez farklı kaynak dosyalarda tanımlanması durumunda, eğer tüm
tanımlar token-by-token aynı ise ODR ihlal edilmiş olmaz.
   1. class definition
   2. inline functions
   3. inline variables
   
Inline expansion is a compiler optimization that replaces a function call site with the body of the
called function. Inline expansion is similar to macro expansion, but occurs during compilation,
without changing the source code(the text), while macro expansion occurs prior to compilation, and
results in different text to be processed by compiler.

Inline functions:
 *  Genellikle kısa ve çok fazla çağrılan fonksiyonlar.
 *  inline derleyiciye yapılan ricadır, garanti edilmez.
 *  Definition ve/veya declaration'da inline keyword kullanılarak yapılır. Fonksiyonun tanımının doğrudan sınıfın tanımı içinde yapılması halinde inline yazılmaz.
```cpp
  inline int func(int x){
	return x*x+5;
  }
```
  
Fonksiyonların delete edilmesi:
 * Fonksiyonun bildirilmesi ama yapılan çağrının syntax hatası oluşturmasına yol açar.
```cpp
   Myclass(const Myclass&) = delete;	// copy ctor is deleted
```
 * Delete edilen fonksiyonlar function overload resolution'a katılırlar.
```cpp
   void func(int);
   void func(double) = delete;
   func(22.78);	// geçersiz, çünkü best viable function -> void func(double)
```

# COURSE 09
Fonksiyonların default edilmesi:
 *  Sadece sınıfların özel üye fonksiyonlarına yapılabilir. Kodun yazımı derleyiciden talep edilir.
```cpp  
   class Myclass{
   public:
     Myclass() = default;  // user-declared
   };
```

Type conversion:
1) Implicit type conversion: e.g. char türünden iki değişkenin toplamı (c1+c2) -> int türündedir.
2) Explicit type conversion: using type-cast operators

#### C-style Cast
```cpp
1) (double)i1/i2 // 1. operand double'a cast edilir, 2. operand implicit type conversion ile double
   1.* i1/i2 // type-cast kullanmadan type conversion, kötü bir yöntem
2) int* func(const int* ptr){
     return (int*)ptr; 
   }
3) int x = 1;
   if(*(char*)&x){
     // little endian
   } else {
	 // big endian
   }
```   
#### C++ Type-cast
1) static_cast: e.g. enum => int, int => double
2) const_cast: e.g. const T* => T*, T* => const T* (zaten implicit olarak yapılır)
3) reinterpret_cast: adres türleri arası dönüşüm için, e.g. int* => double*, double* => char*
4) dynamic_cast: inheritance ile ilgili.

void* => T* dönüşümü hem static_cast hem de reinterpret_cast ile yapılabilir.
```cpp
  int* p1 = static_cast<int*>(std::malloc(n*sizeof(int)));
  int* p2 = reinterpret_cast<int*>(std::malloc(n*sizeof(int)));
```
#### extern C bildirimleri
C'de function overloading olmadığı için C derleyicileri linker için isim oluştururken(name mangling) sadece fonksiyon ismini kullanır. Ancak C++'da derleyiciler fonksiyonun imzasını da kullanır. Bu farklılık, C++ derleyicisi ile derlenen kodlarla C derleyici ile derlenen kodlar arasında link hatasına sebep olur.
```cpp
#ifdef __cplusplus	// C++ derleyicilerinde bu macro defined kabul edilir. C'deki karşılığı __STDC__
  extern "C" {		// extern C bildirimi C derleyicisinde hata verir, bu nedenle koşullu derlenir.
#endif
    void f1(int);
    void f2(int);
#ifdef __cplusplus
  }
#endif
```
#### copy constructor
C++ dilinde eğer bir sınıf nesnesi değerini kopyalama yoluyla aynı türden bir başka sınıf nesnesinden alarak hayata gelecek ise sınıfın copy constructor'ı çağrılır.
```cpp
  T gx; 
  T gy = gx; // or T gy(gx) or T gy{gx}, gy is copy constructed
  
  void func(T ax);
  func(gy); // ax is copy constructed
  
  T foo(){	// T is a class
	return mx;  // return value is copy constructed
  }
```
copy constructor her sınıf için implicitly ve explicitly yazılır. Derleyicinin yazdığı copy ctor sınıfın public, non-static ve inline fonksiyonudur.
```cpp
  Myclass(const Myclass& other) : ax(other.ax), bx(other.bx) {}
```
Rule of Zero: If you can avoid defining any default operations, do!

#### Shallow copy vs Deep copy
* Shallow copy: A new object is created that has an exact copy of the values in the original object.
If any of the fields of the object are references to other objects, just the reference addresses are copied.
* Deep copy: Stores the copy of the original object and recursively copies the objects as well.

Eğer bir sınıf nesnesi kaynak kullanıyorsa ve bu kaynaklar sınıfın elemanları olan pointerlar tarafından gösteriliyorsa, shallow copy pointerları kopyalayacağından deep copy yapmak için copy ctor yazmak gerekir. Bu durumda alınabilecek diğer önlem delete ile copy ctor'ı silmektir.

#### copy assignment 
C++ dilinde eğer bir sınıf nesnesine aynı türden bir başka sınıf nesnesi ile atama yapılıyorsa o sınıfın copy assignment fonksiyonu çağrılır.
```cpp
  Myclass& operator=(const Myclass& other){
	ax = other.ax;
	bx = other.bx;
	return *this;
  }
  Myclass x, y;
  x = y;	// or x.operator=(y)
```

# COURSE 10
Copy assignment fonksiyonuna ihtiyaç duyulmasını anlatan örnek:
```cpp
int main() {
  Sentence first {"first"};
  {
    Sentence second {"second"}; // could not be freed
    second = first;
  }
  first.print(); // undefined behavior due to dangling pointer
} // double deletion for address of "first" object

Sentence& Sentence::operator=(const Sentence& other) {
  if(this == &other) // self-assignment
    return *this;
  free(mp);
  mlen = other.mlen;
  mp = static_cast<char*>(malloc(mlen+1));
  if(!mp)
    exit(EXIT_FAILURE);
  strcpy(mp, other.mp);
  return *this;
}
```

Rule of Five (The Rule of Big 5): If a class defines any of the following, then it should probably explicitly define all five special member functions; destructor, copy constructor, copy assignment operator, move constructor, move assignment operator.

#### Move Semantics: move constructor, move assignment
Bir sınıf nesnesinin hayatının biteceğini ve hiç bir kodun bu sınıf nesnesini kullanmayacağını biliyorsak, move semantics kullanarak kaynakları başka bir nesneye aktarabiliriz.
```cpp
Myclass(const Myclass&); // copy ctor -> L-val ile çağrılır.
Myclass(Myclass&&);      // move ctor -> R-val ile çağrılır.
```
```cpp
Myclass x;               // ctor
Myclass y(x);            // copy ctor
Myclass z(Myclass{});    // move ctor
Myclass k(std::move(x)); // move ctor -> Myclass k(static_cast<Myclass&&>(x);
```
Her sınıfın move constructor'ı olmak zorunda değil. R-val ile çağrı yapıldığında sınıfın move ctor'ı yok ise copy ctor çağrılır.
```cpp
Sentence::Sentence(Sentence&& other) : mp(other.mp), mlen(other.mlen)
{
  other.mp = nullptr;
}
Sentence::~Sentence()
{
  if(mp)  // if(mp != nullptr)
    free(mp);
}
Sentence& Sentence::operator=(Sentence&& other)
{
  if(this == &other) // self-assignment
    return *this;
  free(mp);
  mp = other.mp;
  mlen = other.mlen;
  return *this;
}
```
Bir fonksiyonun parametresi neden sağ taraf referansı olur?
* Taşımak için;
```cpp
void func(Myclass&& x){
  Myclass m(std::move(x)); // move ctor -> eğer std::move() çağrılmasaydı copy ctor çağrılırdı.
}
```
Derleyicinin yazdığı special member functions:
```cpp
Myclass(const Myclass& other): ax(other.ax), bx(other.bx) {} // copy ctor
Myclass(Myclass&& other): ax(std::move(other.ax)), bx(std::move(other.bx)){} // move ctor
Myclass& operator=(const Myclass& other) { // copy assignment
  ax = other.ax;
  bx = other.bx;
}
Myclass& operator=(Myclass&& other) { // move assignment
  ax = std::move(other.ax);
  bx = std::move(other.bx);
}
```
The special member functions can be:
* Not declared: yok, bildirilmemiş.
    ```cpp 
    Myclass(int); // derleyici default ctor yazmaz.
    ```
* Implicitly declared: derleyici tarafından bildirilmiş (defaulted or deleted).
    ```cpp
    class Myclass {
    public:
	  // Myclass() = delete; const bir değişken default init ile hayatına başlayamayacağı için default ctor derleyici tarafından delete edilir.
    private:
	  const int x;
    };

    class A{
      A();
    };
    class B{
      // B() = delete; A sınıfının ctor'ı private olduğu için B sınıfı tarafından default init yapılamaz. Bu nedenle B sınıfının default ctor'ı derleyici tarafından delete edilir.
      A ax;
    };
	```
* user declared: programcı tarafından bildirilmiş (user defined, defaulted or deleted).
  ```cpp
  Myclass();
  Myclass() = default;
  Myclass() = delete;
  ```

| USER DECLARES| default ctor  | destructor    | copy ctor     | copy assign   | move ctor     | move assign   |
|--------------|---------------|---------------|---------------|---------------|---------------|---------------|
| nothing      | defaulted     | defaulted     | defaulted     | defaulted     | defaulted     | defaulted     |
| ctor         | not declared  | defaulted     | defaulted     | defaulted     | defaulted     | defaulted     |
| default ctor | user declared | defaulted     | defaulted     | defaulted     | defaulted     | defaulted     |
| destructor   | defaulted     | user declared | * defaulted   | * defaulted   | not declared  | not declared  |
| copy ctor    | not declared  | defaulted     | user declared | * defaulted   | not declared  | not declared  |
| copy assign  | defaulted     | defaulted     | * defaulted   | user declared | not declared  | not declared  |
| move ctor    | not declared  | defaulted     | deleted       | deleted       | user declared | not declared  |
| move assign  | defaulted     | defaulted     | deleted       | deleted       | not declared  | user declared |

-> Compiler implicitly declares

* Yıldız işareti olan durumların tercih edilmemesi gerekir, kullanıcı bu durumlarda copy member fonksiyonları kendisi yazmalıdır.

```cpp
class Myclass{ // non-copyable & non-movable
public:
  Myclass(const Myclass&) = delete;
  Myclass& operator=(const Myclass&) = delete;
  // user declared and deleted copy members: rows 5 and 6 from above table
  // compiler does not implicitly declare move ctor and move assign
};
```

Defaulted move members defined as deleted actually behave as not declared.

# COURSE 11
Mandatory copy elision: C++17 ile zorunlu hale gelen, derleyicinin kopyalamadan kaçınmak için yaptığı kod düzenlemesidir.
#### Temporary objects: En sık fonksiyon parametresine verilmek üzere kullanılır.
```cpp
Myclass{10}; // ismi yok, construct edildikten sonra bir sonraki statement evaluate edilmeden destruct edilir.
```
```cpp
void f1(Myclass);        // f1(Myclass{}); call by value
void f2(const Myclass&); // f2(Myclass{});
void f3(Myclass&&);      // f3(Myclass{});
void f4(Myclass&);       // f4(Myclass{}); syntax hatası
```
Life extension:
```cpp
const Myclass& r = Myclass{}; // r nesnesinin ömrünün sona ereceği yere kadar destruct edilmez.
```
Bazı durumlarda derleyiciler implicit olarak temporary objects oluşturabilirler.

```cpp
void func(Myclass x);
int main() {
  func(Myclass()); // önce geçici nesnenin ctor'ı çağrılır.
  // sonra parametre değişkeni için copy ctor çağırmak yerine mandatory copy elision uygulanarak ikinci bir çağrı yapmaktan kaçınılır.
  // buradaki işlem sınıfın move ctor'ı olup olmamasından bağımsızdır.
}
```
#### Return Value Optimization
```cpp
Myclass foo(){
  return Myclass{};
}
int main(){
  Myclass x = foo(); // önce geçici nesne için default ctor çağrılır.
  // sonra x değişkeni için copy/move ctor çağırmak yerine mandatory copy elision uygulanır.
}
```
#### Named Return Value Optimization
Mandatory olmayan ama çoğu derleyicinin kullandığı bir copy elision yöntemidir.
```cpp
Myclass func(){
  Myclass mx;
  return mx;
}
int main(){
  Myclass x = func();
}
```
Modern C++'da copy elision sayesinde fonksiyon parametresine adres geçmek tercih edilmemeye başlamıştır.
```cpp
std::string foo(); // more preferable
void func(std::string& r);
```
#### Conversion constructor
```cpp
class Myclass{
public:
  Myclass();
  Myclass(int);
};
void func(Myclass x);
void foo(const Myclass&);
int main(){
   Myclass m;
   m = 10;    // m = Myclass{10}; önce sınıfın int parametreli ctor kullanılarak bir geçici nesne oluşturulur.
   // sonra copy assignment operator fonksiyonu ile kopyalanır. Geçici nesne destruct edilir.
   func(15);  // func(Myclass{15});
   foo(7);    // foo(Myclass{7});
   m = 2.5;
   func(4.75); // standard conv.(double->int) + user defined conv.(int->Myclass)
}
```
* user-defined conversion
	* conversion constructor
	* type-cast operator function
* Derleyiciler, standard conversion ve user-defined conversion yapılması gereken dönüşümleri yapmakla yükümlüdür.
```cpp
int main(){
  string str;
  str = "this is a sentence"; // conversion ctor çağrıldı.
}
```
#### Explicit constuctor: Sadece explicit olarak tür dönüşümü yapan constructor.
```cpp
class Myclass{
public:
  Myclass() = default;
  explicit Myclass(int);
};
int main(){
  Myclass m;
  m = static_cast<Myclass>(10); // m = 10; syntax hatası
}
```
```cpp
class Myclass{
public:
  Myclass(int); // explicit yazılırsa aşağıdaki çağrıda copy init syntax hatası verir.
};
int main(){
  Myclass m = 10; // burada geçici nesne oluşturulmaz, doğrudan Myclass(10) şeklinde çağrı yapılır.
}
```
Tek parametreli constructorlarda explicit anahtar sözcüğü kullanımı hataların önüne geçer.
```cpp
class Myclass{
public:
  explicit Myclass(int); // viable function olmaktan çıktığı için function overload setinde yer almaz.
  Myclass(double);
};
int main(){
  Myclass x = 10; // Myclass(double) çağrılır.
}
```

## Dynamic Storage Class
- Maliyeti yüksek,
- Kodlama hatası riski çok yüksek,
- Test, debug veya değişiklik yapmak daha zor olduğu için mecbur kalmadıkça automatic storage class kullanılmalıdır.

Dinamik ömürlü nesne hayata new operatörü ile gelir, delete operatörü ile ömrü son bulur.
new operatörü ile oluşturulan ifadeler birer pointerdır, adrese karşılık gelir.
```cpp
Myclass* p = new Myclass; // default initialization
p->func();
delete p;
```
Derleyici C++ dilinin standart bir fonksiyonuna çağrı yaparak ihtiyaç duyulan dinamik belleği tutmaya çalışır. Elde edilen bellek bloğunun adresi ile sınıfın ctor'ına çağrı yapılır.
```cpp
void* operator new(size_t n); // C karşılığı malloc
// new başarısız olursa throw exception, malloc başarısız olursa return nullptr
```
delete operatörü ile bir sınıf nesnesi delete edildiğinde önce o nesneye ait sınıfın destructor'ı çağrılır. Daha sonra dinamik bellek free edilmesi için operator delete fonksiyonu çalışır.
```cpp
void operator delete(void*); // C karşılığı free
```

# COURSE 12
İfadeler değil operator fonksiyonları(new() or delete()) overload edilebilir.
* new ifadesi = operator new() => constructor
* delete ifadesi = destructor => operator delete()

C# ve Java gibi dillerde bulunan garbage collector sistemi C++'da bulunmadığı için new ile hayata gelen dinamik nesnelerin programcı tarafından delete edilmesi gerekir.

new operatörü ile const bir nesne oluşturulabilir.
```cpp
const Myclass* p = new const Myclass{12};
```
array new and array delete:
```cpp
Myclass* p = new Myclass[5]; // ctor is called 5 times.
delete[] p;
```
Static data members of a class: Sınıf nesnesinin değil, sınıfın değişkenleridir.
```cpp
class Myclass{
public:
  static int x; // declaration
};
int Myclass::x = 5; // definition
```
#### Complete Type: Derleyicinin türün geçtiği noktada sınıfın tanımını gördüğü türlere denir.
#### Incomplete Type: Derleyicinin türün geçtiği noktada sınıfın bildirimini gördüğü türlere denir.
```cpp
class Myclass; // class declaration (forward declaration)
```
+ Sınıf türünden bir pointer veya reference değişken oluşturabilir.
	```cpp
    Myclass* p = nullptr;
	``` 
+ Fonksiyon bildirimlerinde parametrelerin türü veya geri dönüş türü olabilir.
	```cpp
    Myclass& foo(Myclass);
	``` 
+ extern kullanılarak yapılan bildirimlerde kullanılabilir.
	```cpp
    extern Myclass mx[];
	``` 
+ Type alias bildirimlerinde kullanılabilir.
	```cpp
    typedef Myclass* MPTR;
    using MPTR = Myclass*;
	``` 
+ Sınıfın static veri elemanı bildirimi yapılabilir.
	```cpp
    class Myclass{
      static A ax;
    };
    class Myclass{
      static Myclass mx;
    }
	``` 
+ Sınıfın non-static veri elemanı yapılamaz.
+ Bir sınıf türünden nesne oluşturulamaz.
+ sizeof operatörü ile kullanılamaz.
+ pointer değişken tanımlanabilir ama dereference edilemez.
	```cpp
    Myclass* foo();
    int main(){
      Myclass* p = foo();
      auto x = *p; // syntax hatası
    }
	``` 
!!! Bir header dosyasında başka bir header dosyası yalnızca forward declaration yeterli olmadığı durumlarda include edilmelidir.

pimpl idiom: pointer implementation
* Sınıfın private bölümünü başlık dosyasında gizlemek için kullanılır.
* Implementation hakkında fikir vermek istenmeyen durumlarda tercih edilir.
* Başka başlık dosyalarını eklemenize gerek kalmaz, kodun diğer dosyalara olan bağımlılığını azaltır.

Sınıfın static veri elemanı main fonksiyon çağrılmadan önce hayata gelir ve bu veri elemanını kullanmak için sınıf türünden bir nesneye ihtiyaç yoktur.
```cpp
Myclass::x = 5;
```
Sınıf objesi üzerinden de sınıfın static veri elemanına erişilebilir.
```cpp
Myclass m;
Myclass* p = &m;
Myclass::x = 5;  // changes static member variable of class
p->x = 6;        // changes static member variable of class
m.x = 7;         // changes static member variable of class
```
Bir sınıfın const ve integral type olan static değişkenlerine sınıf bildirimi içinde ilk değer verilebilir.
```cpp
class Myclass{
  static int x = 10;               // geçersiz
  static const int y = 5;          // geçerli
  static const double z = 2.0;     // geçersiz
  static constexpr int t = 7;      // geçerli
  static constexpr double k = 3.4; // geçerli
};
```
#### inline variables
```cpp
// header file  |
extern int x;   |  // header file
                |  inline int x = 10;
// source file  |   
int x = 10;     |
```
In C++17, we can initialize the static variables inside the class using inline variables.
```cpp
class Myclass {
public:
  inline static int x = 10; // geçerli
};
inline void foo(){}
```
Static member functions of a class;
* Sınıf nesnesinin değil, sınıfın üye fonksiyonudur.
* this pointerları yoktur, bu nedenle sınıfın non-static üye elemanlarını kullanamaz, üye fonksiyonlarını çağıramaz.
* this pointerları olmadığı için const olamaz. e.g. static void foo() const; // geçersiz
* Global fonksiyonlara göre farkları;
	* namespace scope yerine class scope,
	* sınıfın private bölümüne erişimi var,
	* access kontrole tabidir.

# COURSE 13
Sınıfın static veri elemanı kullanım örnekleri:
* Bir sınıf türünden üretilen nesnelerin sayısını saymak (kaçı hayatta, toplam kaç tane üretildi vs)
* Sınıf nesnelerinin birbirlerine erişimini sağlamak (hayata gelen nesnelerin adreslerini container oluşturup saklamak - inline static std::vector<Fighter*> vec;)

!!! Header dosyaları içerisinde "using" bildirimi yapılmaz.
#### named constructor idiom: Client kodların constructora doğrudan çağrı yapmaları yerine static bir üye fonksiyon çağrılarak nesne oluşturulması durumudur.
```cpp
class Myclass{
public:
  static Myclass* createObject(){
    return new Myclass;
  }
private:
  Myclass();
};
```
Singleton: Creational design pattern; ensures only one instance of an object is created. Also, global access should be provided.
```cpp
class Singleton{
public:
  static Singleton& get_instance(){
    if(!mp)                  // Meyer's Singleton
      mp = new Singleton;    // static Singleton mp;
    return *mp;              // return mp;
  }
  Singleton(const Singleton&) = delete;
  Singleton& operator=(const Singleton&) = delete;
  void foo();
private:
  Singleton();
  inline static Singleton* mp = nullptr;
};

int main(){
  Singleton::get_instance().foo();
}
```
Sınıfın static veri elemanına ilk değer veren ifadelerdeki isimler önce class scope içerisinde aranır.
```cpp
class Myclass{
public:
  static int foo(){return 1;}
  static int x;
};
int foo(){return 2;}
int Myclass::x = foo(); // önce class scope bakıldığı için, x = 1
```
#### friend: Bir sınıfın private bölümüne sınıf dışından erişimi sağlar.
* Bir global fonksiyon (namespace) için yapılabilir.
    ```cpp
    class Myclass {
    public:
      friend int func();
    private:
      int mx;
      void foo();
    };
    
    Myclass n;
    int func(){
      Myclass m;
      m.mx = 10; // valid
      m.foo();   // valid
      n.mx = 7;  // valid
    }
	```
* Bir başka sınıfın üye fonksiyonu için yapılabilir, sınıf complete type olmalıdır.
    ```cpp
    class Data {
    public:
      int func(int);
    };
    class Myclass {
    public:
      friend int Data::func(int);
    private:
      int mx;
    };
    
    int Data::func(int x){
      Myclass m;
      m.mx = x; // valid
    }
	```
* Bir sınıf için yapılabilir, complete type olma şartı yoktur.
    ```cpp
    class Data {
    public:
      int func(int);
    };
    class Myclass {
    public:
      friend class Data;
    private:
      int mx;
    };
    
    int Data::func(int x){
      Myclass m;
      m.mx = x; // valid
    }
	```
	!!! friend bildiriminin sınıfın public, private veya protected alanında yapılması arasında herhangi bir fark yoktur.
	#### Operator Overloading: Bir sınıf nesnesinin bir operatörün operandı yapıldığında derleyicinin ifadeyi bir fonksiyon çağrısına dönüştürmesine denir. Bu şekilde çağrılan fonksiyonlara operator functions denir. Bu fonksiyonlar global operator functions ve (non-static) member operator functions olarak iki çeşittir.
* Compile-time'a yöneliktir, run-time için herhangi bir ek maliyet getirmez.
* Sınıfı kullanan programcıların daha kolay kod yazmasını sağlar.
    ```cpp
    if(is_equal(a.multiply(b).add(c),x))  =>  if(a*b+c == x)
	```
* C++ dilinde olmayan operatör overload edilemez, e.g. a@b.
* Operandlardan en az birinin sınıf türünden veya enum türünden olması gerekir.
* Bazı operatörler overload edilemez ( :: , . , ? , sizeof , typeid, .*)
* Bazı operatörler global olarak overload edilemez ([ ] , ( ) , typecast , -> , =)
* ( ) operatörü hariç operatör fonksiyonları default argument alamaz.
* Operatör fonksiyonlarında operatörlerin arity özelliği(binary(+ , <) - unary(! , ~) değiştirilemez.
    ```cpp
          -global op-     -member op-
    !x   operator!(x)    x.operator!()
    a>b  operator>(a,b)  a.operator>(b)
	```
* Operatörlerin öncelik seviyesi(priority-precedence) ve öncelik yönü(associativity) değiştirilemez.
    ```cpp
    x = !a * b + c > d;
    x.operator=(operator>(operator+(operator*(operator!(a),b),c),d))  // hepsi global op. (except = )
    x.operator=(a.operator!().operator*(b).operator+(c).operator>(d)) // hepsi member operator
    ```

# COURSE 14
Neden global operator fonksiyonlar var?
* 10 + x işlemi bu sırayla bir üye operatör fonksiyonu yardımıyla yazılamaz. e.g., operator+(10, x)
* Kullanmak istenilen yapı üçüncü parti bir sınıfa ait olabilir. Dolayısıyla o sınıfların koduna müdahale edilemeyeceğinden global operatör fonksiyonu yazılır.

L-value expression oluşturan ifadelere karşılık gelen operatör fonksiyonlarının geri dönüş değeri referans olmalıdır. e.g., =, ++(prefix), --(prefix).
```cpp
class A{
public:
  bool operator<(const A&) const;
  A& operator+=(const A&);
};
```
Bazı operatörlerin const ve const olmayan nesneler için ayrı ayrı overload edilmesi gerekir.
```cpp
class Vector{
public:
  int& operator[](size_t);            // vec[5]
  const int& operator[](size_t)const; //cvec[5]
};
```
Simetrik binary operatörlerin global overload edilmesi önerilir. e.g., >
Sınıf nesnesini değiştiren(yan etkisi olan) operatör fonksiyonlarının üye operatör fonksiyonu olması önerilir. e.g., ++. İstisnası enum türleridir, mecburen global olarak overload edilirler.

int wrapper örneği - stream operatörlerini overload etme
```cpp
// mint.h
#include <iosfwd> // <ostream> yerine daha hafif, sadece incomplete declaration içerir.
class Mint{
public:
  explicit Mint(int x = 0) : mval{x}{}
  friend std::ostream& operator<<(std::ostream&, const Mint&);
  friend std::istream& operator>>(std::istream&, Mint&);
  friend bool operator<(const Mint& x, const Mint& y){
    return x.mval < y.mval;
  }
  friend bool operator==(const Mint& x, const Mint& y){
    return x.mval == y.mval;
  }
private:
  int mval;
};
```
```cpp
// mint.cpp
#include "mint.h"
#include <ostream>
#include <istream>

std::ostream& operator<<(std::ostream& os, const Mint& m){ // inserter
  return os << "(" << m.mval << ")";
}
std::istream& operator>>(std::ostream& is, Mint& m){ // extractor
  return is >> m.mval;
}
```
```cpp
// main.cpp
int main(){
  Mint a, b, c;
  std::cout << a << " " << b << " " << c << "\n";
}
```

STL containerları(vector, map etc.) ile user-defined türler kullanılacağı zaman operator< overload edilmelidir, aksi halde syntax hatası alınır. Bazı durumlarda operator== da overload edilmelidir.

equality -> a == b 

equivalence -> !(a < b) && !(b < a)

Aritmetik operatörlerde + ve += gibi ilişkili operatörler beraber overload edilmelidir.
```cpp
class Mint{
public:
  Mint& operator+=(const Mint& r){
    mval += r.mval;
    return *this;
  }
private:
  int mval;
};

inline Mint operator+(const Mint& left, const Mint& right){
  return Mint{left} += right;
}
or alternatively
inline Mint operator+(Mint& left, const Mint& right){
  return left += right; // copy elision for left
}
```
Unary operatörlerin overload edilmesi
```cpp
Mint operator+()const{
  return *this;
}
Mint operator-()const{ // sign operator
  return Mint{-mval};
}
Mint& operator++(){ // prefix
  ++mval;
  return *this;
}
Mint operator++(int){ // postfix, int is a dummy parameter
  Mint temp{*this};
  ++*this; // operator++();
  return temp;
}
```

array elemanına ulaşma
```cpp
int a[3] = {5, 7, 9};
cout << a[1] << 1[a] << *(a + 1) << *(1 + a); // output: 7777 all same!
```
operator [ ] fonksiyonunun overload edilmesi için sınıfın array-like veya pointer-like olması beklenir.
```cpp
class Array{
public:
  explicit Array(size_t n) : msize{n}, mp{new int[msize]}{}
  size_t size() const{ return msize; }
  int& operator[](size_t idx){
    return mp[idx];
  }
  const int& operator[](size_t idx)const{
    return mp[idx];
  }
private:
  size_t msize;
  int* mp;
};
```

dereferencing(indirection - *ptr) operatörün overload edilmesi pointer-like sınıflar için yapılır. Iterator veya smart pointer sınıfları örnek verilebilir.

ok operatörü binary bir operatör olmasına rağmen unary olarak overload edilir.
```cpp
p->x   =>   p.operator->()->x
```
```cpp
class Smartptr{
public:
  Smartptr() : mp{nullptr}{}
  Smartptr(Resource* p) : mp{p}{}
  Smartptr(const Smartptr&) = delete;            // non-copyable due to these two delete
  Smartptr& operator=(const Smartptr&) = delete; // non-copyable due to these two delete
  Smartptr(Smartptr&& other) : mp{other.mp}{
    other.mp = nullptr;
  }
  ~Smartptr(){
    if(mp)
      delete mp;
  }
  Resource& operator*(){
    return *mp;
  }
  Resource* operator->(){
    return mp;
  }
private:
  Resource* mp;
};
```

# COURSE 15
#### function call operator ( ):
C dilinde foo() bir fonksiyon, function pointer veya fonksiyonel makro olabilir.
C++ dilinde ise yukarıdakilere ek olarak bir sınıf nesnesi de olabilir.
Fonksiyon çağrı operatörünü overload edilerek, sınıf nesnelerin fonksiyon çağrı operatörünün operandı olarak kullanabilen sınıflara "functor class" denir.
Default argument alabilen tek operatör fonksiyonudur.
```cpp
class Functor{
public:        // parametresiz veya birden fazla parametreli olabilir.
  void operator()(int x = 5){} // dönüş türü farklı olabilir.
};
```
typecast operator functions:
```cpp
class Counter{
public:
  explicit operator int() const {
    return mval;
  }
private:
  int mval;
};

int main(){
  Counter c{4};
  int ival = static_cast<int>(c);
  // int ival = c; syntax hatası çünkü explicit.
  
  /* explicit olmasına rağmen logic context için kullanıldığında
  implicit dönüşüm yapılır. e.g; if, while, !, &&, ?, ||  */
  unique_ptr<int> uptr {new int{5}};
  if(uptr) // if(uptr.operator bool())
    cout << "this is not a null pointer";
}
```
enum class tipi değişkenler için global operator overload function yazılır.
```cpp
enum class Weekday{Monday, Tuesday, Wednesday, Thursday, Friday};
Weekday& operator++(Weekday& wd){ // prefix
  return wd = wd == Weekday::Friday ? Weekday::Monday : static_cast<Weekday>(static_cast<int>(wd)+1);
}
std::ostream& operator<<(std::ostream& os, const Weekday& wd){
  static const char* const pdays[]{
    "Monday", "Tuesday", "Wednesday", "Thursday", "Friday"
  };
  return os << pdays[static_cast<int>(wd)];
}

int main(){
  Weekday wd{Weekday::Monday};
  ++wd;
  cout << wd;
}
```
#### Delegating Constructor (C++ 11)
```cpp
class Myclass{
public:
  Myclass(int x, int y, int z) : mx(x), my(y), mz(z){}
  Myclass(int a) : Myclass(a, a, a){} // delegating ctor
private:
  int mx, my, mz;
};
```
if with initializer:
```cpp
if(int x = foo()) // C++'in ilk standartlarından beri mevcut, C'de yok.
if(int x = foo(); x > 10) // C++17'de eklendi, if with initializer.
```
#### Structured Binding
```cpp
struct Data{
  int a, b;
  double c;
};

int main(){
  Data mydata = {10, 20, 1.3};
  auto [x, y, z] = mydata; // genelde ihtiyaç olmayan yerine _ kullanılır.
  // auto [x, y, _] = mydata; 
}
```
```cpp
struct Person{
  std::string name;
  int age;
  double wage;
};
Person get_person(){
  return {"alican", 30, 5000.0};
}

int main(){
  if(auto [name, age, salary] = get_person(); age > 40){...}
}
```
Structured binding in range based for loop:
```cpp
for(auto person: pvector) -> for(auto [name, age, wage] : pvector)
```
Structured binding yaparken derleyici eşitliğin sağındaki yapı ifadesinden bir nesne oluşturmuş gibi davranır.
```cpp
struct myStruct{
  int a[5];
  double dval;
};

int main(){
  myStruct ms;
  auto [arr, salary] = ms; // arr türü pointer değil int[5].
}
```
#### Reference Qualifiers
```cpp
class Myclass{
public:
  void func()& { }
  void func()const& { }
  void func()&& { }
  void func()const&& { }
};

int main(){
  Myclass m;
  const Myclass cm;

  m.func();         // void func()&
  cm.func();        // void func()const&
  Myclass{}.func(); // void func()&&
  move(m).func();   // void func()&&
  move(cm).func();  // void func()const&&
}
```

# COURSE 16
#### Trailing Return Type: 
```cpp
int func(){ }
auto func()->int{ } // same with above
```
Useful in function templates when the type of the return value depends on template parameters. When auto is used in conjunction with a trailing return type, it just serves as a placeholder for whatever the decltype expression produces, and does not itself perform type deduction.
```cpp
template<typename Lhs, typename Rhs>
auto Add(const Lhs& lhs, const Rhs& rhs) -> decltype(lhs + rhs)
{
  return lhs + rhs;
}
```
#### Nested Types (Type Members): A type that declared within the scope of another class.
To refer to a nested class from a scope other than its immediate enclosing scope, you must use a fully qualified name.
```cpp
class Enclosed{ // Enclosing class, nested type'ın private alanına erişemez.
  class Nested{ // Nested type, enclosing class'ın private alanına erişebilir.
    int x;
  };
  enum Color{White, Black};
  typedef int Word; // using Word = int;
public:
  static Nested foo;
};

int main(){
  auto x = Myclass::foo(); // valid
  Enclosed::Nested y = Enclosed::foo(); // not valid since Nested has only private access.
}
```

Sınıfa başka bir sınıf türünden static veri elemanı koyulacaksa bu sınıfların tanımını içeren başlık dosyalarını include etmek yerine forward declaration kullanılmalıdır. Aynı durum veri elemanları pointer veya referans ise de geçerlidir.

#### Composition
An object is a part of another object. The object that is a part of another is known as a subobject. When a composition is destroyed, then all of its subobjects are destroyed as well.
```cpp
class Member{}; // member class
class Owner{   // owner class
  Member mx;
public:
  Owner(const Owner& other) : mx(other.mx); // ctor initializer list
};

int main(){
  Owner ox; // Member ctor->Owner ctor->Owner dtor->Member dtor
}
```
!!! Eğer owner class için copy ctor yazılırsa, member classların copy ctor ile hayata getirilebilmesi için constructor initializer list kullanılmalıdır. Aksi halde default ctor'ları çağrılır.
```cpp
class Person{
public:
  Person(const char* pname){ // önce ctor çağrılır, sonra copy assignment yapılır.
    mname = pname;
  }
private:
  std::string mname;
};

class Person{
public:
  Person(const char* pname) : mname(pname){ } // doğrudan parametreli ctor çağrılır.
private:
  std::string mname;
};
```

#### Namespaces
A namespace is a declarative region that provides a scope to the identifiers (the names of types, functions, variables, etc.) inside it. Namespaces are used to organize code into logical groups and to prevent name collisions that can occur especially when your code base includes multiple libraries.
```cpp
namespace myspace{
  int x = 10;
}
namespace myspace{
  int y = 5;
}
// derleyici üstteki alanları kümülatif olarak değerlendirir ve birleştirir.
using namespace myspace;
int main(){
  myspace::x = 7;
  myspace::y = 6;
  x = 5;
  y = 4; 
}
```

# COURSE 17
using bildiriminin kapsamı(scope) vardır.
```cpp
namespace ns{
  int x;
}
void func(){
  using ns::x; // sadece func() içinde x kullanılabilir.
  x = 5;       // 'int x' şeklinde yeni bir değişken tanımlanamaz.
}              // 'using namespace ns' kullanılsaydı 'int x' yapılabilirdi.
```

#### Argument Dependent Lookup (ADL): Koenig lookup
The compiler can use argument-dependent name lookup to find the definition of an unqualified function call. The type of every argument in a function call is defined within a hierarchy of namespaces, classes, structures, unions, or templates. When you specify an unqualified postfix function call, the compiler searches for the function definition in the hierarchy associated with each argument type.
```cpp
namespace ns{
  class Myclass{};
  void foo(std::vector<Myclass>){ }
  void func(int){ }
}

int main(){
  std::vector<ns::Myclass> myvec;
  foo(myvec); // valid, the compiler finds ns::foo() in namespace ns, which is where the type of argument myvec is defined.
  func(67);   // not valid.
}
```
ADL'nin bir önceliği yoktur. Global namespace içerisinde aynı isimli bir fonksiyon ile isim çakışması durumunda compiler hatası alınır.

#### std::endl
Inserts a newline character into the output sequence os and flushes it as if by calling os.put(os.widen('\n')) followed by os.flush(). Try to use '\n' if flushing is not necessary.

#### inline namespaces
In contrast to an ordinary nested namespace, members of an inline namespace are treated as members of the parent namespace. This characteristic enables argument dependent lookup on overloaded functions to work on functions that have overloads in a parent and a nested inline namespace. It also enables you to declare a specialization in a parent namespace for a template that is declared in the inline namespace.
```cpp
namespace A{
  namespace B{
    inline namespace C{
      int x;
    }
  }
}

int main(){
  A::B::x = 7; // do not need to specify namespace C.
}
```

#### Anonymous(unnamed) namespaces
Useful when you want to make variable declarations invisible to code in other files (i.e. give them internal linkage) without having to create a named namespace. All code in the same file can see the identifiers in an unnamed namespace but the identifiers, along with the namespace itself, are not visible outside that file—or more precisely outside the translation unit.
```cpp
namespace { 
  int MyFunc(){} 
}
```

const değişkenler global olmaları durumunda default olarak internal linkage ile bağlanır. Dolayısıyla header dosyasında const değişken tanımı yapılırsa ODR ihlal edilmiş olmaz; header dosyasının include edildiği her kaynak dosyasında ayrı bir değişken hayata gelmiş olur. Eğer bu değişken inline anahtar sözcüğü ile nitelendirse yalnızca tek bir değişken hayata gelir.

#### Namespace aliases
Namespace names need to be unique, which means that often they should not be too short. If the length of a name makes code difficult to read, or is tedious to type in a header file where using directives can't be used, then you can make a namespace alias which serves as an abbreviation for the actual name.
```cpp
namespace a_very_long_namespace_name { class Foo {}; }
namespace AVLNN = a_very_long_namespace_name;
void Bar(AVLNN::Foo foo){ }
```

### std::string
size: Returns the number of elements in the string.
capacity: Returns the number of characters that the string has currently allocated space for.

The Small Buffer Optimization (SBO) is a well-known optimization technique that can reduce the number of dynamic memory allocations that occur at program execution time. Stack memory allocations are faster than heap memory allocations. The idea behind SBO is that if an object that would normally be created on the heap is small enough to fit inside of a small buffer allocated for the object, the application will use the buffer for storage instead of requesting memory to be allocated dynamically from the heap. This reduces the runtime overhead typically associated with dynamic memory allocation and can thereby improve runtime performance.

Eğer baştan yazının ulaşacağı maksimum uzunluk biliniyorsa bellek alanı reserve fonksiyonu ile tek seferde ayrılabilir. Bu şekilde birden fazla reallocation yapılmanın önüne geçilebilir.

Dinamik dizilerde capacity otomatik olarak artırılır ama otomatik olarak azaltılmaz. shrink() fonksiyonu ile kapasite daraltma yapılabilir.

string ifadesinin son elemanı bir pointer ile tutulduğundan sabit zamanda O(1) sondan ekleme veya çıkarma yapılabilir.

STL containers -> Sequence containers -> vector, deque, list, forward_list, array, string

```cpp
string str;
str.size();        // str.length() de kullanılabilir.
str.empty();       // bool olarak boş olup olmadığını döner.
str.capacity();    // bellek alanının alabileceği max karakter, taşarsa reallocation.
str.reserve(500);  // reserve edilen alan tam olarak verilen sayı olmayabilir.
```

# COURSE 18
string, basic_string sınıf şablonu kullanılarak oluşturulur.
```cpp
string => basic_string<char, char_traits<char>, allocator<char>>
```
#### std::basic_string<CharT,Traits,Allocator>::npos
This is a special value equal to the maximum value representable by the type size_type. The exact meaning depends on context, but it is generally used either as end of string indicator by the functions that expect a string index or as the error indicator by the functions that return a string index.

#### std::initializer_list (not to be confused with member initializer list)
initializer_list bir fonksiyona argüman olarak verilirken her zaman referans semantiği ile geçer, kopyalama yapılmaz. Bu nedenle fonksiyon parametresinde const veya ref diye belirtmek gereksiz (const& by default). initializer_list öğeleri değiştirilemez.
```cpp
std::initializer_list<int> mylist{2, 5, 8};
class Myclass{
public:
  Myclass(std::initializer_list<int>);
  Myclass(int);
};

int main() {
  Myclass m1{15}; // calls 1st constructor
  Myclass m2(12); // calls 2nd constructor
}
```

Birden fazla kez bir fonksiyonu çağırmak için;
```cpp
for (auto val : {5, 7, 9})
{
  func(val);
}
```

containerların begin() fonksiyonu containerda tutulan ilk öğenin konumunu döndürür. end() üye fonksiyonu ise sondan bir sonraki olmayan bir öğenin konumunu döndürür. Dizilerin de aynı semantik yapıyı kullanabilmesini sağlayan global begin() ve end() fonksiyonları da vardır.
```cpp
vector<int> ivec{2, 4, 6}; // ivec.begin(), ivec.end()
int a[]{2, 4, 6}; // begin(a), end(a)
```

String içinde tutulan yazının başlangıç adresini elde etmek için;
```cpp
str.c_str() or 
str.data() or 
&str[0] or 
&*str.begin()
```

Reference to the last element of a non-empty string;
```cpp
str[str.size() - 1] or
str.at(str.size() - 1) or
str.back() or
*(str.end() - 1) or
*str.rbegin()
```

c_str fonksiyonu ile elde edilen pointer, reallocation vs sebeplerle dangling pointer haline gelebilir.
```cpp
std::string str{"football"};
const char* p = str.c_str();
str += "coming home"; // if reallocation, p will be a dangling pointer
```

std::basic_string<CharT,Traits,Allocator>::at function returns a reference to the character at specified location pos. Bounds checking is performed, exception of type std::out_of_range will be thrown on invalid access.
```cpp
string str{"ali"};
auto c = str[5];     // undefined behaviour, no exception 
auto cc = str.at(5); // out-of-range exception
```

Range-based for loops:
```cpp
for (auto val : con) -> val local variable, kopyalama yapılır
for (auto& val : con) -> containerdaki öğeye referans, yazma amaçlı
for (const auto& val : con) -> containerdaki öğeye const referans, okuma amaçlı
for (auto&& val : con) -> forwarding reference
```
