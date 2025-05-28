# ➕ Dodawanie danych w MongoDB

Dodawanie danych do kolekcji w MongoDB odbywa się przy pomocy metod `insertOne()` oraz `insertMany()`. W tym dokumencie przedstawiamy praktyczne przykłady tych operacji, oparte na kolekcjach `customers` i `products` ze skryptu `mongo_inserts.js`.

## 📌 insertOne()

Metoda `insertOne()` służy do dodania pojedynczego dokumentu do kolekcji.

### 🔧 Przykład – dodanie jednego klienta:

```js
// Dodanie klienta do kolekcji 'customers'
db.customers.insertOne({
  source_id: 21,
  first_name: "Anna",
  last_name: "Nowak",
  email: "anna.nowak21@example.com",
  registration_date: "2025-05-27 10:00:00",
  address: {
    street: "123 Nowa Ulica",
    city: "Warszawa",
    zip_code: "00-001",
    country: "Polska"
  },
  orders: []
});
```

## 📌 insertMany()

Metoda `insertMany()` umożliwia dodanie wielu dokumentów jednocześnie.

### 🔧 Przykład – dodanie wielu produktów:

```js
// Dodanie produktów do kolekcji 'products'
db.products.insertMany([
  {
    source_id: 21,
    name: "Tablet graficzny",
    description: "Tablet graficzny do rysowania z rysikiem.",
    price: 199.99,
    active: true,
    inventory: {
      quantity: 12,
      last_updated: "2025-05-28T00:00:00"
    }
  },
  {
    source_id: 22,
    name: "Powerbank 10000mAh",
    description: "Kompaktowy powerbank z dwoma portami USB.",
    price: 89.99,
    active: true,
    inventory: {
      quantity: 40,
      last_updated: "2025-05-28T00:00:00"
    }
  }
]);
```

## ⚙️ Opcjonalne parametry konfiguracyjne

### 📥 insertOne(dokument, \[options])

| Opcja                      | Typ       | Opis                                                               |
| -------------------------- | --------- | ------------------------------------------------------------------ |
| `bypassDocumentValidation` | `boolean` | Pomija walidację dokumentu według schematu (jeśli istnieje)        |
| `writeConcern`             | `object`  | Określa poziom potwierdzenia zapisu, np. `{ w: 1 }`, `{ j: true }` |

**Przykład:**

```js
db.customers.insertOne(
  { first_name: "Tomasz", last_name: "Kowalski" },
  { bypassDocumentValidation: true }
);
```

### 📥 insertMany(dokumenty, \[options])

| Opcja                      | Typ       | Opis                                                                         |
| -------------------------- | --------- | ---------------------------------------------------------------------------- |
| `ordered`                  | `boolean` | Domyślnie `true`. Gdy `false`, MongoDB kontynuuje operacje nawet po błędach. |
| `bypassDocumentValidation` | `boolean` | Jak wyżej.                                                                   |
| `writeConcern`             | `object`  | Jak wyżej.                                                                   |

**Przykład:**

```js
db.products.insertMany([
  { name: "Item A", price: 10 },
  { name: "Item B", price: "not-a-number" } // potencjalny błąd
], {
  ordered: false
});
```

## 🔒 writeConcern – poziomy bezpieczeństwa zapisu

Parametr `writeConcern` kontroluje sposób potwierdzania operacji zapisu. Można za jego pomocą określić, na ilu węzłach (jeśli używamy replikacji) lub w jakim stopniu MongoDB powinien zagwarantować, że dane zostały zapisane.

| Składnik   | Typ                     | Znaczenie                                                                     |
| ---------- | ----------------------- | ----------------------------------------------------------------------------- |
| `w`        | liczba lub `"majority"` | Ilu członków klastra musi potwierdzić zapis (np. `1`, `2`, `"majority"`)      |
| `j`        | boolean                 | Czy zapis musi zostać uwzględniony w dzienniku (journal) przed potwierdzeniem |
| `wtimeout` | liczba (ms)             | Ile maksymalnie milisekund MongoDB ma czekać na spełnienie warunku `w`        |

> 💡 **Uwaga:**
>
> * **`journal`** (dziennik) to mechanizm MongoDB zapewniający trwałość operacji – zapisane dane są przechowywane na dysku w specjalnym pliku przed potwierdzeniem zapisu. Dzięki temu można odzyskać dane po awarii.
> * **`majority`** oznacza, że zapis musi zostać zatwierdzony przez większość członków klastra replikacji (więcej niż połowa). Zapewnia to większą spójność danych w środowiskach rozproszonych.

### 📌 Przykłady:

**Minimalne potwierdzenie (domyślnie)**

```js
db.customers.insertOne(
  { first_name: "Tomasz", last_name: "Kowalski" },
  { writeConcern: { w: 1 } }
);
```

**Potwierdzenie przez większość węzłów**

```js
db.customers.insertOne(
  { first_name: "Jan", last_name: "Kowal" },
  { writeConcern: { w: "majority" } }
);
```

**Wymaganie zapisu do dziennika (journal)**

```js
db.customers.insertOne(
  { first_name: "Iga", last_name: "Nowicka" },
  { writeConcern: { j: true } }
);
```

**Ustawienie limitu czasu oczekiwania**

```js
db.customers.insertOne(
  { first_name: "Ewa", last_name: "Szymańska" },
  { writeConcern: { w: 2, wtimeout: 2000 } }
);
```

---

## ✅ Uwagi

* `insertOne()` zwraca potwierdzenie wstawienia jednego dokumentu (`acknowledged: true` i `insertedId`).
* `insertMany()` zwraca tablicę z identyfikatorami wstawionych dokumentów.
* Warto zadbać o unikalność pól takich jak `source_id`, jeżeli będą używane jako identyfikatory logiczne.

W kolejnych sekcjach repozytorium znajdziesz analogiczne przykłady dla operacji odczytu, aktualizacji oraz usuwania danych.
