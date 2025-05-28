# 🔍 Wyszukiwanie danych w MongoDB

MongoDB umożliwia wyszukiwanie dokumentów w kolekcji za pomocą metody `find()`. W połączeniu z operatorami porównania i logicznymi daje to ogromną elastyczność w konstruowaniu zapytań.

Przykłady poniżej odnoszą się do kolekcji `customers` ze skryptu `mongo_inserts.js`.

---

## 📌 Podstawowe zapytanie – `find()`

### 🔎 Znajdź wszystkich klientów

```js
db.customers.find();
```

### 🔎 Znajdź klienta o konkretnym adresie e-mail

```js
db.customers.find({ email: "natalie.gray7@example.com" });
```

---

## ⚖️ Operatory porównania

MongoDB udostępnia następujące operatory porównania:

* `$eq` – równe
* `$ne` – różne
* `$gt` – większe niż
* `$gte` – większe lub równe
* `$lt` – mniejsze niż
* `$lte` – mniejsze lub równe

### 🔎 Klienci zarejestrowani po 2022 roku

```js
db.customers.find({ registration_date: { $gt: "2022-01-01" } });
```

### 🔎 Klienci zarejestrowani do końca 2020 roku

```js
db.customers.find({ registration_date: { $lte: "2020-12-31" } });
```

### 🔎 Klienci spoza Polski

```js
db.customers.find({ "address.country": { $ne: "Polska" } });
```

---

## 🧠 Operatory logiczne

MongoDB obsługuje następujące operatory logiczne:

* `$and`
* `$or`
* `$not`
* `$nor`

### 🔎 Klienci zarejestrowani w 2020 roku i pochodzący z Etiopii

```js
db.customers.find({
  $and: [
    { registration_date: { $gte: "2020-01-01", $lt: "2021-01-01" } },
    { "address.country": "Ethiopia" }
  ]
});
```

### 🔎 Klienci z Etiopii lub Polski

```js
db.customers.find({
  $or: [
    { "address.country": "Ethiopia" },
    { "address.country": "Polska" }
  ]
});
```

### 🔎 Klienci, którzy NIE są z Austrii

```js
db.customers.find({
  "address.country": { $not: { $eq: "Austria" } }
});
```

### 🔎 Klienci, którzy NIE są z Polski ANI z Francji

```js
db.customers.find({
  $nor: [
    { "address.country": "Polska" },
    { "address.country": "France" }
  ]
});
```

---

### 🔎 Klienci zarejestrowani po 2021 roku, którzy mają co najmniej jedno zamówienie i nie pochodzą z Francji ani Austrii

```js
db.customers.find({
  $and: [
    { registration_date: { $gt: "2021-12-31" } },
    { orders: { $ne: [] } },
    {
      $nor: [
        { "address.country": "France" },
        { "address.country": "Austria" }
      ]
    }
  ]
});
```

## ✅ Uwagi

* `find()` domyślnie zwraca maksymalnie 20 dokumentów w shellu. Użyj `.toArray()` lub `.pretty()` dla pełnego wyniku.
* Użycie operatorów logicznych i porównawczych pozwala tworzyć bardzo precyzyjne zapytania.
* Kwerendy mogą być zagnieżdżane i łączone.

W kolejnych sekcjach repozytorium znajdziesz przykłady aktualizacji i usuwania danych.
