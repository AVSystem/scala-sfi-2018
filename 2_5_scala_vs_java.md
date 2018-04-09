##  Mniej istotne różnice pomiędzy Scalą i Javą
   
Część druga różnic pomiędzy Scalą i Javą.

### Varargsy

W Javie 5 zostały wprowadzone do języka varargsy, które pozwalają zadeklarować ostatni parametr w metodzie jako przyjmujący pod spodem tablicę, ale nie wymagający od wywołującego metody explicite zadeklarowania jej:
 
 ```Java
 public static void printInts(int... ints) {
    for (int i : ints) {
        System.out.println(i);
    }
 }
 
 printInts(1, 2, 3);
 printInts(new int[] {1, 2, 3});
 ```

Oczywiście w przypadku pierwszego wywołania kompilator Javy w wygenerowanym bajtkodzie sam stworzy tablicę intów z odpowiednimi elementami. Varargsy stosuje się, aby usprawnić korzystanie z danego API.
 
Scala również posiada wsparcie dla varargsów, różnica pojawia się jedynie w składni oraz tym, że możemy przekazać do takiej metody nie tylko tablicę ale dowolną sekwencję, którą należy oznaczyć adnotacją typu `:_*`.
 
<iframe height="500px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=bKXnyfy/0&layout=v50"></iframe>
 
### Importy lokalne, importy z aliasem.

Mały, ale czasami bardzo ułatwiający życie feature języka. W przypadku Javy wszystkie importy muszą znajdować się po deklaracji pakietu, dodatkowo nie można na potrzeby danego pliku zmienić nazwy, pod jaką występuje zaimportowany element. 
 
Scala pozwala nie tylko importować elementy w bardziej ograniczonym zakresie (np. w obrębie danej funkcji), możemy również stworzyć alias dla importu. Aliasy są bardzo pomocne, gdy w danym zakresie musimy posługiwać się dwoma klasami z różnych pakietów, ale o tej samej nazwie. W Javie do jednej lub obu należałoby odwoływać się przez nazwę zawierającą fragment pakietu. 
 
<iframe height="800px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=CzLXWrT/2&layout=v70"></iframe>
 
Nie musimy "zaśmiecać" przestrzeni nazw całego pliku importem, który jest potrzebny tylko w jednym specyficznym miejscu. Jest to przełożenie zasady, aby deklarować zmienne w jak najwęższym zakresie, na importy.

### Średniki, a właściwie ich brak

W większości przypadków, pisząc w Scali, można zupełnie pomijać średniki, ponieważ są inferowane podczas procesu kompilacji (sytuacja podobna do JavaScriptu, gdzie średniki również są opcjonalne). Oprócz skończonej liczby sytuacji koniec linii traktowany jest jako średnik i zakończenie danego wyrażenia.

<iframe height="400px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=CGkthVj/0&layout=v70"></iframe>

### Słówko kluczowe `return`

Podobnie jak w przypadku średnikamów, w większości kodu Scalowego nie używa się explicite słówka kluczowego `return`, a polega się na tym, że wartość ostatniego wyrażenia traktowana jest jak wartość zwracana.

Na początku programowanie bez użycia `return` może wydawać się dziwne i nienaturalne, jednak z czasem nie ma z tym żadnego problemu. Dodatkowo każdy kod używający explicite `return` można zrefaktorować na taki bez `return` bez zmiany zachowania.
    
W przypadku Scali nieużywanie `return` wiąże się również z jego zachowaniem. W zależności od kontekstu użycie `return` może być mniej lub bardziej zaskakujące.
    
<iframe height="800px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=POtQLXe/0&layout=v70"></iframe>

### Metody `apply` i `unapply`

Część "magii" Scalowej zawarta jest w dwóch niepozornie nazwanych methodach `apply` oraz `unapply`. 

Pierwsza z nich pozwala nam "wywoływać" dowolną rzecz - kompilator, gdy natrafi na kod `identyfikator(...)` zamienia go na `identyfikator.apply(...)`, następnie sprawdzane jest, czy dana instancja klasy (jakakolwiek by ona nie była) posiada metodę `apply` z daną sygnaturą. 

Przykładowo dzięki temu jesteśmy w stanie tworzyć instancje `case class` bez użycia słówka kluczowego `new` czy odwoływać się do elementów różnych kolekcji w sposób zunifikowany.

<iframe height="800px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=gXuuVw8/0&layout=v70"></iframe>

