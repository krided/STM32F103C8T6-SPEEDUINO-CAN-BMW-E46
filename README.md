To tylko modyfikacja kodu od PAZI88 z jego PNP wersji. W moim wypadku dopiero ta wersja zaczeła działać z chinskim STM oraz Speeduino 0.4.3 - Mega2560. Pamiętajcie też o dzielniku napięcia/konwerterze poziomów logicznych na rx i tx z speeduino!. Ustawienia w tunerstudio: Enable i generic fixed list

# 📟 Serial3toBMWcan – Speeduino → BMW CAN Bridge (STM32)

Ten projekt to zaawansowany most UART → CAN oparty na STM32 (np. STM32F103), który umożliwia przesyłanie danych z ECU **Speeduino** do zegarów BMW E46/E39 przez magistralę **BMW CAN-Bus**.

---

## 🧠 Funkcje
- Wysyłanie RPM, TPS, temperatury silnika (CLT), prędkości (VSS), zużycia paliwa.
- Symulacja działania oryginalnego DME przez ID 0x545 (Multiplexed Diagnostic Frames).
- Odpowiedzi na zapytania DS2 przez CAN (np. 0x613, 0x615).
- Obsługa PWM wentylatora (wyjście na pin `PA15`).
- Obsługa sygnału A/C idle-up (`PC15`).
- Logika błędów i zabezpieczeń (np. przegrzanie, brak sygnału UART).
- Obsługa wiadomości z Speeduino przez UART (np. linia `"R"` i `"A"` z danych Runtime).

---

## ⚙️ Wymagania sprzętowe
- Płytka STM32 (np. STM32F103C8T6 – Blue Pill),
- Komunikacja UART (Serial3 → Speeduino),
- Magistrala CAN (np. przez sprzętowy kontroler STM32 CAN1),
- Zasilanie 5V/12V,
- Opcjonalnie: Arduino Mega (dla połączenia z ECU), wyświetlacz OLED I2C, linia VSS.

---

## 🛠️ Pinout
| Funkcja                | Pin STM32 | Opis                                          |
|------------------------|-----------|-----------------------------------------------|
| UART RX (z Speeduino)  | `PA10`    | Odbiór danych z ECU (Serial3 RX)              |
| UART TX (do Speeduino) | `PA9`    | Odpowiedzi DS2 (Serial3 TX)                   |
| CAN TX                 | `PA12`    | Transmisja danych CAN do zegarów              |
| CAN RX                 | `PA11`    | Odbiór z magistrali BMW                       |
| PWM Fan Output         | `PA15`    | Sterowanie wentylatorem (do ULN)              |
| A/C Idle-Up            | `PC15`    | Reakcja na żądanie A/C                        |
| SS0 (Arduino Mega)     | `PA1`     | Połączenie z pinem 53 Arduino Mega (opcjonalne) |
| SS1 (Arduino Mega)     | `PC15`    | Połączenie z pinem 49 Arduino Mega (AC idle up) |

---

## 💻 Kod źródłowy
Główna pętla odbiera dane przez UART, analizuje linie `"R"` i `"A"` z ECU i wysyła odpowiednie dane CAN na ID:
- **0x316** – RPM, VSS, Gear, Load,
- **0x329** – CLT, zużycie paliwa, TPS,
- **0x545** – multiplexowane dane: zapłon, EML, rozgrzanie, MIL.

Dodatkowo:
- Obsługa DS2: odpowiedzi na 0x613, 0x615,
- Sygnał prędkości (VSS) przetwarzany lokalnie lub z CAN,
- Próg temperatury i wentylator z modulacją PWM,
- Funkcja wykrywania braku danych UART.

---

## 🔧 Kompilacja
- PlatformIO lub Arduino IDE
- Framework: STM32Cube / Arduino STM32
- Płytka: `Generic STM32F103C series`
- Definicja `REV1_5` aktywuje funkcje zależne od wersji płytki (pinout v1.5)
- Biblioteki: `MFL.h`, `DS2.h` (lokalne / własne)

---

## 🧪 Testowanie
- Zegar BMW powinien aktywować się w ciągu 2 sekund po starcie STM32
- RPM, CLT, TPS reagują dynamicznie na dane z ECU
- Prędkość (VSS) może być symulowana
- Możliwość użycia INPA do odczytu DS2 z zegarów

---

## 📷 Efekty działania
- Obrotomierz, temperatura, wskazanie MIL/EML działają jak w oryginale
- Wentylator uruchamiany automatycznie zgodnie z temperaturą CLT
- Brak błędów zegarów (głośnik, check control)

---

## 📋 TODO / rozwój
- Obsługa tempomatu, DIS, komunikacji D-Bus
- Obsługa przycisków wielofunkcyjnych (MFL)
- Dodatkowe źródła danych (np. z czujników analogowych)
- Możliwość aktualizacji firmware przez UART

---

Projekt do użytku własnego – eksperymentalny. Wymaga podstawowej wiedzy z zakresu CAN, STM32 i Speeduino.
