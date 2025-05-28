# 📘 Operacje CRUD w MongoDB – Wprowadzenie

Ten projekt stanowi materiał pomocniczy do wykładu na temat operacji CRUD w bazie danych MongoDB, z przykładami z życia wziętymi oraz praktycznymi zapytaniami.

## 🎯 Cel wykładu

Celem wykładu jest zapoznanie uczestników z podstawowymi operacjami na dokumentach w MongoDB – od dodawania danych, poprzez ich wyszukiwanie, aktualizację, aż po usuwanie. Omówione zostaną również bardziej zaawansowane operatory aktualizacji, takie jak `$set`, `$inc`, `$push` i `$pull`.

## 🗂️ Zakres tematyczny

W trakcie wykładu zostaną omówione następujące zagadnienia:

### ➕ 1. Dodawanie danych

* `insertOne()` – dodawanie pojedynczego dokumentu
* `insertMany()` – dodawanie wielu dokumentów jednocześnie

### 🔍 2. Wyszukiwanie danych

* `find()` – podstawowe zapytania wyszukujące
* Operatory porównania: `$eq`, `$ne`, `$gt`, `$gte`, `$lt`, `$lte`
* Operatory logiczne: `$and`, `$or`, `$not`, `$nor`

### ✏️ 3. Aktualizacja danych

* `updateOne()` – aktualizacja pierwszego pasującego dokumentu
* `updateMany()` – aktualizacja wielu dokumentów
* `replaceOne()` – zamiana całego dokumentu na nowy

### 🗑️ 4. Usuwanie danych

* `deleteOne()` – usuwanie pierwszego pasującego dokumentu
* `deleteMany()` – usuwanie wielu dokumentów

### 🛠️ 5. Zaawansowane operatory aktualizacji

* `$set` – ustawianie nowych wartości pól
* `$inc` – inkrementacja (lub dekrementacja) wartości liczbowych
* `$push` – dodawanie elementu do tablicy
* `$pull` – usuwanie elementów z tablicy

## 📁 Struktura repozytorium

Repozytorium zawiera:

* `mongo_inserts.js` – przykładowy skrypt do wstawiania danych (klienci i produkty)
* pliki `.md` z przykładami dla poszczególnych operacji (dodawanie, aktualizacja, wyszukiwanie, usuwanie)

## 🧰 Wymagania wstępne

* Podstawowa znajomość MongoDB
* Zainstalowany `mongosh` lub dostęp do MongoDB Atlas
* Node.js (opcjonalnie, jeśli dane będą wczytywane skryptem)

## 📄 Licencja

Materiały dostępne na licencji MIT. Można je swobodnie wykorzystywać do nauki i dydaktyki.
