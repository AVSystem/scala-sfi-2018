##  Najważniejsze różnice pomiędzy Scalą i Javą
   
W najprostszej wersji Scala zarówno składniowo jak i w zachowaniu bardzo przypomina Javę, jednak im jednak dalej w las, tym więcej jest różnic.

Część rzeczy wprowadzonych w Scali jest z kategorii _quality-of-life_ (case class, nazwane parametry), a inne, takie jak implicity, dają programiście dodatkowe (w porównaniu np. z Javą) możliwości podczas pisania kodu. 
 
### Domyślne wartości parametrów i argumenty podawane po nazwie

Feature, który bardzo uprzyjemnia pisanie kodu wszelakiego. Wołając metodę/funkcję/konstruktor, możemy wywołać go nie tylko jak w Javie, po prostu podając po przecinku argumenty. Przekazując argument, możemy odwołać się do jego nazwy w sygnaturze, dodatkowo każdy argument może posiadać wartość domyślną - w takim przypadku możemy w ogóle nie podawać jego wartości.

<iframe height="800px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=kiFywCw/3&layout=v70"></iframe>


### Traity

Java posiada klasy, klasy abstrakcyjne oraz interfejsy. W przypadku Scali mamy klasy, case classy, klasy abstrakcyjne oraz traity.
 
Najważniejsze cechy traitów:
 * mogą służyć jako definicja interfejsu, czyli metody bez implementacji (tak jak w Javie).
 * mogą definiować zachowanie - metoda + domyślna implementacja (dostępne także w interfejsach od Javy 8 przy użyciu słówka kluczowego `default`).
 * mogą posiadać stan (obecnie niemożliwe w Javie).
 * mogą dziedziczyć po klasach oraz innych traitach.
 * w odróżnieniu od klas nie możemy przekazać parametrów konstruktora (ale same traity mogą mieć kawałek logiki wykonywany podczas ich inicjalizacji).
 * w odróżnieniu od dziedziczenia klas można dziedziczyć z wielu traitów.
 
<iframe height="800px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=0SOQYaC/0&layout=v70"></iframe>
 
Dziedzicząc z traita, używamy słówka kluczowego `extends` albo `with` (gdy jest to kolejny trait lub nasza klasa dziedziczy po innej klasie).
 
Traity można traktować jak specyfikację zachowania (interfejsy Javowe) lub reużywalne kawałki logiki, które możemy wplatać we własne klasy (dzięki możliwości definiowana nie tylko zachowań ale również stanu). 
 
Ponieważ możemy dziedziczyć z wielu traitów, każdy z nich może definiować zachowanie, implementację oraz kod inicjalizacyjny, zarówno język jak i programista muszą radzić sobie z problemami takimi jak:
 *  "diamond problem" - jak żyć gdy w hierarchii dziedziczenia wiele razy pojawia się ten sam trait?
 *  w jakiej kolejności ma być wykonany kod inicjalizacyjny?
 *  co zrobić, gdy w hierarchii dziedziczenia pojawiają się 2 implementacje tej samej metody?
 
Część z tych problemów rozwiązana jest przez tzw. linearyzację traitów - algorytm, który w deterministyczny sposób układa klasy oraz traity w kolejności. W sytuacji, gdy mamy 2 implementacje tej samej metody, programista sam musi zadecydować, która z nich ma być użyta.
 
