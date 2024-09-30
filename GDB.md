
Oto zestaw podstawowych funkcji i komend GDB, które są przydatne przy debugowaniu programów napisanych w NASM w x86_64:

1. **Uruchomienie programu**:
   - `run` (lub `r`): Uruchamia program. Możesz podać argumenty, np. `run arg1 arg2`.
   - `start`: Uruchamia program i zatrzymuje się na pierwszej instrukcji.

2. **Breakpoints**:
   - `break <label>` (lub `b <label>`): Ustawia punkt przerwania na etykiecie. Etykiety to odpowiedniki funkcji w NASM.
   - `break *<address>`: Ustawia punkt przerwania na konkretnym adresie pamięci, np. `break *0x4000d0`.
   - `info breakpoints`: Wyświetla listę punktów przerwania.

3. **Sterowanie wykonaniem**:
   - `nexti` (lub `ni`): Wykonuje kolejną instrukcję, przeskakując wywołania funkcji (użyteczne przy skokach w kodzie asemblerowym).
   - `stepi` (lub `si`): Wykonuje kolejną instrukcję, wchodząc w funkcje lub skoki.
   - `continue` (lub `c`): Kontynuuje wykonanie programu do następnego punktu przerwania lub końca programu.

4. **Wyświetlanie rejestrów**:
   - `info registers` (lub `i r`): Wyświetla aktualne wartości wszystkich rejestrów procesora.
   - `x/<n>i <address>`: Wyświetla instrukcje w pamięci na podanym adresie. `n` to liczba instrukcji do wyświetlenia.
   - `x/<n>x <address>`: Wyświetla zawartość pamięci na podanym adresie w formacie heksadecymalnym.

5. **Dostęp do pamięci**:
   - `x/<format> <address>`: Wyświetla zawartość pamięci pod adresem. Format może być różny:
     - `x` – heksadecymalnie,
     - `d` – dziesiętnie,
     - `i` – instrukcje maszynowe.
   - `display/i $rip`: Automatycznie pokazuje kolejne instrukcje wykonywane przez procesor, począwszy od wartości rejestru `rip` (instruction pointer).

6. **Śledzenie stosu**:
   - `info frame`: Wyświetla informacje o aktualnej ramce stosu.
   - `backtrace` (lub `bt`): Wyświetla stos wywołań (call stack).
   - `info stack`: Podobne do `backtrace`, wyświetla stos ale z bardziej szczegółowymi informacjami.

7. **Manipulacja rejestrami i pamięcią**:
   - `set $<register>=<value>`: Ustawia wartość rejestru, np. `set $rax=0x1234`.
   - `set {<type>} <address>=<value>`: Ustawia wartość pod adresem pamięci, np. `set {int} 0x6000d0=5`.

8. **Kroki w systemie**:
   - `stepi` (lub `si`): Wykonuje jedną instrukcję maszynową, co jest bardzo przydatne w asemblerze.
   - `finish`: Kończy wykonanie aktualnej funkcji i przechodzi do miejsca jej wywołania.

9. **Wyjście z GDB**:
   - `quit` (lub `q`): Zamyka sesję GDB.

________________________
### Debugowanie kodu asemblerowego

1. **Wyświetlanie instrukcji asemblerowych**:
   - `disassemble <function_name>`: Wyświetla dysasembler dla konkretnej funkcji. W przypadku NASM może być to etykieta (label) zamiast nazwy funkcji.
   - `layout asm`: Włącza widok TUI, który pokazuje okno terminala z instrukcjami asemblerowymi obok kodu źródłowego.
   - `layout split`: Pokazuje zarówno kod źródłowy, jak i instrukcje asemblerowe w tym samym oknie.
   - `tui enable` / `tui disable`: Włącza lub wyłącza interfejs TUI, który wyświetla kod asemblerowy i inne informacje w trybie graficznym.

2. **Obserwacja pamięci**:
   - `watch <variable>`: Ustawia punkt obserwacyjny (watchpoint) na zmiennej lub na adresie, np. `watch *(int *)0x6000d0`. GDB zatrzyma program, gdy zawartość tego miejsca w pamięci się zmieni.
   - `rwatch <variable>`: Ustawia watchpoint tylko na odczyt wartości, np. `rwatch *(char *)0x6010`.
   - `awatch <variable>`: Ustawia watchpoint na odczyt i zapis, np. `awatch *(int *)0x6020`.

