# 📊 Agregacja danych w MongoDB

MongoDB oferuje zaawansowany mechanizm agregacji, który pozwala na przetwarzanie i analizę danych za pomocą potoku (pipeline). Potok składa się z wielu etapów, z których każdy przekształca dokumenty przepływające przez kolejne fazy.

Przykłady poniżej odnoszą się do kolekcji `customers` i `products` ze skryptu `mongo_inserts.js`.

---

## 🔍 `$match` – filtrowanie dokumentów

Filtruje dokumenty na podstawie warunków – działa podobnie jak `find()`, ale w potoku agregacyjnym.

### 🔧 Klienci zarejestrowani po 2023 roku

```js
db.customers.aggregate([
  { $match: { registration_date: { $gt: "2023-01-01" } } }
]);
```

**Wynik:** Lista klientów, których pole `registration_date` ma wartość późniejszą niż 1 stycznia 2023. Każdy dokument zawiera pełne dane klienta.

---

## 📦 `$group` – grupowanie i agregowanie

Pozwala zgrupować dane według wskazanego pola i wykonać agregacje (np. sumowanie, liczenie, średnia).

### 🔧 Liczba klientów z podziałem na kraje

```js
db.customers.aggregate([
  { $group: { _id: "$address.country", total: { $sum: 1 } } }
]);
```

**Wynik:** Dokumenty, z których każdy reprezentuje jeden kraj (`_id`) oraz liczbę klientów (`total`) z tego kraju.

### 🔧 Średnia cena produktów

```js
db.products.aggregate([
  { $group: { _id: null, avgPrice: { $avg: "$price" } } }
]);
```

**Wynik:** Jeden dokument zawierający pole `avgPrice` z obliczoną średnią ceną wszystkich produktów.

---

## 🧰 `$project` – wybieranie i przekształcanie pól

Umożliwia określenie, które pola mają zostać zwrócone i jak mają wyglądać (np. przekształcenia nazw, wartości).

### 🔧 Wyświetlenie imienia, nazwiska i kraju klienta

```js
db.customers.aggregate([
  {
    $project: {
      _id: 0,
      fullName: { $concat: ["$first_name", " ", "$last_name"] },
      country: "$address.country"
    }
  }
]);
```

**Wynik:** Lista dokumentów zawierających tylko `fullName` (zbudowane z `first_name` i `last_name`) oraz `country` – bez `_id`.

---

## 🔄 `$unwind` – rozpakowywanie tablic

Rozdziela dokument z tablicą na wiele dokumentów – po jednym na każdy element tablicy.

### 🔧 Rozpakowanie zamówień klientów

```js
db.customers.aggregate([
  { $unwind: "$orders" },
  { $project: {
      _id: 0,
      email: 1,
      order_id: "$orders.source_id",
      status: "$orders.status"
    }
  }
]);
```

**Wynik:** Każdy klient z tablicą `orders` zostaje rozdzielony na wiele dokumentów – po jednym dla każdego zamówienia, zawierających `email`, `order_id` i `status`.

---

> 💡 **Uwaga:**
>
> W potokach agregacyjnych MongoDB operator `$` przed nazwą pola (np. `$first_name`, `$price`) oznacza **odwołanie do wartości pola** w dokumencie.
>
> * Przykład: `$price` oznacza „wartość pola `price` z bieżącego dokumentu”.
> * W kontekście operatorów takich jak `$group`, `$project` czy `$unwind`, `$` służy do manipulowania zawartością dokumentów wejściowych.
>
> Gdybyśmy pominęli `$`, MongoDB potraktowałby nazwę literalnie jako ciąg znaków, a nie jako odniesienie do pola.

## 🧪 Zaawansowane przykłady pipeline

### 🔧 Liczba zamówień z podziałem na status

```js
db.customers.aggregate([
  { $unwind: "$orders" },
  { $group: {
      _id: "$orders.status",
      totalOrders: { $sum: 1 }
  }}
]);
```

**Wynik:** Zestawienie liczby zamówień (`totalOrders`) według ich statusu (`_id`).

### 🔧 Średnia liczba pozycji w zamówieniach dostarczonych klientom z Polski

```js
db.customers.aggregate([
  { $match: { "address.country": "Polska" } },
  { $unwind: "$orders" },
  { $match: { "orders.status": "delivered" } },
  { $project: {
      itemCount: { $size: "$orders.items" }
  }},
  { $group: {
      _id: null,
      avgItems: { $avg: "$itemCount" }
  }}
]);
```

**Wynik:** Jeden dokument z polem `avgItems` – średnia liczba pozycji w zamówieniach dostarczonych klientom z Polski.

### 🔧 Lista klientów z liczbą zrealizowanych zamówień

```js
db.customers.aggregate([
  { $unwind: "$orders" },
  { $match: { "orders.status": { $in: ["shipped", "delivered"] } } },
  { $group: {
      _id: "$email",
      completedOrders: { $sum: 1 }
  }}
]);
```

**Wynik:** Lista klientów (`_id` jako e-mail) wraz z liczbą ich zamówień ze statusem „shipped” lub „delivered”.

## ✅ Uwagi

* Etapy agregacji wykonują się w kolejności – każdy operuje na wynikach poprzedniego.
* Można łączyć wiele etapów w jednym pipeline.
* Wydajne agregacje często wykorzystują indeksy w pierwszym etapie `$match`.

Więcej zaawansowanych przykładów można uzyskać przez połączenie `$match`, `$group`, `$project` i `$unwind` w jednym zapytaniu.
