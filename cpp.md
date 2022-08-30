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