`apply` jest więc jednym z wielu ułatwień składniowych oferowanych przez język. 

Metoda `unapply` jest zdecydowanie ciekawsza. Odgrywa ona ważną rolę, gdy w pattern matchingu chcemy używać bardziej skomplikowanych wzorców, dobierać się do poszczególnych elementów danej struktury lub korzystać z zagnieżdżonych wzorców. W nomenklaturze Scalowej `object` z metodą `unapply` nazywany jest ekstraktorem.

<iframe height="800px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=mYoSNdl/0&layout=v70"></iframe>

Dzięki metodzie `unapply` jestśmy w stanie tworzyć reużywalne wzorce, które potem możemy wykorzystać w pattern matchingu. 

Metoda `unapply` może zwrócić albo typ `Boolean` - wtedy jest to "ekstraktor", który tak na prawdę nic nie ekstrahuje, jedynie stwierdza, czy dany argument spełnia jakiś warunek. Możemy również zwrócić `Option[(T1, T2, ...)]`. Poszczególne elementy krotki będzie można przypisać do zmiennych w pattern matchingu.

<iframe height="800px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=TbLtoAI/0&layout=v70"></iframe>

#### Ćwiczenie 1

Zaimplementuj ekstraktor `Prime` który przyjmuje typ `Int` i sprawdza, czy dana liczba jest pierwsza. W implementacji możesz wykorzystać funkcję `isPrime` zdefiniowaną w fiddlu.
 
<iframe height="800px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=25Cz4iD/0&layout=v70"></iframe>

### `try`/`catch` 
Podobnie jak w Javie, Scala wspiera model raportowania błędów poprzez rzucanie wyjątków. Składnia bloku `catch` różni od tej Javowej, ponieważ wykorzystywany tam jest pattern matching.
 
Podobnie jak w Javie kolejność wyjątków powinna być od najbardziej specyficznego do najbardziej ogólnego ponieważ bloki sprawdzane są z góry na dół. 
 
W Scali w bloku `catch` każdy wzorec dla wyjątku powinien mieć podany typ, w przeciwnym przypadku kompilator wygeneruje ostrzeżenie. Dodatkowo w Scali istnieje ekstraktor `NonFatal`, którego można użyć, gdy chcemy złapać wszystkie "sensowne" `Throwable`.
 
 
 ```Java
 try {
      //
 } catch (IOException e) {
      //
 } catch (Exception e) {
    
 }
 ```
 
 ```scala
 try { 
   // ... 
 } catch {
   case ioe: IOException => ... 
   case e: Exception => ...
 }
 ```
 <iframe height="600px" frameborder="0" style="width: 100%" src="https://embed.scalafiddle.io/embed?sfid=O7WFHj4/0&layout=v70"></iframe>
 
W Scali (podobnie jak w Javie) możemy również definiować blok `finally`, czyli kod, który wykona się po kodzie w bloku `try` oraz (opcjonalnie) `catch`. 
 
