## Podsumowanie

Scala to bardzo rozbudowany język. Na końcu każdej części zebraliśmy interesujące materiały, które mogą posłużyć do poszerzania wiedzy w danych tematach. 

### (Subiektywnie) najważniejsze pozytywne jak i negatywne cechy Scali

Scala ma swoje dobre i złe strony. Wymaga od zespołu większej dyscypliny oraz umiejętności posługiwania się wieloma paradygmatami (FP, OOP) jednak w odpowiednich rękach jest potężnym narzędziem pozwalającym pisać (subiektywnie) czytelny oraz reużywalny kod. Ekosystem Scalowy rozwija się w wielu kierunkach, także trzeba być przygotowanym na nieudane eksperymenty lub porzucone inicjatywy, z drugiej strony pozwala to programistom zacząć bardzo wcześnie korzystać z rzeczy które z czasem są przenoszone do bardziej konserwatywnych języków (Java) - przykład to lambdy, pójście w stronę immutability, value classy. 

#### Pozytywy

 * umożliwienie wyrażania pewnych idiomów w zwięzły sposób (case classy, parametry nazwane, domyślne wartości parametrów)
 * zaawansowany system typów
 * zachęcanie do pisania immutable kodu poprzez udostępnienie odpowiednich struktur językowych oraz elementów biblioteki standardowej (ADT, immutable kolekcje, val)
 * możliwość użycia zaawansowanego programowania funkcyjnego (ad-hoc polimorfizm, higher kinded types), ekosystem bibliotek napisanych w ten sposób
 * interop z Javą stojący na wysokim poziomie
 * wiele platform docelowych - ScalaJS, Scala native
 * makra - alternatywa dla refleksji

#### Negatywy
 
 * duża dowolność w pisaniu kodu powoduje, że kod Scalowy napisany przez jedną osobę może wyglądać zdecydowanie inaczej od tej napisanej przez inną. Może to powodować problemy przy wzroście liczebności zespołu.
 * zdecydowanie mniejsza (w porównaniu z Javą) pula kandydatów, którzy już mają doświadczenie ze Scalą
 * długie czasy kompilacji w porównaniu z Javą
 * im bardziej funkcyjny kod piszemy, tym większa szansa, że nasze IDE będzie raportowało false negative/positive, jak chodzi o kompilację
 * lekki podział w społeczności - Typelevel Scala, Lightbend Scala
 * dużo "magii", która może odrzucać osoby przyzwyczajone do explicitności Javy
 * łatwo (zwłaszcza na początku) pisać kod, który alokuje bardzo duże ilości tymczasowych obiektów (kolekcje pośrednie, różnego rodzaju wrappery) - zwykle nie jest to problem, ale trzeba mieć świadomość, jak część maszynerii działa pod spodem
 * brak kompatybilności (binarnej jak i źródłowej) pomiędzy major wersjami, kod skompilowany w Scali 2.11 nie będzie działał poprawnie z kodem skompilowanym w Scali 2.12 oraz może się nie kompilować od razu w nowej major wersji scali. Może to powodować też problemy przy aktualizacji Scali, gdy jakaś nasza pośrednia zależność jeszcze nie została scross-compilowana do nowej wersji Scali. 
 * problemy wynikające z interopu pomiędzy Javą i Scalą
 * brak odpowiednika Javowych enumów, który zapewniałby wszystkie jego funkcjonalności.

### Książki

1. [Scala By Example](https://www.scala-lang.org/docu/files/ScalaByExample.pdf) - Martin Odersky, 2014
2. [Shapeless Guide](https://github.com/underscoreio/shapeless-guide/blob/develop/dist/shapeless-guide.pdf) - Dave Gurnell, 2017
3. [Scala with Cats](https://underscore.io/books/scala-with-cats/) - Noel Welsh and Dave Gurnell, 2017
4. [Functional Programming in Scala](https://www.manning.com/books/functional-programming-in-scala) - Paul Chiusano and Runar Bjarnason, 2014

### Kursy online

1. [Scala Documentation](http://docs.scala-lang.org/)
2. [Oficjalna dokumentacja - wprowadzenie do Scali](http://docs.scala-lang.org/tour/tour-of-scala.html)
2. [Scala Exercises](https://www.scala-exercises.org/)
3. [Effective Scala](http://twitter.github.io/effectivescala/)
4. [Specjalizacja z Coursery](https://www.coursera.org/specializations/scala?action=enroll)

## Wartościowe materiały

1. [Typy typów Scalowych](http://ktoso.github.io/scala-types-of-types/)
2. [Reddit Scalowy](https://www.reddit.com/r/scala/)
3. [Scala Improvement Process](https://docs.scala-lang.org/sips/all.html)
4. [Post o modyfikatorach dostępu](http://www.jesperdj.com/2016/01/08/scala-access-modifiers-and-qualifiers-in-detail/)
5. [Oficjalna dokumentacja dotycząca interpolacji stringów](https://docs.scala-lang.org/overviews/core/string-interpolation.html)
6. [Informacja o data classach w Javie](http://cr.openjdk.java.net/~briangoetz/amber/datum.html)
7. [Cake Pattern w Scali czyli stackowanie traitów w praktyce](https://www.cakesolutions.net/teamblogs/2011/12/19/cake-pattern-in-depth)
8. [I dyskusja dlaczego (może) nie jest to najlepszy sposób budowania aplikacji](https://stackoverflow.com/questions/7860163/what-are-some-compelling-use-cases-for-dependent-method-types)
9. [Higher Kinded Types](https://typelevel.org/blog/2016/08/21/hkts-moving-forward.html)
1. [Type classy 1](https://blog.scalac.io/2017/04/19/typeclasses-in-scala.html)
1. [Type classy 2](http://danielwestheide.com/blog/2013/02/06/the-neophytes-guide-to-scala-part-12-type-classes.html)
1. [Java/Scala cheat sheet](http://rea.tech/java-to-scala-cheatsheet/)
1. [Wydajość kolekcji w Scali](http://www.lihaoyi.com/post/BenchmarkingScalaCollections.html)
1. [Cats - Type Classes](https://typelevel.org/cats/typeclasses.html)
