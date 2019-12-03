# Szablony wydruków do xSale

Szablony edytuje sie: https://community.jaspersoft.com/project/jaspersoft-studio

Gotowy szablon należy wczytać i używać w xSale zgodnie z dokumentacją: https://xsale.ai/docs/xsale-panel/konfiguracja-xsale/

# Pierwsze uruchomienie wzoru
Może zdażyć się sytuacja, że pierwsze uruchomienie wzoru, podczas podglądu, będzie generować błąd związany z `DataSet` lub `DataSource` - w takiej sytuacji trzeba przypiąć istniejące źródło danych lub wygenerować nowe źródło danych --> patrz poniżej 'Ładowanie danych z json' oraz `Kolekcja z json`

# Kompilacja
Program tworzy i pracuje na formacie `.jrxml`, aby można było go użyć do wygenerowania raportu trzeba go przekonwertować do formatu `.jasper`
![alt text](https://intranet.futuriti.pl/wp-content/uploads/2019/12/Konvertowanie-do-jasper-1.jpg)

# Ładowanie danych z json
Przepnij zakładkę na Repository Explorer

![alt text](https://intranet.futuriti.pl/wp-content/uploads/2019/12/ladowanie-danych1.jpg)

Prawym –> data adapters –> craete new –> JSON File –> File/URL wskaż miejsce wygenerowanego szablonu danych z xsale –> zaznacz Create data source using this expression –> nacisnij Finish
Wejdź do okna dialogowego Dataset and Query Dialog

![alt text](https://intranet.futuriti.pl/wp-content/uploads/2019/12/ladowanie-danych2.jpg)

W lewym górnym rogu znajdzie strzałkę która po naciśnięciu rozwinie menu, wybierz Data Adapter, który został wcześniej stworzony

Po lewej stronie zostanie wygenerowana lista pól, które pochodzą z pliku json

Przycisk Read Fields stworzy powiązania automatycznie wszystkie pola z pliku json na pola wykorzystywane z programnie. Zważywszy na wielkość pliku dla przyszłej wygody pracy odradza się z jego używania

Aby dodać pojedyncze mapowanie naciśnij prawym klawiszem myszy na pole –> Add node as field

Najważniejsze cechy pola to:
  * Field Name – która będzie używana w każdym oknie dialogowym w Jasper Studio
  * Class Type – typ klasy z języka Java. Najczęściej używana i ustawiania default’owo wartość to java.lang.String, zaleca się jej używanie do wszystkich pól które chcemy „wydrukować do pdf” niezależnie czy dane pole będzie reprezentować liczbę całkowitą, czy zmienna przeciekową. Inne typy klas używane są do konkretnych kontrolek, czy ekspresji
  * Field Expression – dokładna ścieżka do której dane pole (z poziomu Jasper Studio) odwołuje się do pola w json
  
Gdy json obiekt jest złożony to: odwołujemy się po kropce ```.``` np: ```objectData.Id```

![alt text](https://intranet.futuriti.pl/wp-content/uploads/2019/12/ladowanie-danych3.jpg)

Gdy dane pole ma odwołać się do elementu listy np: DataBinary to ```objectData.BinaryData[0].FileContentURL``` (wszelkie kolekcje klasycznie od liczone od ‚ 0 ‚)

Po zapisaniu i wyłączeniu okna dialogowego Data Adapter w lewym dolnym roku w zakładce Outline w rozwijanej strukturze drzewiastej w Fields pojawią się wszystkie wygenerowane pola

Aby użyć je w projekcie wystarczy przeciągnąć je do konkretnego miejsca na wizualizacji

# Eksprecje

Każda kontrolka posiada ekspresję, czyli ścieżkę mapowania do danych. Ekspresje można ustawić w wielu miejsach, najprościej 2 razy szybko lewym myszy na kontrolkę i w Propeties (prawy dolny róg ekranu) będzie kilka ogólnych zakładek dla każdej kontrolki, jak i zakładka posiadająca tylko i wyłącznie ustawienia danej kontrolki: Apearance, borders…., Image, ikona okna dialogowego z kredką uruchamia edytor ekspresji gdzie znajdziesz listę pól, ich typy, oraz dodatkowe metody

![alt text](https://intranet.futuriti.pl/wp-content/uploads/2019/12/ekspresja1-1.jpg)

![alt text](https://intranet.futuriti.pl/wp-content/uploads/2019/12/ekspresja2-1.jpg)

**Zdjęcia**

* Jeżeli zdjęcie jest w postaci URL to wystarczy dodać ekspresję kontrolki do pola $F{objectData.BinaryData.FileContentURL}, typ ```java.lang.String```
* Jeżeli zdjęcie jest w ```base64``` to musimy polączyć kod java z ekspresją do pola new ```java.io.ByteArrayInputStream(java.util.Base64.getDecoder().decode($F{organizationImageBase64}))```,typ ```java.lang.String```

**Ciąg znaków**

* łączenie pola z tekstem ```$F{objectData.Price.Value} + " zł"```, typ ```java.lang.String```

# Propeties

Posiada zakładkę Advanced w której często jest więcej opcji niż w pozostałych !!

# Sekcje na stronie
http://jsystems.pl/blog/artykul.html?id=409 – każdy dokument posiada sekcję, każda sekcja inaczej się zachowuje na stronie w zależności od mapowanych w niej pól

Sekcję, kontrolki – nie mogą zachodzić na siebie

Kontrolka – nie może być większa niż sekcja

Suma wysokości sekcji nie może być większa niż strona. Jasper w niektórych sytuacjach zależnych od ustawień w Properties może nie renderować kontrolek, np: gdy sekcja posiada tabelę która rozszerza się dynamicznie, a strona posiada ustawienie sztywne wysokości

# Kolekcja z json

Przeciągając kontrolkę Table uruchomi się Wizzard

Stwórz nowy Data Set lub użyj istniejącego

Tworząc nowy poprosi Cię o wybór Data Source, z którego wybierz kolekcję danych

Ekspresja: ```Use a JRDatasource expression```

```((net.sf.jasperreports.engine.data.JsonDataSource)$P{REPORT_DATA_SOURCE}).subDataSource("objectData.AttributeList")```, typ ```java.lang.String```

2 razy lewym myszy – wchodzisz do tabeli gdzie możesz organizować ją posiadając osobne okno edycji

![alt text](https://intranet.futuriti.pl/wp-content/uploads/2019/12/tabela1.jpg)

Okno dialogowe Dataset and Query Dialog posiada tylko i wyłącznie ustawienia należące do tabeli, zakładka Outline – również zawęziła listę, sekcji, pól itp do tabeli

# Source i Preview

Pod oknem edycji znajdują się zakładki Source oraz Preview, gdzie możesz podglądnąć wygenerowany kod xml oraz w locie przekompilować i pokazać szybki podgląd dokumentu na podstawie podpiętego Data Source

Częsty podgląd jest pierwszym śitem wyłapywania większości błędów