Niestety Scala nie posiada obsługi konstrukcji [try-with-resources](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html) wprowadzonej w Javie 7. Jeżeli chcemy w idiomatyczny sposób pracować z klasami, które implementują interfejs [AutoCloseable](https://docs.oracle.com/javase/8/docs/api/java/lang/AutoCloseable.html) mamy kilka opcji:
 
 * użycie gotowego rozwiązania, przykładowo [Try-Close monad](https://github.com/choppythelumberjack/tryclose)
 * zaimplementowanie logiki bezpośrednio w Javie
 * zaimplementowanie pomocniczej funkcji w Javie, która przyjmie logikę do wykonania w każdym z przypadków (`try`/`catch`/`finally`)
 
   ```Java
   public static <T, E extends AutoCloseable> T 
   tryWithResources(E closeable,  
                    Function<E, T> logic,  
                    Function<Exception, T> errorHandler,  
                    Consumer<Void> finallyBlock) {
       try (E c = closeable) {  
            return logic.apply(c);  
       } catch (Exception e) {  
            return errorHandler.apply(e);  
       } finally {  
            finallyBlock.accept(null);  
       }  
   }
   ```
### Interop z Javą

Często jako zaletę Scali podaje się interoperability z Javą, jednak jak to wygląda w praktyce?
 
Wywoływanie kodu Javowego ze Scali oprócz dziwnych przypadków szczególnych i [błędów w kompilatorze Scali](https://issues.scala-lang.org/browse/SI-10241) zazwyczaj jest bezproblemowe. Spokojnie możemy korzystać z bogactwa biblioteki standardowej Javy oraz jej rozległego ekosystemu. To, na co trzeba się jednak przygotować, to:
 
 * częste konwertowanie kolekcji z Javowych do Scalowych, tutaj bardzo pomocne są gotowe konwersje z [biblioteki standardowej](https://www.scala-lang.org/api/2.12.5/scala/collection/JavaConverters$.html). 

   Należy pamiętać, żeby nie korzystać z `JavaConverters` które są deprecated, ponieważ są to implicit konwersje. Ich wykorzystanie (jak sama nazwa implicit sugeruje) powoduje, że konwersja przebiega automatycznie. Zmniejsza to zdecydowanie czytelność kodu (często bez pomocy IDE nie wiadomo czy dana kolekcja to Scala czy już Java) oraz bardzo łatwo napisać kod, który w większości zajmuje się konwersją z jednej kolekcji do drugiej,
 * biblioteki Scalowe bardzo rzadko używają wartości `null` - zazwyczaj, gdy metoda może nie zwrócić wyniku, typ zwracany to `Option`, `Try` lub inny mechanizm pozwalający zakodować błąd za pomocą systemu typów. Użycie biblioteki Javowej ze Scali może wiązać się z koniecznością znalezienia jej odpowiednika napisanego w Scali lub napisania warstwy pośredniej, która zwraca/przyjmuje typy Scalowe i pod spodem konwertuje je na te Javowe. Na szczęście współczesne biblioteki Javowe posiadają bardzo sensowne API, więc nie jest to zwykle bardzo dużo pracy,
 * do przetwarzania asynchronicznego biblioteka standardowa Scali definiuje typ `Future`, dodatkowo istnieją jego alternatywy w bibliotekach typu `scalaz` oraz `Cats`. W ekosystemie Javowym często można spotkać `java.util.concurrent.Future` (wprowadzony w Javie 5), `java.util.concurrent.CompletableFuture` (wprowadzone w Javie 8) lub `com.google.common.util.concurrent.ListenableFuture` z Guavy. 
   Tak jak w przypadku kolekcji, możemy potrzebować skonwertować jeden smak `Future` na inny. Albo napiszemy własne konwersje podobne do `JavaConverters` lub skorzystamy z [istniejących rozwiązań](https://github.com/AVSystem/scala-commons).
 
Jak widać problemy w przypadku interopu Scala -> Java są takie, jakich można się spodziewać i wynikają z różnic w sposobie pisania kodu (idiomatyczność) oraz elementów biblioteki standardowej. 
    
(Nie)stety łatwość wywoływania Scali z Javy bardzo dużo zależy od tego, jaki kod Scalowy piszemy. Jeżeli traktujemy Scalę jako Javę++ i wykorzystujemy tylko jej podstawowe możliwości takie jak case classy, pattern matching, funkcje lokalne, rekursję ogonową, to nie powinniśmy mieć problemów innych niż te, które występują w interopie Scala -> Java.
 
Gdy jednak zdecydujemy się wykorzystywać bardziej zaawansowane cechy języka takie jak `AnyVal`, implicity, kowariancja/kontrawariancja, higher kinded types, wywoływanie API Scalowego może być nieprzyjemne. 
 
Czasami nawet coś prostego jak wywołanie metody ze Scalowego companion objectu jest kłopotliwe. Gdyby w poniższym przykładzie case clasa nie definiowała metody o takiej samej nazwie jak ta w companion objecie, to moglibyśmy wywołać ją w naturalny sposób `Foo.bar()` dzięki temu, że kompilator wygenerowałby przekierowanie do `Foo$.MODULE$.bar()`.
 
 
 ```Scala
 case class Foo(a: Int) {
   def bar: String = "case class Foo"
 }
 object Foo {
   def bar: String = "companion object Foo"
 }
 ```
 ```Java
 Foo$.MODULE$.bar();
 ```
 
Zdarza się, że biblioteki (tak jak np. `Akka`) posiadają osobne API dla Javy, które wrapują te Scalowe tak, aby ułatwić wywoływanie Java -> Scala.
 
W przypadku naszej firmy problemy interopowe nie były aż tak poważne, aby powstrzymać adopcję Scali jako wiodącego języka, w którym powstaje oprogramowanie. 
