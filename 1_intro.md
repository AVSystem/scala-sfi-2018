## Podstawy składni Scali
    
Podstawowa składnia Scali jest analogiczna do innych języków obiektowych. Ten krótki wstęp prezentuje najważniejsze elementy, które będą niezbędne do dalszej nauki. W trakcie warsztatów mogą przydać się poniższe ściągi. 

[Scalacheat](http://docs.scala-lang.org/cheatsheets/index.html) - ściąga z najważniejszymi elementami składni Scali.

[Java/Scala cheat sheet](http://rea.tech/java-to-scala-cheatsheet/) - ściąga pokazująca jak przepisać Javę na Scalę. 
    
###  Definiowanie stałych, zmiennych, klas oraz metod

Jednymi z podstawowych elementów Scali są:
  * stałe `val`.
  * stałe inicjalizowane "leniwie" tj. przy pierwszym odwołaniu `lazy val`.
  * zmienne `var`.
  * metody `def`.

Za pomocą słówka kluczowego `class` definiujemy klasy. W Scali klasa nie ma opisanego konstruktora w postaci osobnej metody. Zamiast tego interfejs konstruktora definiujemy zaraz po nazwie klasy, a implementacją konstruktora jest całe ciało klasy.

```scala
class Foo(arg: Int) {
  val a: Int = arg
  lazy val b: Int = 2
  var c: Int = 3
  def d: Int = 4
  
  println("Print from constructor")
}
```
    
### Inferencja typów i _type ascription_
Nie ma potrzeby definiowania typów zmiennych i stałych czy też typów zwracanych przez metody - może to zostać wyinferowane przez kompilator. Możliwe jest również podanie _explicite_, jakiego typu jest dane wyrażenie (_type ascription_), pod warunkiem, że jest to bezpieczne rzutowanie.
   
```scala
class Foo {
  val a = 1
  val b: Long = 1
  val c = 1: Long
}
```

### Klasy

```scala
class Foo(var x: Int, val y: String, z: Double) extends Bar(x, y) {
  assert(x > 0, "positive please")
  var a: String = y * x
  val readonly: Int = 5
  private var secret = 1
  
  def this(x: Int, y: String, zStr: String) = this(x, y, zStr.toDouble)
}
```

Powyższy przykład przedstawia klasę `Foo`, która:
 * w podstawowym konstruktorze przyjmuje trzy parametry (`Int`, `String` oraz `Double`)
 * rozszerza klasę `Bar` jednocześnie przekazując do jej konstruktora dwa parametry
 * ma publicznie dostępne pola: `x` i `a` z możliwą zmianą ich wartości oraz `y` i `readonly` dostępny tylko do odczytu
 * posiada argument `z` konstruktora (który nie jest polem)
 * posiada prywatne pole `secret`
 * definiuje drugi konstruktor z innym ostatnim argumentem.

Podstawowe specyfikatory dostępu w Scali to: `public` (domyślny), `protected` i `private`. Dodatkowo w przypadku specyfikatorów`protected` oraz `private` możliwe jest podanie dodatkowej informacji, w jakim zasięgu (np. do którego pakietu) ograniczony jest dostęp do danej składowej (`protected[x]`/`private[x]`).


### Blok kodu
Blok kodu to kilka wyrażeń otoczonych nawiasami klamrowymi `{}`. Wartością bloku jest wartość ostatniego wyrażenia (nie ma potrzeby używania słowa kluczowego `return`). 

<iframe height="550px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=OeKNP2A/1&layout=v70"></iframe>
OeKNP2A/0
    
#### Ćwiczenie
Uzupełnij poniższy kod tak, by asercje były prawidłowe.
*Uwaga 1*: `???` może zastąpić dowolne wyrażenie w kodzie Scali - pozwala na skompilowanie kodu, ale w momencie wykonania rzuca wyjątek. W tym ćwiczeniu należy zastąpić `???` odpowiednimi wartościami. 
*Uwaga 2*: Metoda `assertEq` to zdefiniowana przez nas metoda przyjmująca dwa argumenty tego samego typu, sprawdzająca, czy są one takie same i wyświetlająca odpowiednią informację.
*Uwaga 3*: `ArrayBuffer` to jedna z kolekcji dostępnych w Scali, o których będziemy mówić później. Na potrzeby tego ćwiczenia można postrzegać go jako mutowalną kolekcję, do której są dodawane kolejne elementy w trakcie działania kodu za pomocą operatora `+=`. Aby porównać wartości tych tablic wpisz w miejsce `???` kolejne wartości, np.: `ArrayBuffer(1,2,3,4,5)`
    
<iframe height="1300px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=NjVtOZD/5&layout=v80"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Zwróć uwagę na to, które kawałki kodu wykonają się od razu po utworzeniu instacji klasy.</span><

*Podpowiedź 2:* ><span style="color:white;background:white">Różne fragmenty kodu mogą wykonywać się tylko raz lub przy każdym odwołaniu.</span><

Rozwiązanie: [https://scalafiddle.io/sf/2aUnjvX/0](https://scalafiddle.io/sf/2aUnjvX/0)

### Hierarchia typów w Scali

![](http://docs.scala-lang.org/resources/images/tour/unified-types-diagram.svg)
*Hierarchia typów w Scali - obrazek z [oficjalnej dokumentacji](http://docs.scala-lang.org/tour/unified-types.html)*

 * `Any` - nadtyp wszystkich typów
 * `AnyVal` - nadtyp "typów prymitywnych", nie-nullowalnych
 * `AnyRef` - nadtyp typów referencyjnych, odpowiednik `java.lang.Object`
   **Uwaga**: rzutowanie do `AnyRef` zawsze się powiedzie.
 * `Nothing` - podtyp wszystkich typów, który nie ma żadnej wartości
 * `Null` - podtyp wszystkich typów referencyjnych (rozszerzających `AnyRef`), który ma tylko jedną wartość - `null`

Wszystkie standardowo definiowane przez programistę klasy znajdą się w hierarchii typów pod `AnyRef`. Typ `Unit` ma tylko jedną wartość `()` - jest przydatny do definiowania metod, które nie mają zwracać żadnej wartości (analogicznie do `void` w Javie). W metodzie zwracającej `Unit` nie trzeba dopisywać `()` na końcu, kompilator robi to automatycznie. 

### Kontrola przepływu
**Uwaga**: większość konstrukcji Scali to wyrażenia, które zwracają konkretną wartość, a nie tylko są imperatywną częścią języka.

#### Pętle `while`/`do-while`

<iframe height="400px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=4oDAnCT/1&layout=v70"></iframe>
      
W przypadku pętli zaprezentowanych powyżej typem wyrażenia `while` czy `do-while` jest `Unit`.
      
```scala
var x = 0

val t: Unit = ()
val u: Unit = while (x < 5) { x += 1 }
```
      
#### Wyrażenia `if`/`else`
Podobnie jak w powyższym przypadku, Scalowy `if`/`else` może być traktowany jako konstrukcja czysto imperatywna. Jednak taka struktura jest również wyrażeniem, którego wartością będzie wartość ostatniego wyrażenia w tym bloku, który faktycznie się wykona (można tu zauważyć analogię do Javowego operatora `b ? x : y`).

<iframe height="500px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=uxQZHqc/0&layout=v80"></iframe>
      
Jeśli dany `if` nie jest zakończony konstrukcją `else`, to jest ona uzupełniana przez kompilator jako `else ()`. Typ całego wyrażenia `if` jest inferowany jako najbardziej wyspecjalizowany typ wspólny dla wartości, jakie mogą zostać zwrócone, zatem w takim przypadku będzie to typ `Any` lub `AnyVal` (lub `Unit`).
      
```scala
val a = 5

val any: Any = if (a < 5) "asd" //else ()
val anyVal: AnyVal = if (a < 5) 0 //else ()
```
      
#### "Pętle"`for`
W Scali nie ma standardowej pętli `for`. Istnieje natomiast bardziej ogólna konstrukcja zwana _for comprehension_, która m&#46;in. może spełniać podobną funkcję jak Javowa pętla _foreach_. Mechanizm ten ma jednak dużo większe możliwości, o których powiemy później.

<iframe height="500px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=mhYPyDD/0&layout=v40"></iframe>

### Słowo kluczowe `object`
Słowo kluczowe `object` pozwala w Scali na tworzenie singletonów - jedynych instancji ich własnych klas, do których można odwoływać się po nazwie.

<iframe height="400px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=wy4mJaW/1&layout=v80"></iframe>
    
Szczególnym przypadkiem jest `object` o nazwie takiej samej jak klasa. Mówimy wówczas o nich _companion object_ i _companion class_. Mają one wzajemnie dostęp do swoich składowych prywatnych. _Companion object_ można traktować jako analogię do statycznych składowych klas w Javie, a zatem wykorzystywać do definiowania metod pomocniczych (w tym tzw. metod wytwórczych - ang. _factory method_) czy stałych nie związanych z konkretnymi instancjami klasy.
    
```scala
class Foo(a: Int, b: Int) {
  import Foo._
  private val c: Int = someComplexMathLogic(a)
  private val d: Int = someComplexMathLogic(b)
}

object Foo {
  private def someComplexMathLogic(x: Int): Int = ???

  def apply(a: Int): Foo = new Foo(a, a)
}
```
    
### `Predef`
Podobnie jak w Javie domyślnie importowane jest wszystko z pakietu `java.lang` (`import java.lang.*;`), w Scali domyślnie zaimportowana jest zawartość obiektu `scala.Predef` (`import scala.Predef._`). Tylko dzięki temu zapewnione są niektóre w zasadzie podstawowe możliwości języka (np. operatory, "globalne" metody typu `println` czy bezpieczne konwersje między typami liczbowymi).

### Interpolacja i multi-line stringi

Scala domyślnie wspiera trzy rodzaje interpolacji stringów:
 * `s` - pozwala na wstawianie wartości zmiennych i wyrażeń za pomocą `$` i `${}`
 * `f` - pozwala na tworzenie stringów w sposób częściowo typowany, podobny do `printf` znanego z innych języków
 * `raw` - interpolator, który nie wymaga escapowania znaków szczególnych

Mechanizm ten jest rozszerzalny - możliwe jest stworzenie własnej interpolacji.

Dodatkowo Scala pozwala na proste i czytelne tworzenie stringów wielolinijkowych.

<iframe height="550px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=iVWX37c/1&layout=v60"></iframe>

### Tuple

Scala oferuje tzw. tuple, czyli pewnego rodzaju rekordy, "pojemniki" na kilka wartości potencjalnie różnych typów. Są to klasy typu `TupleN[T1, T2, ..., TN]`, gdzie `N` oznacza ilość wartości, które chcemy wpakować do jednego pudełka (od 1 do 22), a `T1`, `T2`, ..., `TN` to typy tych kolejnych wartości. Taki typ można również zapisywać jako `(T1, T2, ..., TN)`, co jest częściej spotykane. Istnieje kilka różnych sposobów tworzenia tupli, z których jeden jest najczęściej używany (patrz niżej). Do poszczególnych wartości możemy się odwoływać za pomocą takich metod jak `._N`, gdzie `N` to numer pola, które nas interesuje, _uwaga_, **indeksowany od 1**.

<iframe height="650px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=x7Eqf7u/0&layout=v60"></iframe>

### Funkcje

Scala jako język funkcyjny wspiera istnienie funkcji jako „obywateli pierwszej kategorii” (ang. first class objects), co oznacza, że funkcję można przypisać do zmiennej, przekazać jako argument itd. 

Typy funkcyjne zdefiniowane są jako `FunctionN[T1, T2, ..., TN, R]`, gdzie `N` oznacza ilość argumentów przyjmowanych przez funkcję (od 0 do 22), `T1`, `T2`, ..., `TN` to typy kolejnych argumentów, a `R` to typ zwracany. Inaczej taki typ można zapisać jako `(T1, T2, ..., TN) => R`.

Funkcje bezargumentowe definiowane są jako `() => { //ciało funkcji }`, w szczególności w przypadku ciała składającego się z pojedynczego wyrażenia, nawiasy klamrowe można pominąć.

Scala daje sporo składniowych możliwości definiowania funkcji. Pierwszym z nich jest:
```scala
val fun = (x: Int) => x.toString
```

W przypadku, gdy typ funkcji jest _explicite_ określony, nie ma potrzeby określania typu argumentu bezpośrednio przy nim.

```scala
val fun: Int => String = x => x.toString
```

W przypadku tego typu prostych funkcji (jednym z wymagań jest jednokrotne użycie argumentu) można również posłużyć się skróconą składnią.

```scala
val fun: Int => String = _.toString
```

Korzystając ze składni z `_`, również można bezpośrednio podać typ argumentu.

```scala
val fun = (_: Int).toString
```

Analogicznie tworzone są funkcje o większej ilości argumentów (w składni z `_` istotna jest kolejność parametrów):

```scala
val fun = (s: String, i: Int) => s.toUpperCase * i
val fun2: (String, Int) => String = (s, i) => s.toUpperCase * i
val fun3: (String, Int) => String = _.toUpperCase * _
val fun4 = (_: String).toUpperCase * (_: Int)
```

Funkcje mogą być wywoływane bezpośrednio poprzez `fun(args)`. Jest to równoznaczne z wywołaniem metody `apply` tj. `fun.apply(args)`.

Symbol `=>` jest prawostronnie łączny, a zatem np. `A => B => C` oznacza funkcję, która otrzymuje argument typu `A` i zwraca funkcję z `B` w `C`, czyli `(A => (B => C))`.

### Rekurencja ogonowa i metody wewnętrzne

![Alt Text](https://i.imgur.com/PBLYIc2.gif)

Rekurencja występuje, gdy metoda `foo` jako część swojej logiki woła samą siebie. Każde nowe wywołanie metody `foo` powoduje odłożenie odpowiednich informacji na stosie wywołań. Ponieważ pamięć komputera jest skończona, to możemy odłożyć jedynie ograniczoną liczbę wywołań `foo` na stos. 
 
Istnieje jednak pewien szczególny typ funkcji rekursywnych zwanych ogonowymi. Charakteryzuje się on tym, że do obliczenia wyniku funkcji nie potrzebujemy wartości zwróconej przez następne wywołania rekurencyjne. W takim przypadku kompilator może zoptymalizować rekurencję do prostej pętli.
 
Język Java nie wspiera takiej optymalizacji, więc pisanie funkcji rekurencyjnych w czystej Javie zawsze niesie za sobą ryzyko przepełnienia stosu. Ponieważ Scala jest językiem starającym się wspierać zarówno programowanie funkcyjne jak i obiektowe, to jej kompilator zawiera zaimplementowaną optymalizację ogonowej rekurencji.
 
Dodatkowo istnieje adnotacja `@scala.annotation.tailrec` - gdy jakaś funkcja posiada takową adnotację, to kompilator wygeneruje błąd, jeżeli nie uda mu się zastąpić rekurencyjnego wywołania pętlą. Dzięki temu mamy 100% pewność, że w czasie działania programu stos nie zostanie przepełniony przez wywoływanie danej funkcji.
 
Istnieje jednak ograniczenie tego mechanizmu w obecnej wersji Scali - optymalizowana jest jedynie rekurencja ogonowa w obrębie jednej funkcji. W przykładzie funkcje `ping` oraz `pong` można wyrazić za pomocą pętli `while`, niestety obecnie kompilator nie jest na tyle mądry, żeby wykryć rekurencję ogonową w takim przypadku.
 
Ponieważ pod spodem `ScalaFiddle` korzysta ze Scala.JS zachowanie kodu w poniższym fiddle zależy od przeglądarki. Gdy poniższy kod zostanie uruchomiony na JVMce będzie widać, że w przypadku funkcji które nie są rekurencyjnie ogonowe głębokość stosu będzie się zwiększała z każdym wywołaniem. Silnik JavaScripti w Firefoxie zachowuje się podobnie jak JVMka ale już z naszych obserwacji wynika, że V8 jest na tyle sprytny, żę również optymalizuje wywołania `factorial` do pętli.
 
<iframe height="1000px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=s7F1uiI/0&layout=v70"></iframe>

Scala pozwala również na zagnieżdżanie funkcji w innych funkcjach albo metodach.
 
```scala
def max(a:Int, b:Int, c:Int) = {
  def max(x:Int, y:Int) =
    if (x > y) x else y
    
  max(a, max(b, c))
}
```

Funkcja wewnętrzna ma dostęp do wszystkich argumentów przekazanych do jej rodzica, a także do zmiennych lokalnych zdefiniowanych do miejsca jej definicji, więc nie ma potrzeby przekazywania ich ponownie. 

Funkcje wewnętrzne często wykorzystuje się w sytuacji, gdy chcemy wyekstrahować jakiś kawałek logiki w ograniczonym zakresie. W Javie w takich przypadkach musimy stworzyć prywatną metodę, do której dostęp mają wszystkie inne metody w danej klasie. Kończy się to często tym, że klasa ma wiele prywatnych pomocniczych metod używanych tylko przez jedną inną metodę. Użycie w takim przypadku funkcji wewnętrznej zwiększa czytelność kodu ponieważ od razu wiemy, gdzie będzie używana dana funkcja (ograniczamy jej widoczność).

#### Ćwiczenie 1

Uzupełnij implementację metod `compose`, `uncurry` i `curry` tak, by wykonywały one operacje odpowiednio [złożenia](https://pl.wikipedia.org/wiki/Z%C5%82o%C5%BCenie_funkcji), [zwijania i rozwijania](https://pl.wikipedia.org/wiki/Currying) funkcji.

<iframe height="550px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=6Q4eT2k/4&layout=v75"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Zacznij od stworzenia pustej (bez implementacji działania) funkcji, która będzie pasowała do oczekiwanego typu zwracanego. Zastosuj dowolną składnię z rozdziału o funkcjach np. (i: Int) => ???</span><

*Podpowiedź 2:* ><span style="color:white;background:white">Pamiętaj, że zapis Int => Double => String można postrzegać jako Int => (Double => String), czyli jako funkcję przyjmującą jeden argument typu Int i zwracającą nową funkcję.</span><

*Podpowiedź 3:* ><span style="color:white;background:white">Jeśli masz już pustą funkcję, teraz spróbuj wywołać funkcję otrzymaną jako argument. Przekaż do niej argumenty, które otrzymała nowa funkcja.</span><

*Podpowiedź 4:* ><span style="color:white;background:white">Początki implementacji mogą wyglądać np. następująco: i => dla compose; (i, d) => dla uncurry; i => d => dla curry.</span><

Rozwiązanie: [https://scalafiddle.io/sf/qrBIXAn/0](https://scalafiddle.io/sf/qrBIXAn/0)

#### Ćwiczenie 2

Napisz funkcję rekurencyjną, która oblicza element z danego wiersza oraz danej kolumny w [trójkącie Pascala](https://en.wikipedia.org/wiki/Pascal%27s_triangle). Funkcja nie musi korzystać z rekursji ogonowej.

![Pascal1](https://upload.wikimedia.org/wikipedia/commons/thumb/0/0d/PascalTriangleAnimated2.gif/220px-PascalTriangleAnimated2.gif)

*Żródło: https://en.wikipedia.org/wiki/Pascal%27s_triangle*

![Pascal2](https://www.mathsisfun.com/images/pascals-triangle-1.gif)

*Żródło: https://www.mathsisfun.com/pascals-triangle.html*

![Pascal3](https://www.mathsisfun.com/numbers/images/pascals-triangle-types.svg)

*Żródło: https://www.mathsisfun.com/pascals-triangle.html*


<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=Fj7nJx9/1&layout=v70"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Element (r,c) w trójkącie Pascala jest równy sumie elementów (r-1,c-1) i (r-1,c).</span><

*Podpowiedź 2:* ><span style="color:white;background:white">Pamiętaj o zdefiniowaniu warunków brzegowych, aby zakończyć rekurencję. Zauważ, że wartości na krawędziach trójkąta są równe 1.</span><
 
Rozwiązanie: [https://scalafiddle.io/sf/aNnsrkg/0](https://scalafiddle.io/sf/aNnsrkg/0)

#### Ćwiczenie 3

Napisz funkcję, która wylicza n-ty wyraz [ciągu Fibonacciego](https://en.wikipedia.org/wiki/Fibonacci_number) za pomocą rekursji ogonowej.

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=z5cfMUY/1&layout=v70"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Standardową metodą obliczania zadanego wyrazu ciągu Fibonacciego jest pętla, która za pomocą dwóch zmiennych zapamiętuje dwa poprzednie wyrazy oraz co iterację oblicza następny, zapisując go w jednej ze zmiennych.</span><

*Podpowiedź 2:* ><span style="color:white;background:white">Spróbuj wyrazić pętlę opisaną wyżej za pomocą funkcji wewnętrznej, która zamiast zapisywać wyniki do zmiennych, będzie przekazywać wartości do kolejnego wywołania rekurencyjnego i zakończy działanie, gdy wartość iteratora będzie równa n. Wyraz pierwszy i drugi możesz obsłużyć warunkiem w funkcji głównej i zwrócić 1.</span><

*Podpowiedź 3:* ><span style="color:white;background:white">Sygnatura funkcji wewnętrznej może wyglądać tak: def inFib(it: Int, a: Int, b: Int): Int = ???. Spróbuj ją zaimplementować oraz wywowałać.</span><

Rozwiązanie: [https://scalafiddle.io/sf/wy9ay51/1](https://scalafiddle.io/sf/wy9ay51/1)

### Materiały

* [Książka: Scala By Example](https://www.scala-lang.org/docu/files/ScalaByExample.pdf) - Martin Odersky, 2014
* [Oficjalna dokumentacja - wprowadzenie do Scali](http://docs.scala-lang.org/tour/tour-of-scala.html)
* [Post o modyfikatorach dostępu](http://www.jesperdj.com/2016/01/08/scala-access-modifiers-and-qualifiers-in-detail/)
* [Oficjalna dokumentacja dotycząca interpolacji stringów](https://docs.scala-lang.org/overviews/core/string-interpolation.html)
* [Scala Exercises - Scala tutorial](https://www.scala-exercises.org/scala_tutorial/terms_and_types)
