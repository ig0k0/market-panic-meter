# Jak powtórzyć testy

Cały backtest robi jeden skrypt: [`scripts/panic_meter_tests.pine`](../scripts/panic_meter_tests.pine). Liczy wszystkie warianty naraz i pokazuje je w tabeli na wykresie, więc nie trzeba przełączać ustawień ani używać Strategy Testera. Zajmuje to około 5 minut.

## 1. Wykres

- Symbol: **SP:SPXTR** (S&P 500 z reinwestowanymi dywidendami). Alternatywa: **AMEX:SPY** z włączonym **ADJ** w prawym dolnym rogu wykresu.
- Interwał: **D** (1 dzień). Działa też na 1W i 1M, ale testy w repozytorium są robione na 1D.
- Na czas testu zostaw na wykresie tylko ten skrypt. Plan Basic ma limit czasu wykonania (0,5 s), a inne wskaźniki go zjadają.

## 2. Dodanie skryptu

1. Na dole ekranu kliknij **Pine Editor**.
2. Kliknij nazwę skryptu w lewym górnym rogu edytora → **Utwórz nowy** → **Wskaźnik**.
3. Zaznacz cały przykładowy kod, wklej zawartość `panic_meter_tests.pine` i kliknij **Zapisz**, np. jako „PM Testy”.
4. Kliknij **Dodaj do wykresu**. Tabela pojawi się w prawym górnym rogu.

## 3. Przełączanie tabel

Kliknij koło zębate przy „PM Testy”, wybierz zakładkę **Argumenty**, a w niej pole **Pokaż**:

| Pokaż | Co liczy |
| --- | --- |
| Testy A i C | 9 wariantów „kupuj panikę” + „sprzedaj w euforii” + „kup i trzymaj” |
| Test B (DCA) | zwykłe DCA + 6 wariantów z rezerwą na panikę |
| Wszystkie tabele (wolniej) | oba zestawy naraz; na planie Basic może przekroczyć limit czasu |

## 4. Jak czytać tabelę

- **Wynik**: zysk z 10 000 $ w danym okresie.
- **W rynku**: jaki procent czasu wariant trzymał akcje.
- **Rocznie w rynku**: średni roczny zwrot liczony tylko za czas trzymania akcji. To najważniejsza kolumna testu A, bo porównuje strategię, która często trzyma gotówkę, z rynkiem.
- **Wejścia (zysk.)**: liczba wejść oraz zyskowne transakcje na tle zamkniętych. Poniżej ~8 wejść wynik jest mało wiarygodny.
- **vs DCA** (test B): o ile wariant z rezerwą jest lepszy lub gorszy od zwykłego DCA.
- **Żółte pole**: lepiej niż punkt odniesienia. W kolumnie „Rocznie w rynku” podświetla się tylko przewaga powyżej 1 punktu procentowego, bo mniejsze różnice to szum.

**Wiarygodna przewaga** to taka, która wychodzi w **obu dekadach** (kolumny 2006–2016 i 2016–dziś) i przy sąsiednich wartościach progów, a nie tylko przy jednym ustawieniu.

## 5. Co warto zmienić

W **Argumentach** możesz zmienić:
- **Początek testu** i **Datę podziału**, np. żeby sprawdzić tylko lata po 2020;
- **progi sygnału** (sekcja ②), żeby sprawdzić, czy przewaga przetrwa zmianę o ±20%;
- **długości trzymania** w teście A (domyślnie 63, 252 i 756 sesji);
- **wpłatę miesięczną** i **odstęp między transzami** w teście B;
- **prowizję**.

Wszystkie warianty przeliczą się wtedy jednocześnie.

## Ograniczenia skryptu

- Gotówka nie dostaje odsetek.
- Nie ma listy pojedynczych transakcji. Jeśli jej potrzebujesz, ten sam sygnał można przenieść do skryptu `strategy()` i użyć Strategy Testera.
- Sygnał zawsze pochodzi z rynku USA (VIX, szerokość S&P 500, Put/Call CBOE). Na innym instrumencie tabela pokazuje, jak ten instrument zachowywał się po sygnałach z USA.