3. **Śledzenie zmiennych i wartości**:
   - `display <expression>`: Automatycznie wyświetla wartość zmiennej lub rejestru po każdym kroku. Na przykład `display $rax` będzie pokazywać zawartość rejestru RAX po każdym kroku programu.
   - `undisplay <number>`: Usuwa wyświetlanie wartości zmiennej pod danym numerem (można znaleźć numery w `info display`).
   - `info display`: Wyświetla wszystkie zmienne, które są automatycznie śledzone (np. przez komendę `display`).

4. **Śledzenie przepływu skoków**:
   - `tbreak <address>`: Ustawia tymczasowy punkt przerwania, który zniknie po pierwszym jego trafieniu. Przydatne, gdy chcesz zatrzymać się na konkretnej instrukcji, ale nie chcesz trwale dodawać breakpointa.
   - `until <address>`: Kontynuuje wykonanie do momentu napotkania adresu lub etykiety.
   - `jump <address>`: Bezpośrednio przeskakuje do adresu lub etykiety, zmieniając `rip` i omijając część kodu.

5. **Analiza stosu (głębsza)**:
   - `frame <n>`: Przechodzi do ramki stosu o numerze `n`. Pozwala analizować wcześniejsze wywołania.
   - `info frame`: Szczegółowe informacje o bieżącej ramce stosu, w tym adres powrotu, offset, wartości rejestrów itp.
   - `info args`: Pokazuje argumenty funkcji (lub etykiety) dla bieżącej ramki stosu.
   - `info locals`: Wyświetla zmienne lokalne w bieżącej funkcji (lub ramce stosu).

6. **Analiza sekcji i symboli**:
   - `info files`: Wyświetla załadowane pliki binarne oraz ich sekcje (np. `.text`, `.data`).
   - `info symbol <address>`: Pokazuje symbol (np. etykietę) odpowiadający danemu adresowi, co pozwala zrozumieć, do której sekcji pamięci należy dana instrukcja.
   - `info functions`: Lista wszystkich funkcji i etykiet w programie.
   - `info variables`: Lista wszystkich zmiennych globalnych w programie.

7. **Zarządzanie punktami przerwania**:
   - `enable <breakpoint_number>`: Włącza wyłączony punkt przerwania.
   - `disable <breakpoint_number>`: Wyłącza punkt przerwania.
   - `delete <breakpoint_number>`: Usuwa punkt przerwania.

8. **Monitorowanie kodu w pętli**:
   - `while <expression> <commands>`: Możesz wykonywać zestaw poleceń GDB w pętli, np. monitorować zmienną do momentu, aż osiągnie określoną wartość.

9. **Śledzenie wartości rejestrów**:
   - `info all-registers`: Wyświetla wszystkie rejestry, w tym te specyficzne dla FPU i SIMD, co bywa przydatne, jeśli pracujesz z rozszerzeniami jak SSE lub AVX.
   - `info float`: Wyświetla rejestry koprocesora zmiennoprzecinkowego, co może być przydatne przy debugowaniu operacji matematycznych na liczbach zmiennoprzecinkowych.

10. **Zmienianie kodu w locie**:
    - `set $rip=<address>`: Przeskoczenie do innej instrukcji przez modyfikację rejestru instrukcji (program counter), np. `set $rip=0x400080`.
    - `set <memory_address> = <value>`: Bezpośrednia modyfikacja zawartości pamięci, np. `set *(char*)0x6010 = 42`.

### Debugowanie równoległych wątków (jeśli Twój kod NASM współpracuje z wątkami):

1. **Zarządzanie wątkami**:
   - `info threads`: Wyświetla listę wszystkich uruchomionych wątków.
   - `thread <thread_number>`: Przełącza na konkretny wątek.
   - `thread apply all <command>`: Wykonuje dane polecenie dla wszystkich wątków (np. `thread apply all backtrace`).

### Optymalizacja wydajności debugowania:
- **Zapis sesji debugowania**:
   - `set logging on`: Zapisuje wszystkie polecenia i wyniki do pliku `gdb.txt` (lub innego, jeśli ustawisz odpowiednią nazwę).
   - `set logging file <filename>`: Ustawia nazwę pliku do logowania.
   - `set logging off`: Wyłącza logowanie.
  
Dzięki tym funkcjom masz bardziej szczegółową kontrolę nad debugowaniem, śledzeniem i manipulowaniem kodem asemblerowym w GDB.