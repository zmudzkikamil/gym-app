# Dziennik treningowy (Android)

Aplikacja do śledzenia siłowni, biegania i wagi. HTML/JS w `www/` opakowany w Capacitor 8.

Dane zapisują się w pliku `dziennik.json` w pamięci aplikacji (`Directory.Data`), a raz dziennie powstaje też kopia `dziennik.bak.json`. Wyczyszczenie danych Chrome ich nie usuwa. Znikną tylko po odinstalowaniu aplikacji albo po „Wyczyść dane” w ustawieniach systemu. `allowBackup="true"`, więc przy włączonej kopii zapasowej Androida pliki trafiają też na Dysk Google.

## Budowanie przez GitHub Actions (bez Android Studio)

1. Utwórz **prywatne** repo i wypchnij ten folder (bez `node_modules`).
2. W repo: Settings → Secrets and variables → Actions. Dodaj 4 sekrety z pliku `github-secrets.txt`: `KEYSTORE_BASE64`, `KEYSTORE_PASSWORD`, `KEY_ALIAS`, `KEY_PASSWORD`.
3. Każdy push na `main` buduje podpisane APK. Plik pobierzesz z zakładki Actions → ostatni run → Artifacts (plik zip z APK w środku).
4. Na telefonie otwórz APK i zezwól na instalację z tego źródła.

Bez sekretów workflow zbuduje APK testowe (debug), ale każde takie APK ma inny podpis. Aktualizacja wymaga wtedy odinstalowania apki, a to **usuwa dane**. Dlatego używaj podpisanej wersji.

## Budowanie lokalnie

Wymagania: Node 22+, JDK 21, Android Studio (SDK 36).

```bash
npm install
npx cap sync android
npx cap open android      # Android Studio → Build → Build APK(s)
```

Podpisane APK z linii komend: utwórz `android/keystore.properties` (wzór w `github-secrets.txt`), potem:

```bash
cd android && ./gradlew assembleRelease
```

## Zmiany w aplikacji

Edytujesz `www/index.html`, potem `npx cap sync android` albo po prostu push, a Actions zbuduje nową wersję. `versionCode` = numer runu, więc nowe APK instaluje się na starym **bez utraty danych**, pod warunkiem że jest podpisane tym samym kluczem.

## Ważne

- **Nie zgub pliku `dziennik.jks` ani hasła.** Bez nich nie zbudujesz aktualizacji, którą da się zainstalować na istniejącą apkę.
- Plan treningowy (ćwiczenia, serie, zakresy) jest w stałej `PLAN` na początku skryptu w `www/index.html`.
- Kopia ręczna: zakładka Tydzień → „Zapisz kopię” otwiera menu udostępniania (np. Dysk Google). „Wczytaj kopię” przywraca dane z pliku JSON.
