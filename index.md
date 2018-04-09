# Jak się żyje ze Scalą?

> **Scala** ([/ˈskɑːlɑː/](https://en.wikipedia.org/wiki/Help:IPA/English "Help:IPA/English") [_SKAH-lah_](https://en.wikipedia.org/wiki/Help:Pronunciation_respelling_key "Help:Pronunciation respelling key"))[\[9\]](https://en.wikipedia.org/wiki/Scala_(programming_language)#cite_note-9) is a [general-purpose](https://en.wikipedia.org/wiki/General-purpose_programming_language "General-purpose programming language") [programming language](https://en.wikipedia.org/wiki/Programming_language "Programming language") providing support for [functional programming](https://en.wikipedia.org/wiki/Functional_programming "Functional programming") and a strong [static](https://en.wikipedia.org/wiki/Static_typing "Static typing") [type system](https://en.wikipedia.org/wiki/Type_system "Type system"). Designed to be concise,[\[10\]](https://en.wikipedia.org/wiki/Scala_(programming_language)#cite_note-10) many of Scala's design decisions aimed to address [criticisms of Java](https://en.wikipedia.org/wiki/Criticism_of_Java "Criticism of Java").[\[8\]](https://en.wikipedia.org/wiki/Scala_(programming_language)#cite_note-overview-8)

[Wikipedia](https://en.wikipedia.org/wiki/Scala_(programming_language))

Wybór języka jest bardzo ważną decyzją techniczną w projekcie, determinuje m&#46;in. środowisko wykonania, ekosystem i dostępne biblioteki. Decyzja ta ma również konsekwencje dla naszej organizacji na wyższym poziomie, należy odpowiedzieć sobie na pytania takie jak: dostępność kandydatów z doświadczeniem, czy język jest na tyle atrakcyjny, że ludzie bez doświadczenia będą chcieli się go nauczyć, jakość dostępnych narzędzi (IDE, debuggery, monitoring aplikacji), jak wygląda sprawa ze wsteczną kompatybilnością.

Oczywiście jak z każdą decyzją techniczną - są zady oraz walety i należy dokonać takiego wyboru z pełną świadomością tego, na co się piszemy. Podczas tego warsztatu przejdziemy przez główne elementy języka i postaramy się wam pokazać jego dobre i złe strony, dodatkowo akcentując elementy związane z programowaniem funkcyjnym. 

Scala jest obecnie jednym z [wielu](https://en.wikipedia.org/wiki/List_of_JVM_languages) języków dostępnych na JVM. Można sobie zadawać pytanie "czy w Anno Domini 2018 warto inwestować w znajomość tego języka?", Java 8 wprowadziła lambdy jako first-class citizen i patrząc na przyszłość, rzeczy typu value classy również są na horyzoncie. Po drugiej stronie mamy Kotlina - język uznawany za prostszy od Scali, który również obiecuje "lepszą Javę".

Według nas odpowiedź na pytanie "czy warto" brzmi - zdecydowanie (raczej) tak. Scala pomimo swoich wad jest językiem oferującym ogromne możliwości dla tych, którzy chcą z nich skorzystać. Zawiera szerokie wsparcie dla bardziej zaawansowanego programowania funkcyjnego oraz generycznego (higher kinded types, implicity, makra, inferencja, zaawansowany system typów, type classy). Z drugiej strony umożliwia wykorzystanie konstrukcji z programowania obiektowego (klasy, klasy abstrakcyjne, traity, polimorfizm). 

Warto też zaznaczyć możliwość uruchomienia Scali poza maszyną wirtualną Javy. Obecnie wspierana jest kompilacja [Scali do JavaScriptu](http://www.scala-js.org/) oraz [kodu natywnego](http://www.scala-native.org/en/latest/). [Scala.js](http://www.scala-js.org/) uznawany już jest za gotowy do użycia "na produkcji" oraz pojawiają się biblioteki oparte o ten projekt, które wspierają tworzenie aplikacji webowych w Scali, np.: [Udash](https://udash.io/) lub [scalajs-react](https://github.com/japgolly/scalajs-react).

W trakcie tych warsztatów zaprezentujemy najciekawsze elementy języka oraz spróbujemy je zastosować w ćwiczeniach. Zaczniemy od podstaw składni. Następnie skupimy się na elementach, które sprawiają, że Scala jest przez wielu uważana za lepszą Javę (a.k.a. Java++). Spojrzymy też na zestaw kolekcji oraz pracę z kodem asynchronicznym. Na koniec zahaczymy o tematy związane z zaawansowanym programowaniem funkcyjnym. 

Większość przykładów i ćwiczenia są edytowalne w przeglądarce dzięki [ScalaFiddle](https://scalafiddle.io). Za pomocą klawiszy `Ctrl + Spacja` można wywołać podpowiadanie dostępnych metod. To narzędzie oparte jest o projekt [Scala.js](http://www.scala-js.org/) - kompiluje wysłany kod do JavaScriptu, który wykonywany jest w przeglądarce. Ten fakt nie powinien być zauważalny pod kątem wyników działania kodu. Jedynym mankamentem, który tu napotkamy jest praca z kodem asynchronicznym, który w praktyce sprowadza się do odkładania wywołań na kolejkę JSową, dlatego jego zachowanie może być inne niż po uruchomieniu na JVM - w szczególności nigdy nie wykonuje się równolegle. 

Pod każdym ćwiczeniem znajdziesz podpowiedzi, które mogą pomóc, jeśli nie masz pomysłu na rozwiązanie zadania, a także link do pełnego rozwiązania danego ćwiczenia. 

## Spis treści

1. [Podstawy składni Scali](1_intro)
2. [Najważniejsze różnice pomiędzy Scalą i Javą](2_scala_vs_java)
3. [Mniej istotne różnice pomiędzy Scalą i Javą](2_5_scala_vs_java)
3. [Najciekawsze elementy biblioteki standardowej Scali](3_std_lib)
4. [Elementy zaawansowanego programowania funkcyjnego w Scali](4_functional_programming)
5. [Podsumowanie](5_summary)
