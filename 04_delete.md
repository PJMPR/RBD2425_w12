# 🗑️ Usuwanie danych w MongoDB

MongoDB umożliwia usuwanie dokumentów z kolekcji za pomocą metod `deleteOne()` i `deleteMany()`. Obie metody wymagają wskazania filtra – czyli warunku, według którego wyszukiwane są dokumenty do usunięcia.

Przykłady poniżej odnoszą się do kolekcji `customers` i `products` ze skryptu `mongo_inserts.js`.

---

## 📌 deleteOne()

Usuwa **pierwszy** dokument pasujący do zapytania.

### 🔧 Usunięcie klienta o adresie e-mail „[kelly.jordan10@example.com](mailto:kelly.jordan10@example.com)”

```js
db.customers.deleteOne({ email: "kelly.jordan10@example.com" });
```

### 🔧 Usunięcie produktu o `source_id: 3`

```js
db.products.deleteOne({ source_id: 3 });
```

---

## 📌 deleteMany()

Usuwa **wszystkie** dokumenty pasujące do zapytania.

### 🔧 Usunięcie wszystkich klientów zarejestrowanych przed 2021 rokiem

```js
db.customers.deleteMany({ registration_date: { $lt: "2021-01-01" } });
```

### 🔧 Usunięcie wszystkich nieaktywnych produktów

```js
db.products.deleteMany({ active: false });
```

---

## ✅ Uwagi

* `deleteOne()` przyjmuje ten sam filtr co `find()` – usuwa pierwszy pasujący dokument.
* `deleteMany()` może usunąć wiele dokumentów – warto stosować ostrożnie.