Nie będziemy zajmowali się różnymi przypadkami szczególnymi podczas tego warsztatu, osoby głodne wiedzy spokojnie znajdą dużo informacji w [internecie](https://github.com/ghik/opinionated-scala/wiki/Traits#multiple-class-inheritance) lub odpowiedniej literaturze.
 
Sposób wyrażenia traitów oraz ich dziedziczenia w bajtkodzie jest szczegółem implementacyjnym i może się zmienić z jednej majorowej wersji Scali na inną. Przykładowo Scala od wersji `2.12` wspiera tylko Javę 8, więc traity bez stanu ale z implementacją metod mogą być bez problemu wyrażane przez standardowy Javowy interfejs z metodami `default`owymi. W starszych wersjach Scali kompilator musiał generować statyczne metody i forwardery do nich w konkretnych klasach tak, aby emulować implementację metod w interfejsach.
 
### Case classy

Element języka chyba najczęściej wykorzystywany w dyskusjach "Patrz, jaka ta Scala jest zwięzła w porównaniu z Javą."
 
Na sam początek przykład klasy Javowej i jej odpowiednika case classowego.

```java
import java.util.Objects;

public final class Person {
    private final String name;
    private final String surname;

    public Person(String name, String surname) {
        this.name = name;
        this.surname = surname;
    }

    public String getName() {
        return name;
    }

    public String getSurname() {
        return surname;
    }

    public Person withName(String newName) {
        return new Person(newName, surname);
    }

    public Person withSurname(String newSurname) {
        return new Person(name, newSurname);
    }

    @Override
    public boolean equals(Object other) {
        if(other instanceof Person) {
            Person otherPerson = (Person)other;
            return Objects.equals(name, otherPerson.name) &&
                   Objects.equals(surname, otherPerson.surname);
        } else {
            return false;
        }
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, surname);
    }

    @Override
    public String toString() {
        return "Person(" + name + ", " + surname + ")";
    }
}
 ```

```Scala
case class Person(name: String, surname: String)
```

Najważniejsze konsekwencje dodania słówka kluczowego `case` to:
 *  automatyczna generacja companion objectu z metodą `apply`. Dzięki temu możemy tworzyć instancję case classy bez użycia słówka kluczowego `new`.
 *  automatyczna generacja metody `uanpply` w companion objecie. Pozwala ona dostać się do poszczególnych elementów case classy podczas pattern matchingu i umożliwia stosowanie zagnieżdżonych wzorców.
 *  każde zadeklarowane pole jest dostępne publicznie.
 *  klasa posiada sensowną implementację `equals`, `hashCode`, `toString`.
 *  klasa posiada automatycznie wygenerowaną metodę `copy`, która bardzo ułatwia tworzenie nowej instancji case classy ze zmienionymi wartościami pewnego podzbioru pól.

<iframe height="500px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=RBf8Shk/1&layout=v60"></iframe>

Użycie case class pozwala nam na uniknięcie dużej ilości "boilerplate'u" (metody wygenerowane automatycznie) oraz bardzo ułatwia pisanie kodu w bardziej funkcyjny sposób (zachęcanie do tworzenie immutable rekordów, wsparcie dla pattern matchingu).

### Pattern matching i partial funkcje

Pattern matching to taki switch na sterydach.
 
W Javie istnieje konstrukcja `switch/case`, jednak wspiera ona jedynie kilka typów prymitywnych, kilka wyróżnionych wrapperów (`Character`, `Byte`, `Short`, `Integer`), Stringi oraz Enumy. Na pierwszy rzut oka `switch` w Scali może nie różnić się niczym od tego Javowego, szybko się jednak okazuje, że pozwala on na zdecydowanie więcej niż jego Javowy odpowiednik. 
 
Pierwsza różnica, która się może rzucić w oczy, to brak słówka kluczowego `break`. W przeciwieństwie do swojego Javowego odpowiednika Scalowy "switch" nie wykonuje logiki wszystkich `case`ów, które pasują do danej instancji, szuka on pierwszego od góry i tylko ta logika jest wykonywana. Po każdym case można dopisać warunki, jakie muszą zostać spełnione, aby zaakceptować dany przypadek. 
 
Jak widać w przykładzie, pattern matching pozwala nam nie tylko pisać odpowiednią logikę per wzorzec, ale również dekonstruować elementy składowe obiektów, nadawać im nazwy oraz zagnieżdżać wzorce. 
 
Sam pattern matching wywodzi się z języków funkcyjnych, gdzie struktury danych i funkcje na nich operujące często nie są połączone, w  przeciwieństwie do OOP, gdzie obiekt posiada tożsamość, zachowanie i stan.
 
<iframe height="1000px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=aFSXM3V/1&layout=v81"></iframe>
 
Jeżeli nasz kod nie rozważył wszystkich wzorców i przekażemy do niego nieuwzględniony element, w czasie wykonania programu zostanie rzucony `MatchError`. Zwykle oznacza on błąd programisty, podobnie jak `NullPointerException`. Tak jak widać w przykładzie, możemy również stworzyć blok kodu, który zawiera pattern matching tylko dla podzbioru możliwych wartości. Taka funkcja ma w czasie działania typ [PartialFunction](https://www.scala-lang.org/api/current/scala/PartialFunction.html).

Interfejs `PartialFunction` daje możliwość sprawdzenia (za pomocą metody `isDefinedAt`), czy dany element na wejściu dopasuje się do jakiegokolwiek wzorca.
 
<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=vB2h7hy/1&layout=v70"></iframe>

Warto zauważyć, że istnienie wzorca, który obsłuży wszystkie możliwe przypadki, powoduje, że dany pattern matching domyślnie nie jest już typu *PartialFunction*. Najbardziej ogólnym wzorcem jest `case _ => ???`.

#### Ćwiczenie

Zaimplementuj `PartialFunction`, który przyjmuje tuplę z dwoma `Int`ami i jeśli pierwsza jest podzielna przez drugą, to zwraca wynik dzielenia. Funkcja nie powinna być zdefiniowana dla liczb niepodzielnych. 
 
<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=yI6DoPV/1&layout=v70"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Wywołanie _.isDefinedAt(???) zwraca false, jeśli do podanego argumentu nie pasuje żaden wzorzec.</span><

*Podpowiedź 2:* ><span style="color:white;background:white">Do wyciągnęcia wrtości z tupli można zastosować składnię case (a, b). Sprawdzenie podzielności liczb można wówczas napisać jako case (a, b) if *sprawdzenie podzielności* => *wykonanie dzielenia*</span><

Rozwiązanie: [https://scalafiddle.io/sf/Bgt6hHq/0](https://scalafiddle.io/sf/Bgt6hHq/0)

### Dostęp do elementów kolekcji

W Javie dostęp do elementów kolekcji zależy od niej samej. Inaczej wygląda to w przypadku tablic, list lub map. 
 
Scala stara się zunifikować wszystkie te przypadki. Do elementów danej kolekcji można dobrać się przy pomocy `()` czyli metody `apply` lub "prawdziwych" metod, np `get` w mapie.

<iframe height="400px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=iFTAH1M/0&layout=v60"></iframe>

Tak jak było wspomniane wcześniej, odwołanie `()` jest przez kompilator Scali zamieniane na wywołanie metody `apply` na danym obiekcie. 

### Różnica pomiędzy == i eq

W Scali `==` porównuje wartości (czyli działa jak wywołanie `equals`). Jeżeli chcemy otrzymać zachowanie Javowego `==`, należy użyć "magicznej" metody `eq`.

Tak jak i w Javie nic nie stoi na przeszkodzie porównywania dwóch zupełnie niepowiązanych typów, np. String oraz Int (choć kompilator, jeśli tylko może, to ostrzeże nas o takiej sytuacji).

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=UOr7PHl/3&layout=v70"></iframe>
 
### Typy generyczne

Pierwsza różnica w deklarowaniu klas oraz metod generycznych pomiędzy Scalą a Javą to użycie `[]` zamiast `<>`. Druga różnica występuje w kontekście umieszczenia informacji o typach generycznych w przypadku metod.

```scala
class GenericCollection[A] {  
  private val buffer: ArrayBuffer[A] = new ArrayBuffer[A]()  

  def add(elem: A): Unit = {  
    buffer += elem  
  }  
}  

def vectorOfTwo[T](first: T, second: T): Vector[T] = Vector(first, second)  
```
    
```java
class GenericCollection<A> {  
    private final ArrayList<A> arrayList = new ArrayList<>();  

    public void add(A elem) {  
        arrayList.add(elem);  
    }  
}

public static <A> List<A> listOfTwo(A first, A second) {  
    return Arrays.asList(first, second);  
}

```

Następne zdanie zawiera lokowanie subiektywnej opinii: składnia Scali w kontekście generyków jest bardziej spójna.

W ekosystemie Scalowym programowanie generyczne jest również bardziej rozwinięte niż w Javie dzięki użyciu możliwości języka takich jak:
 
 * kowariancja/kontrawariancja dla dowolnych typów
 * implicity
 * [type membery](https://github.com/ghik/opinionated-scala/wiki/Generics-and-type-members)
 * [higher kinded types](https://typelevel.org/blog/2016/08/21/hkts-moving-forward.html) - czyli jak napisać funkcję dla dowolnego typu generycznego, który dodatkowo sam w sobie jest ugeneryczniony
 
Użycie tego typu programowania pozwala uniknąć pisania dużej ilości boilerplate'u, z drugiej strony wymaga pewnej wprawy od programistów w czytaniu sygnatur typu `def tupleTC[F[_], A, B](fa: F[A], fb: F[B])(implicit f: Bind[F]): F[(A, B)]` - to oczywiście z czasem nie jest problematyczne, ale próg wejścia dla zespołu może być wysoki.
 
Dla osób zainteresowanych tematem warto sięgnąć po biblioteki typu [Shapeless](https://github.com/milessabin/shapeless) oraz [Cats](https://github.com/typelevel/cats) - implementują one już dużo wzorców oraz konstrukcji generycznych, starających się minimalizować ilość powtarzanego kodu. 
         
#### Ćwiczenie

Napisz funkcję `isSorted`, która przyjmie tablicę dowolnego typu `A`, oraz funkcję `(A, A) => Boolean` i zwróci wartość `Boolean`, która powie, czy dana tablica jest posortowana względem podanej funkcji porównującej.
 
<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=zq2IMff/0&layout=v60"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Metoda isSorted działająca tylko dla tablic Intów wyglada tak: def isSorted(arr: Array[Int], lt: (Int, Int) => Boolean): Boolean = ???. Spróbuj przerobić ją na wersję generyczną, tak aby zamiast Intów mógł pojawić się też inny typ. </span><

*Podpowiedź 2:* ><span style="color:white;background:white">Najprostszym, chociaż niezbyt wydajnym, sposobem sprawdzenia czy tablica jest posortowana jest porównanie jej z tablicą po posortowaniu. W Scali możesz posortować tablicę za pomocą metody _.sortWith(lt: (A, A) => Boolean).</span><

*Podpowiedź 3:* ><span style="color:white;background:white">Czy jesteś w stanie w czasie liniowym sprawdzić, czy tablica jest posorotwana? Czy można logikę wyrazić za pomocą rekurencji ogonowej?</span><
 
Rozwiązanie: [https://scalafiddle.io/sf/S1SbsK1/0](https://scalafiddle.io/sf/S1SbsK1/0)
 
#### Kowariancja/kontrawariancja

Formalnie rzecz biorąc, kowariancja oraz kontrawariancja to relacja pomiędzy typami. Przyjmując oznaczenie `<:`, jeżeli typ `A` jest podtypem typu `B`, to możemy zapisać to `A <: B`. Mając teraz `A` oraz `B`, jeżeli `T[A] <: T[B]` (czyli `T[A] jest podtypem T[B]`) to mówimy, że `T` jest typem kowariantnym. Jeżeli natomiast `T[B] <: T[A]` to `T` jest typem kontrawariantnym.
 
Kowariancja/Kontrawariancja łączy się również z [Liskov Substitution Principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle), ustalenie relacji pozwala systemowi typów powiedzieć:

 * czy `T[A]` jest podtypem `T[B]` - kowariancja.
 * czy `T[B]` jest podtyptem `T[A]` - kontrawariancja.
 * czy pomiędzy `T[A]` i `T[B]` nie ma relacji subtypingu - inwariancja.
 
Domyślnie typy generyczne w Javie oraz Scali są inwariantne, `T[A]` nie jest podtypem `T[B]`, jak również `T[B]` nie jest podtypem `T[A]`.
 
<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=P03XgDh/0&layout=v70"></iframe>
 
W Javie kowariancja występuje w dwóch smakach.
 
Pierwszy z nich to tablice, które są kowariantne, oznacza to, że `Integer[]` jest podtypem `Number[]`. Tablice w Javie są mutowale, what can go wrong?
 
```Java
public static void replace(Number[] numbers, int index) {  
    numbers[index] = Double.valueOf("1.0");  
}
 
Integer[] ints = new Integer[]{1, 2};  
replace(ints, 1);  
for(Integer i: ints) {  
    System.out.println(i);  
}
```
 
W czasie wykonania powyższego kodu Javowego rzucony zostanie `java.lang.ArrayStoreException` w funkcji `replace`, ponieważ do tablicy `Integer`ów wrzucamy `Double`, a ponieważ tablice są obsługiwane przez JVMkę w specjalny sposób, to w czasie działania wiedzą dokładnie, jaki typ tak na prawdę przechowują. Przykład tablic w Javie pokazuje, że jeżeli nasz typ `F` jest mutowalny, to nie powinien być kowariantny ponieważ umożliwia to nam napisanie niepoprawnego kodu, który się skompiluje i dopiero w trakcie wykonania dostaniemy informację, że gdzieś popełniliśmy błąd.
 
Ponieważ typy generyczne w Javie są inwariantne to nie ma takiego problemu.
 
 ```Java
 public static void replace(List<Number> numbers, int index) {  
     numbers.set(index, Double.valueOf("1.0"));  
 }  
 
 // This method accepts List of any type T that extends Number, 
 // we declare List as covariant in this particular call site
 public static void replaceExtends(List<? extends Number> numbers, int index) {  
    //numbers.set(index, Double.valueOf("1.0"));  
    Number num = numbers.get(0);  
    System.out.println(num);  
 }  
 
 // This method accepts List of any type T that is super class of Number, 
 // we declare List as contravariant in this particular call site
 public static void replaceSuper(List<? super Number> numbers, int index) {  
     numbers.set(index, Double.valueOf("1.0"));  
     // Number num = numbers.get(0);  
     // System.out.println(num);
 }  
  
List<Integer> ints = Arrays.asList(1, 2); 
List<Object> objects = Arrays.asList(1, 2);

// we cannot call method that accepts List<Number> with List<Integer>
// because List is invariant
// replace(ints, 1)

// Integer extends Number and List is covariant in replaceExtends 
// so the call is legal
replaceExtends(ints, 1);  
// replaceSuper(ints, 1);  

// replace(objects, 1);
// replaceExtends(objects, 1);
replaceSuper(objects, 1);
 ```
 
W Javie typy takie generyczne takie jak `List` są inwariantne, możemy jednak zadeklarować w konkretnej metodzie/funkcji, że typ ma być kowariantny/kontrawariantny za pomocą `extends` lub `super` (tak zwane "call-site variance"). 
 
Kiedy pierwsze, a kiedy drugie ma sens? Osoby, które czytały [Effective Java](https://www.amazon.com/Effective-Java-3rd-Joshua-Bloch/dp/0134685997), mogą pamiętać pewien mnemonik - PECS (Producer Extends, Consumer Super). 
 
Jeżeli typ generyczny, który akceptujemy, tylko "produkuje" dla nas (w naszym przypadku pobieramy elementy z listy) deklarujemy typ jako kowariantny poprzez użycie `extends`. Jeżeli natomiast przekazujemy obiekty do naszego typu generycznego używamy `super` (deklarujemy typ jako kontrawariantny). Gdy w naszej funkcji zarówno "produkujemy" elementy generyczne jak "konsumujemy", pozostaje nam jedynie zostawić typ jako inwariantny.
 
Sam kompilator Javy upewni się, że nie naruszamy tych zasad, nie jesteśmy np. w stanie przekazać elementu do typu generycznego, który został zadeklarowany jako kowariantny (linijka `//numbers.set(index, Double.valueOf("1.0"));`).
 
Pierwsza różnica między Scalą a Javową jest taka, że tablice w Scali są inwariantne, kompilator Scali upewni się, że nie doprowadzimy do sytuacji, w której rzucony będzie `java.lang.ArrayStoreException`.
 
Druga zmiana jest taka, że Scala wspiera nie tylko call-site variance, ale również declaration-site variance. 
 
Przykładowo immutable lista w Scali zadeklarowana jest jako `List[+A]`. `+` przy typie generycznym oznacza, że `List` jest kowariantny, oznaczenie typu `-A` oznaczałoby natomiast, że typ `List` jest kontrawariantny. 
 
Innym przykładem jest `Function3[-T1, -T2, -T3, +R]`, zgodnie z zasadą PECS, typy, które są "konsumowane" (czyli są argumentami funkcji), oznaczone są jako kontrawariantne, typ zwracany, który jest "produkowany" przez funkcję, jest natomiast zadeklarowany jako kowariantny.
 
 ```Scala
 class B  
 class A extends B  
  
 class Foo  
 class Bar extends Foo  
 class Baz extends Foo

 var f: (Bar, A, Foo) => B = null  
 var g: (Foo, Any, Any) => A = null  
  
 f = g
 ```
 
Typy występujące w liście argumentów są kontrawariantne, możemy je więc zastąpić typami nadrzędnymi `Bar`, `A` oraz `Foo`, typ zwracany jest kowariantny, także w jego miejsce możemy wstawić podtyp `B`.
 
Jeżeli sygnatura naszej innej funkcji spełni te zasady, to jest ona podtyptem `(Bar, A, Foo) => B`. 

### Materiały

* [Oficjalna dokumentacja dotycząca case class](https://docs.scala-lang.org/tour/case-classes.html)
* [Oficjalna dokumentacja dotycząca Pattern Matchingu](https://docs.scala-lang.org/tour/pattern-matching.html)
* [Oficjalna dokumentacja dotycząca ekstraktorów](https://docs.scala-lang.org/tour/extractor-objects.html)
* [Informacja o data classach w Javie](http://cr.openjdk.java.net/~briangoetz/amber/datum.html)
* [Cake Pattern w Scali czyli stackowanie traitów w praktyce](https://www.cakesolutions.net/teamblogs/2011/12/19/cake-pattern-in-depth)
* [I dyskusja dlaczego (może) nie jest to najlepszy sposób budowania aplikacji](https://stackoverflow.com/questions/7860163/what-are-some-compelling-use-cases-for-dependent-method-types)
* [Higher Kinded Types](https://typelevel.org/blog/2016/08/21/hkts-moving-forward.html)
* [Type classy 1](https://blog.scalac.io/2017/04/19/typeclasses-in-scala.html)
* [Type classy 2](http://danielwestheide.com/blog/2013/02/06/the-neophytes-guide-to-scala-part-12-type-classes.html)
* [Java/Scala cheat sheet](http://rea.tech/java-to-scala-cheatsheet/)
