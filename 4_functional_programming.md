## Elementy zaawansowanego programowania funkcyjnego w Scali

Scala duży nacisk kładzie na wsparcie programowania funkcyjnego. W tej części skupimy się na elementach, które są istotne dla osób chcących korzystać ze Scali w ten sposób. 
 
### Sealed hierarchie

Sealed hierarchie przypominają enumy Javowe - jest to zamknięty zbiór klas, które implementują wspólny interfejs.
 
Zazwyczaj jako bazę takiej hierarchii przyjmuje się klasę abstrakcyjną albo traita. W bibliotece standardowej znajduje się wiele przykładów sealed hierarchii, m.in `Option` oraz `List`.
 
Sealed hierarchie (razem z case classami) pozwalają w Scali efektywnie korzystać z tzw. [Algebraicznych typów danych](https://en.wikipedia.org/wiki/Algebraic_data_type). Jest to sposób modelowania wykorzystywany w językach funkcyjnych, gdzie jakaś rzecz opisana jest przez kilka znanych z góry wariantów. 
 
Dla przykładu linked lista może być albo pustą listą (oznaczaną często jako typ `Nil`) lub kawałkiem listy z jakąś daną (np. intem) oraz wskazaniem na następny element (tzw. ogon listy). 

Podobnie możemy opisać drzewo binarne. Element drzewa może być pusty, może być liściem lub zwykłym węzłem, który posiada wskazanie na lewe i prawe dziecko.
 
Często struktury modelowane przez ADT są rekursywne (tak jak lista, drzewo), wynika z tego, że operacje na takich strukturach również w bardzo elegancki sposób można zaimplementować przez funkcje rekursywne. Tutaj przydaje się fakt, że Scala jest w stanie zoptymalizować rekursję ogonową do zwykłej pętli. W Javie najczęściej trzeba by pisać od razu iteracyjne wersje algorytmów, aby uniknąć przepełnienia stosu przy dużym rozmiarze struktury.
 
Wszystkie podklasy `sealed` trait/klasy muszą znajdować się w jednym pliku źródłowym. Dzięki temu kompilator ma pełną informację o wszystkich możliwych podtypach danego `sealed` typu i jest w stanie generować ostrzeżenia, gdy pattern matching nie rozważa wszystkich możliwości.
 
<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=QGuF2wn/1&layout=v70"></iframe>
 
<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=PVKiyg6/2&layout=v70"></iframe>

Ktoś może powiedzieć "hola, hola, czym to się różni od ordynarego `instanceOf` w Javie"? 
 
Jeżeli wykonujemy pattern match na elemencie o bardzo ogólnym typie takim jak `Any` i sprawdzamy, czy jest on stringiem, intem, URLem, to racja, otrzymujemy po prostu "przyjemniejszą" składnię na drabinki `instanceOf`. Trzeba się pilnować, aby nie nadużywać pattern matchingu w ten sposób i patrzeć na niego w taki sam sposób jak `instanceOf` w kodzie Javowym - element języka, który powinniśmy wykorzystać, gdy mamy do tego bardzo dobre powody. Przykładem sensownego użycia `instanceOf` jest kod bibliotek serializujących/deserializujących - tutaj pattern matching pozwala nam zapisać taką logikę w bardziej zwięzły i czytelny sposób.
 
Pattern matching jest jednak czymś zupełnie innym w przypadku sealed hierarchii oraz ADT - kompilator zapewni nas, że wszystkie przypadki zostały obsłużone poprawnie. 
 
<iframe height="500px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=sfymWoy/0&layout=v70"></iframe>
 
Logikę zawartą w przypadkach w pattern matchingu możemy również wyrażać przez odpowiednie metody w klasach, jest to po prostu alternatywny sposób modelowania danych oraz logiki. 
 
Jak zwykle, nie ma jedynego słusznego podejścia (OOP/FP/ETC) i wszystko zależy od kontekstu, wymagań, a nawet osób, które pracują z nami w zespole.

#### Ćwiczenie 1

Zaimplementuj funkcję rekurencyjną `isInTree`, która przyjmie referencję na `Tree` oraz wartość typu `Int` i odpowie na pytanie, czy dany element jest w drzewie. 

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=E69Xubc/2&layout=v70"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Powyżej zaimplementowana została metoda dodająca element do drzewa. Możesz się na niej wzorować, jeśli chodzi o poruszanie się po drzewie.</span><

Rozwiązanie: [https://scalafiddle.io/sf/UiTO6Vx/0](https://scalafiddle.io/sf/UiTO6Vx/0)

#### Ćwiczenie 2*

Zaimplementuj funkcję rekurencyjną `isBST` która przyjmie referencję na `Tree` i odpowie na pytanie, czy zadane drzewo binarne jest [drzewem przeszukiwań binarnych](https://en.wikipedia.org/wiki/Binary_search_tree).
Podpowiedź: użyj funkcji wewnętrznej.
 
<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=wmhBNb0/2&layout=v70"></iframe>

Rozwiązanie: [https://scalafiddle.io/sf/yjEwko2/0](https://scalafiddle.io/sf/yjEwko2/0)
 
### Wiele list parametrów 

Scala nie ogranicza liczby list parametrów, które może posiadać metoda albo funkcja. W najbardziej klasycznym przypadku mamy jedną listę ze wszystkimi parametrami, ale w Scali możemy również zadeklarować funkcję bez listy parametrów lub z wieloma listami.
 
Przyjęło się, że metoda, która nie posiada listy parametrów, nie wykonuje żadnych efektów ubocznych (najprościej mówiąc, zastąpienie wywołania metody jej wartością nie zmieniłoby semantyki programu).
 
```Scala
import scala.concurrent.duration._

def duration5Minutes: FiniteDuration = 5.minutes
def printCurrentTime(): Unit = println(System.currentTimeMillis)
 ```
Więcej niż jedna lista argumentów przydaje się w curryingu oraz przy bardziej skomplikowanych sygnaturach korzystających z typów generycznych. Informacja o typie generycznym wędruje od jednej listy argumentów do drugiej.
     
<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=rod9SSZ/0&layout=v70"></iframe>

### Implicit parametry

Pierwszym z zestawu mechanizmów *implicit* są *implicit parametry*. W definicji każdej metody można oznaczyć ostatnią listę parametrów słowem kluczowym *implicit*. Jeśli przy wywołaniu metody nie podamy wprost parametrów z tej listy, kompilator spróbuje odnaleźć wartości odpowiedniego typu i wstawić je w to miejsce. 

Jak kompilator wybiera wartości do podstawienia?
* Wartość, aby mogła być podstawiona, musi być oznaczona jako *implicit*. Może to być `val`, `var`, `lazy val`, `def` i `object`.
* Dostępne wartości szukane są w: lokalnym zakresie zmiennych, importowanych wartościach, *companion object*cie poszukiwanego typu... (lista uproszczona - więcej informacji można znaleźć m.<span></span>in. [tutaj](https://stackoverflow.com/questions/5598085/where-does-scala-look-for-implicits)).

*implicit def* może przyjmować *implicit parametry*, które wykorzysta do zwrócenia wartości.

Sztandarowym przykładem zastosowania *implicit parametrów* jest sposób przekazywania `ExecutionContext`u przy obliczeniach asynchronicznych opartych o `Future`.

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=lY6JSvL/2&layout=v90"></iframe>

### Implicit konwersje

Kolejnym elementem, w którym kompilator może nam pomóc automatycznie, są *implicit* konwersje pomiędzy typami. Konwersje reprezentowane są przez funkcje (oznaczone słowem kluczowym *implicit*) o typie `X => Y`, może to być też metoda przyjmująca argument typu `X` i zwracająca wartość typu `Y`. 

<iframe height="400px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=rBCNwkS/1&layout=v80"></iframe>

Dobrą praktyką jest unikanie konwersji z użyciem ogólnych typów (np. `String => Int`, `X => Int`), ponieważ może to powodować niespodziewane rezultaty oraz spowalnia kompilację.

### Rozszerzanie interfejsu istniejących klas

Bardzo użytecznym zastosowaniem *implicit konwersji* jest rozszerzanie interfejsu istniejących klas, bez modyfikowania klasy samej w sobie (ang. _extension methods_). 

Załóżmy, że potrzebujemy metody, która skraca `String` do zadanej długości dodając "..." na końcu. Można oczywiście zaimplementować ją tak, aby przyjmowała wartość do skrócenia jako argument, jednak fajnie by było wywołać ją na wartości do skrócenia tak, jakby metoda była częścią interfejsu `String`a. 

W Scali można to osiągnąć za pomocą klasy, która przyjmuje jako argument konstruktora wartość oryginalną i zawiera metody operujące na tej wartości, oraz *implicit* konwersji z klasy oryginalnej na tą zawierającą nowe metody.

<iframe height="400px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=13d92Wh/0&layout=v80"></iframe>

Ten schemat jest bardzo często powtarzany, dlatego istnieje też możliwość zapisu `implicit class TOps(val t: T)`, który automatycznie generuje konwersję `T => TOps`. 

#### Ćwiczenie 1

W miejsce `???` wpisz wartości odnalezionych implicit parametrów. 

```scala
@inline def implicitly[T](implicit e: T) = e
```

<iframe height="800px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=BajMXEu/1&layout=v80"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Zawsze wybierana jest wartość "nabliższa" i najbardziej "konkretna". Najpierw wartości lokalne, potem companion objecty parametrów generycznych, następnie companion object klasy przyjmującej generyki. </span><

Rozwiązanie: [https://scalafiddle.io/sf/oDx6S94/0](https://scalafiddle.io/sf/oDx6S94/0)

#### Ćwiczenie 2

Rozszerz interfejs `Boolean`a o metody `and` i `or` przyjmujące drugiego `Boolean`a i zwracające odpowiednio ich koniunkcję (`&&`) i alternatywę (`||`).

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=2Uu65cJ/0&layout=v60"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Musisz zastosować mechanizm implicit class.</span><

*Podpowiedź 2:* ><span style="color:white;background:white">Definicja klasy może wygladać tak: implicit class BooleanOps(val b: Boolean) { ??? }</span><

Rozwiązanie: [https://scalafiddle.io/sf/2wfMJKG/0](https://scalafiddle.io/sf/2wfMJKG/0)

### Type classy

Type classy to bardzo potężny mechanizm programowania funkcyjnego zapewniający polimorficzne wywołania metod. W Scali można ten mechanizm traktować jako alternatywę dla dziedziczenia i nadpisywania implementacji z programowania obiektowego (tzw. [ad-hoc polimorfizm](http://blog.jaceklaskowski.pl/2015/05/15/ad-hoc-polymorphism-in-scala-with-type-classes.html)).

Rozważmy taki problem: potrzebujemy mechanizmu zapisującego sekwencję elementów do pliku w formacie CSV. Stosując metody programowania obiektowego, możemy zdefiniować interfejs `CsvWriter` i zaimplementować go w każdym typie, który chcemy serializować. Takie podejście może być problemem, jeśli chcemy serializować klasy z jakiejś biblioteki i nie możemy modyfikować tych klas. 

W takim przypadku lepszym rozwiązaniem będzie stworzenie type classy `CsvWriter[T]` i stworzenie instancji dla klas, które będziemy serializować.
 
<iframe height="800px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=wtb8EPs/0&layout=v90"></iframe>

W ten sposób możemy bez najmniejszego problemu zdefiniować logikę serializacji dla klas z biblioteki standardowej Javy i reużywać zaimplmentowane typeclassy, tworząc serializację bardziej złożonych typów. Cała implementacja bardzo mocno polega na *implicitach* definiowanych w *companion objectach*. W obiekcie `CsvWriter` definiujemy implementacje dla typów podstawowych, natomiast dla klas `A` i `B` implementacje znajdują się w ich *companion objectach*.

#### Ćwiczenie

Zaimplementuj type classy, które pozwolą odczytać zapisane wcześniej dane.
 
<iframe height="800px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=RkHxzuw/0&layout=v98"></iframe>

Rozwiązanie: [https://scalafiddle.io/sf/OY9hTy7/0](https://scalafiddle.io/sf/OY9hTy7/0)

### Materiały

* [Oficjalna dokumentacja dotycząca implicit parametrów](http://docs.scala-lang.org/tour/implicit-parameters.html)
* [Oficjalna dokumentacja dotycząca implicit konwersji](http://docs.scala-lang.org/tour/implicit-conversions.html)
* [Opis logiki poszukiwania implicitów](https://stackoverflow.com/questions/5598085/where-does-scala-look-for-implicits)
* [Cats - Type Classes](https://typelevel.org/cats/typeclasses.html)
