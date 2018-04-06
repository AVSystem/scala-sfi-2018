## Najciekawsze elementy biblioteki standardowej Scali

Scala zawdzięcza swoją popularność między innymi bardzo bogatej bibliotece standardowej. Znajdziemy w niej bogaty zbiór kolekcji zarówno mutowalnych jak i niemutowalnych, klasy wspierające obsługę błędów czy też pracę z wątkami. 

### Kolekcje

Scala zapewnia trzy podstawowe rodzaje kolekcji:
* mutowalne - `scala.collection.mutable`,
* niemutowalne - `scala.collection.immutable`,
* równoległe - `scala.collection.parallel`.

Ponadto w pakiecie `scala.collection` znajdziemy nadtypy, które nie dają nam interfejsu zmieniającego kolekcję, ale też nie dają gwarancji niemutowalności samej instancji. 

Na poniższym obrazku zaprezentowane są kolekcje z pakietu `scala.collection`:

![](http://docs.scala-lang.org/resources/images/collections.png)

*Hierarchia kolekcji w Scali - obrazek z [oficjalnej dokumentacji](http://docs.scala-lang.org/overviews/collections/overview.html)*

Najważniejsze elementy powyższych interfejsów:

* `Traversable` - [oficjalna dokumentacja](http://docs.scala-lang.org/overviews/collections/trait-traversable.html)
    * `foreach` - pozwala wykonać zadaną operację na każdym elemencie kolekcji.
    * `++` - łączy dwa `Traversable` w jeden nowy.
    * `map`, `flatMap` i `collect` - tworzą nową kolekcję na podstawie elementów istniejącej.
    * `size`, `isEmpty`, `nonEmpty` - sprawdzanie rozmiaru.
    * `head`, `last`, `headOption`, `find` - wybieranie elementu z kolekcji.
    * `exists`, `forall`, `count` - sprawdzanie elementów kolekcji.
    * `tail`, `take`, `takeWhile`, `drop`, `filter` - wybieranie podzbioru elementów z kolekcji.
    * `partition`, `groupBy` - podział elementów kolekcji na kilka innych.
    * `foldLeft`, `foldRight`, `reduceLeft` - aplikacja funkcji dwu-argumentowej na elementach kolekcji.
    * `toArray`, `toList`, `toSeq`, itp. - konwersje do innych typów kolekcji.
* `Iterable` - [oficjalna dokumentacja](http://docs.scala-lang.org/overviews/collections/trait-iterable.html)
    * `iterator` - zwraca iterator z danej kolekcji.
    * `grouped` - iterator zwracający elementy podzielone na grupy o ustalonym rozmiarze.
    * `sliding` - iterator zwracający kolejne pozycje okna przesuwnego o zadanym rozmiarze.
    * `takeRight`, `dropRight` - odpowiednik `take` i `drop` działające od końca kolekcji.
    * `zip`, `zipAll`, `zipWithIndex` - łączy w pary kolejne elementy z elementami z przekazanej kolekcji (lub z indeksem).
* `Seq` - [oficjalna dokumentacja](http://docs.scala-lang.org/overviews/collections/seqs.html)
    * `apply`, `indices`, `indexOf` - operacje związane z indeksami.
    * `updated`, `update` (mutowalne) - zmiana elementu po indeksie.
    * `sorted`, `sortBy` - sortowanie elementów.
    * `reverse`, `resverseIterator` - odwrócenie kolejności.
    * podtyp `Buffer` - pozwalają na dodawanie i usuwanie elementów (`insert`/`+=`, `remove`/`-=`).
* `Set` - [oficjalna dokumentacja](http://docs.scala-lang.org/overviews/collections/sets.html)
    * `contains`, `subsetOf` - sprawdzanie zawartości.
    * `+`, `++`, `-`, `--` - stworzenie nowego zbioru poprzez dodanie lub usunięcie elementów.
    * `intersect`/`&`, `union`/`|`, `diff`/`&~` - część wspólna, suma i różnica zbiorów.
    * `add`, `remove`, `retain` - operacje na mutowalnym zbiorze.
* `Map` - [oficjalna dokumentacja](http://docs.scala-lang.org/overviews/collections/maps.html)
    * `apply`, `get`, `getOrElse`, `contains` - przeszukiwanie mapy.
    * `keys`, `values` - pobranie kluczy lub wartości z mapy.
    * `filterKeys`, `mapValues` - transformacje na podstawie kluczy/wartości.
    * `updated`, `+`, `++`, `-`, `-=` - tworzenie nowej mapy z modyfikacją zawartości.
    * `put`, `remove`, `retain`, `transform` - operacje na mutowalnej mapie.

#### Tworzenie kolekcji

Kolekcję dowolnego typu można utworzyć za pomocą metody `apply` z *companion object*u danego typu. Wszystkie domyślne konstruktory z pakietu `scala.collection` tworzą niemutowalne kolekcje.

<iframe height="400px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=dvPUda3/1&layout=v55"></iframe>

Każda kolekcja posiada też odpowiadający jej `builder` ułatwiający jej budowanie.

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=oy5XBpG/0&layout=v70"></iframe>

#### Transformacje kolekcji

Bardzo bogaty interfejs kolekcji umożliwia zwięzłe wyrażenie operacji na kolekcjach. Należy jednak uważać na operacje, które tworzą nową kolekcję jako rezultat wykonania - to może być problem wydajnościowy przy pracy z dużymi kolekcjami. Zwykle mutowalne interfejsy zapewniają alternatywną metodę modyfikującą aktualną instancję kolekcji, np. `filter` i `retain`.

Gdy chcemy wykonać wiele operacji na jednej niemutowalnej kolekcji i nie chcemy płacić ceny za pośrednie wyniki, możemy:

 * użyć widoków (aczkolwiek nie ma pewności, czy nie zostaną wyrzucone z API - https://github.com/scala/collection-strawman/issues/13).
 * wywołać `.iterator`, kolejkować operacje w kontekście iteratora, a następnie wywołać metodę `toKolekcja`, aby stworzyć wynik. Iterator w Scali działa podobnie jak streamy w Javie 8 - operacje wykonywane są lazy.
 * stworzyć ręcznie mutowalną kolekcję Javową/Scalową, wykonać odpowiednie transformacje, a na końcu stworzyć kolekcję wynikową.

<iframe height="400px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=NzGUx1l/0&layout=v70"></iframe>

#### Porównywanie kolekcji

Biblioteka standardowa określa proste zasady porównywania kolekcji:

* jeśli kolekcje pochodzą z różnych kategorii (`Map`/`Set`/`Seq`), to są różne,
* w przeciwnym wypadku są równe, jeśli posiadają te same elementy (w przypadku sekwencji z zachowaniem kolejności).

#### Ćwiczenie 1

Zaimplementuj metodę `buildSquaresMap` tak, aby zwracała mapę z wartości podanych w parametrze `values` na ich kwadrat.

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=ugXd9vj/2&layout=v70"></iframe>
 
*Podpowiedź 1:* ><span style="color:white;background:white">Jeśli masz sekwencję par (czyli Seq[(A, B)]) to możesz ją przetransformować na Map[A, B] za pomocą metody _.toMap.</span><

*Podpowiedź 2:* ><span style="color:white;background:white">Do stworzenia sekwencji par może przydać się metoda _.map(v => ???).</span><

Rozwiązanie: [https://scalafiddle.io/sf/acld35W/0](https://scalafiddle.io/sf/acld35W/0)

#### Ćwiczenie 2

Dokończ implementację metody `sum`, która oblicza sumę elementów z przekazanej kolekcji za pomocą metody `foldLeft`.

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=MjS8JS5/2&layout=v70"></iframe>
 
*Podpowiedź 1:* ><span style="color:white;background:white">Sygnatura metody foldLeft: def foldLeft[B](z: B)(op: (B, A) => B): B. W przypadku tego ćwiczenia A = Double.</span><

*Podpowiedź 2:* ><span style="color:white;background:white">Uwaga na inferencję typów: Jeśli jako parametr "z" podasz 0, kompilator uzna, że typ B to Int. </span><

Rozwiązanie: [https://scalafiddle.io/sf/TyJnmGC/0](https://scalafiddle.io/sf/TyJnmGC/0)

#### Ćwiczenie 3

Zaimplementuj metodę `transform` w taki sposób, aby zwróciła sekwencję elementów z wejściowej sekwencji `input`, które spełniają jeden z poniższych warunków:
* są mniejsze niż zadany `separator` i jednocześnie są parzyste,
* są większe niż zadany `separator` i jednocześnie są nieparzyste - te elementy powinny być podniesione do kwadratu.

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=5zzDxUo/6&layout=v70"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Najkrótszym rozwiązaniem jest zastosowanie metody _.collect, która przyjmuje PartialFunction i do wynikowej kolekcji przenosi tylko wyniki z dopasowanych wartości.</span><
*Podpowiedź 2:* ><span style="color:white;background:white">Metoda _.collect to połączenie metod _.filter i _.map. Możesz spróbować zaimplemntować tą operację w dwóch krokach.</span><

Rozwiązanie: [https://scalafiddle.io/sf/VwKbaLx/0](https://scalafiddle.io/sf/VwKbaLx/0)

### Reprezentacja błędów w systemie typów

Projektując API w Scali mamy co najmniej kilka możliwości zamodelowania reprezentacji błędów. 

Najprostszy sposób to rzucanie wyjątków, ale ze względu na brak mechanizmu [checked exceptions](http://java.sun.com/docs/books/jls/third_edition/html/exceptions.html) kompilator nie będzie pomagał w sprawdzeniu, czy obsługa błędów jest prawidłowa. 

Biblioteka standardowa zapewnia trzy klasy abstrakcyjne, którymi możemy reprezentować wynik działania metody lub błąd wykonania:
* `Option[T]` - reprezentuje wartości opcjonalne poprzez `Some(v: T)` lub `None`.
* `Try[T]` - reprezentuje obliczenia, które mogą zwrócić wartość `Success(v: T)` lub wyjątek `Failure(ex: Throwable)`. 
* `Either[A, B]` - reprezentuje wartość, która może być jednego z dwóch podanych typów. Często jest stosowany jako alternatywa dla `Option` zawierająca dodatkową informację o błędzie - w tym przypadku błąd jest reprezentowany jako `Left(error: A)`, a sukces jako `Right(v: B)`.

`Option` bywa bardzo pomocny na styku z interfejsami starszych bibliotek Javowych, które zwracają `null` w razie niepowodzenia. Wystarczy otoczyć wywołanie metody w konstruktor `Option` i bezpiecznie operować na zwróconej wartości tylko, gdy jest ona obecna: 

```scala
Option(javaApi.call())
  .map(v => ???)
  .getOrElse(???)
```

Podobnie `Try` pozwala w zwięzły sposób poradzić sobie z metodami rzucającymi wyjątki. Otoczenie wywołania w `Try` łapie rzucony wewnątrz wyjątek i zwraca `Failure` zawierający ten wyjątek. 

```scala
Try(javaApi.call())
  .map(v => ???)
  .getOrElse(???)
```

W przypadku `Either` możliwe jest stworzenie projekcji na `.left` lub `.right` i operowanie na wartości pryz założeniu, że zawiera wartość odpowiednio `Left` lub `Right`. Jeśli wartość się nie zgadza, to operacje są pomijane.

```scala
val example: Either[String, Int] = ???
val transformed: Either[Int, String] = 
  example
    .right.map((v: Int) => v.toString)
    .left.map((v: String) => v.toInt)
```

#### Ćwiczenie

W obiekcie `Api` znajduje się implementacja metody `checkPrime`, która sprawdza, czy podana liczba jest pierwsza. Ponadto jeśli podany argument jest mniejszy niż 2, to rzuca wyjątek. Używając metody `checkPrime` zaimplementuj metodę `isNotPrime`, która zwraca `Either[String, Boolean]`, przyjmując `Right` jako odpowiedź dla poprawnych zapytań i `Left` jako zgłoszony błąd dla niepoprawnych wywołań (metoda `getMessage()` na wyjątku). 

<iframe height="700px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=CD2MSow/4&layout=v70"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Metoda checkPrime rzuca wyjątek jeśli podany argument jest niepoprawny. Try(???) może się przydać, żeby złapać ten wyjątek w naszej implementacji.</span><

*Podpowiedź 2:* ><span style="color:white;background:white">Try można skonwertować to Either za pomocą _.toEither.</span><

*Podpowiedź 3:* ><span style="color:white;background:white">Aby wykonać jakieś operacje na wartości tylko w przypadku Left, użyj widoku _.left. Na przykład _.left.map(v => ???).</span><

Rozwiązanie: [https://scalafiddle.io/sf/zDKDNaK/0](https://scalafiddle.io/sf/zDKDNaK/0)

### Praca z wątkami

Jedną z najbardziej wyróżniających się cech na tle Javy jest praca z kodem wykonywanym równolegle. `Future[T]` to klasa, która reprezentuje wartość, która może być jeszcze nieobliczona - w szczególności może być obliczana równolegle w innym wątku. Standardowo uruchomienie obliczeń w nowym wątku wygląda tak:

```scala
implicit val executionContext: ExecutionContext = ...
val sum: Future[Int] = Future {
  2 + 2
}
```

#### ExecutionContext

`ExecutionContext` zarządza wykonaniem obliczeń na dostępnej dla niego puli wątków. W Scali jest dostępna domyślna instancja `scala.concurrent.ExecutionContext.Implicits.global`, która wewnątrz korzysta z [`ForkJoinPool`](http://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html). Można też łatwo dokonać konwersji dowolnego `Executor`a na `ExecutionContext` lub zaimplementować własny, np:

```scala
object RunNow extends ExecutionContext {
  def execute(runnable: Runnable): Unit =
    runnable.run()

  def reportFailure(cause: Throwable): Unit =
    cause.printStackTrace()
}
```

#### Obsługa wyników

Podobnie jak `Try`, `Future` zapewnia bogate API, m&#46;in. (sygnatury uproszczone):
* `map[S](f: T => S): Future[S]`
  `flatMap[S](f: T => Future[S]): Future[S]`
  pozwalają na dalszą obróbkę otrzymanego wyniku,
* `onComplete[U](f: Try[T] => U): Unit` - pozwala wykonać kolejne operacje po zakończeniu (sukcesem lub wyjątkiem) wszystkich obliczeń,
* `Future.sequence[A](in: Seq[Future[A]]): Future[Seq[A]]`
  `Future.traverse[A, B](in: Seq[A])(fn: A => Future[B]): Future[Seq[B]]`
  zbierają wiele operacji równoległych w jeden `Future` zawierający sekwencję wyników,
* `Await.result` - pozwala poczekać w wywołującym wątku na wynik obliczeń z zadanego `Future` (uwaga: nie działa w Scala.js ze względu na model wykonania JavaScriptu).

#### Ćwiczenie

Zaimplementuj metodę `makeLowerCaseString`tak, aby zwróciła `Future` zawierający sklejone ciągi znaków przekazane jako argument, dodatkowo zmapowane na małe litery (`_.toLowerCase`). 

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=jqmxWTp/2&layout=v70"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Zamiany Seq[Future[A]] na Future[Seq[A]] można dokonać za pomocą Future.sequence.</span><
*Podpowiedź 2:* ><span style="color:white;background:white">Sekwencję Stringów można sklepić do jednego Stringa za pomocą metody _.mkString(separator).</span><

Rozwiązanie: https://scalafiddle.io/sf/1advK1u/0

### For comprehension

Na początku tego kursu przedstawiona została pętla `for`, która w Scali wygląda zupełnie inaczej niż w Javie. W tej części zaprezentujemy pełnię możliwości tej notacji.

Ogólnie składnię *for comprehension* można przedstawić jako `for (enumerators) yield e`, gdzie `enumerators` to lista rozdzielonych średnikiem generatorów. Operacja oblicza wyrażenie `e` dla każdego zestawu wartości z generatorów, a ostatecznie zwraca listę wszystkich wyników. Każdy generator może korzystać z wartości z poprzednich generatorów oraz posiadać filtr. 

Poniższy przykład generuje pary liczb, które w sumie dają 10.

<iframe height="300px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=11Yh4HH/0&layout=v70"></iframe>

Można patrzeć na for-comprehension jak na iloczyn kartezjański elementów pochodzących z enumeratorów. W powyższym przykładzie generowane są wszystkie pary elementów `(1, (1..10))`, `(2, (2..10))` itd. 

Można też stosować wersję bez `yield`, która działa jak pętla foreach i wartością wyrażenia jest `()`. 

Generatory można też wypisać w `{}` zamiast `()` - wtedy nie trzeba rozdzielać ich średnikami, jeśli nie są wypisane w tej samej linii.

<iframe height="300px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=REq5mIT/0&layout=v50"></iframe>

Jako generatory mogą również posłużyć typy poznane powyżej, czyli: kolekcje, `Future`, `Option`, `Try` czy `Either`. Intuicyjnie można patrzeć, że wymienione typy to kolekcje posiadające 0 lub 1 element.

<iframe height="300px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=6wu1bW5/2&layout=v70"></iframe>

For comprehension jest tak naprawdę przyjemniejszą formą zapisu ciągu wywołań metod `withFilter`, `map` i `flatMap`. Pierwszy przykład zapisany wprost wyglądałby tak:

<iframe height="300px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=KhKqkOK/0&layout=v70"></iframe>

Z tego wynika, że jeśli Twoja klasa posiada zaimplementowane te metody, to możesz używać jej w *for comprehension*. 

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=7czP9tl/1&layout=v90"></iframe>

`for comprehension` często się wykorzystuje przy pracy z monadami (np. `Option`). Jeżeli sami tworzymy klasę do `for comprehension` metody `map` oraz `flatMap` powinny spełniać pewne [prawa](https://miklos-martin.github.io/learn/fp/2016/03/10/monad-laws-for-regular-developers.html) tak aby nasza klasa była monadą.

#### Ćwiczenie 1

Zaimplementuj metodę `threeElements`, która zwróci wszystkie kombinacje trzech różnych liczb naturalnych, które w sumie dają zadaną wartość. Elementy powinny być rosnące.

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=VEWl6sQ/1&layout=v70"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Powyżej zaimplementowany jest przykład dla par liczb.</span><

*Podpowiedź 2:* ><span style="color:white;background:white">Dodaj jeszcze jeden generator do tego przykładu, aby uzyskać trójki.</span><

*Podpowiedź 3:* ><span style="color:white;background:white">For comprehension generuje sekwencję elementów. Typem zwracanym ma być Set, więc pomocna może być metoda _.toSet.</span><

Rozwiązanie: [https://scalafiddle.io/sf/tFCWkWd/0](https://scalafiddle.io/sf/tFCWkWd/0)

#### Ćwiczenie 2

Zaimplementuj metodę `productSum` tak, aby zwróciła sumę wszystkich elementów iloczynu kartezjańskiego obu przekazanych sekwencji. 

<!--
for {
    is <- ints;
    ds <- doubles
  } yield {
    (for {
      i <- is;
      d <- ds
    } yield i * d).sum
  }
-->

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=8NCRdwU/2&layout=v70"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Zacznij implementację od pomocniczej metody: def productSum(ints: Seq[Int], doubles: Seq[Double]): Double</span><

*Podpowiedź 2:* ><span style="color:white;background:white">For comprehension można zagnieździć wewnątrz innego fora.</span><

*Podpowiedź 3:* ><span style="color:white;background:white">Zewnętrzny for może rozpakować Future. Drugi może zapewnić iterację przez elementy sekwencji.</span><

Rozwiązanie: [https://scalafiddle.io/sf/3QQEGI6/0](https://scalafiddle.io/sf/3QQEGI6/0)

#### Ćwiczenie 3*

Zaimplementuj metody `map`, `flatMap` i `withFilter` dla typu `Maybe`, który ma być odpowiednikiem `Option`. 

<iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=mfahjv4/3&layout=v70"></iframe>

*Podpowiedź 1:* ><span style="color:white;background:white">Każda operacja na None zwraca None, ponieważ nie mamy wartości na której moglibyśmy operować. Tutaj spodziewamy się analogicznego zachowania dla Empty.</span><

*Podpowiedź 2:* ><span style="color:white;background:white">Operacje na istniejącej wartości powinny zwracać nowy element ze zmienioną wartością.</span><

Rozwiązanie: [https://scalafiddle.io/sf/0gcVdBO/0](https://scalafiddle.io/sf/0gcVdBO/0)

### Materiały

* [Oficjalna dokumentacja dotycząca kolekcji](http://docs.scala-lang.org/overviews/collections/introduction.html)
* [Oficjalna dokumentacja dotycząca Future](http://docs.scala-lang.org/overviews/core/futures.html)
* [Wstęp do Scali - For Comprehensions](https://docs.scala-lang.org/tour/for-comprehensions.html)
* [Wydajość kolekcji w Scali](http://www.lihaoyi.com/post/BenchmarkingScalaCollections.html)
* [Scala Exercises - Biblioteka standardowa](https://www.scala-exercises.org/std_lib/asserts)
    

