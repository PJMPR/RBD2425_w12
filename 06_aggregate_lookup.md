# 🔗 Agregacja z użyciem `$lookup` w MongoDB

Operator `$lookup` w MongoDB służy do łączenia danych z dwóch kolekcji – jest odpowiednikiem **JOIN** w relacyjnych bazach danych. Wykorzystywany jest w potoku agregacyjnym do pobierania i osadzania powiązanych dokumentów.

W przykładach poniżej łączymy kolekcje `customers` i `products` poprzez zamówienia (`orders.items.product_source_id`).

---

## 🔍 `$lookup` – podstawowe połączenie

### 🔧 Dołączenie informacji o produktach do każdego zamówienia klienta

```js
db.customers.aggregate([
  { $unwind: "$orders" },
  { $unwind: "$orders.items" },
  {
    $lookup: {
      from: "products",
      localField: "orders.items.product_source_id",
      foreignField: "source_id",
      as: "product_info"
    }
  },
  { $unwind: "$product_info" },
  {
    $project: {
      _id: 0,
      customer: { $concat: ["$first_name", " ", "$last_name"] },
      product: "$product_info.name",
      quantity: "$orders.items.quantity",
      price: "$product_info.price"
    }
  }
]);
```

**Wynik:** Lista dokumentów zawierających dane klienta, nazwę produktu, ilość oraz cenę jednostkową złączoną przez `$lookup` z kolekcji `products`.

---

## 📘 Składnia `$lookup`

Podstawowa struktura operatora `$lookup` wygląda następująco:

| Parametr       | Typ      | Opis                                                                       |
| -------------- | -------- | -------------------------------------------------------------------------- |
| `from`         | `string` | Nazwa kolekcji, z którą następuje połączenie.                              |
| `localField`   | `string` | Pole w bieżącym dokumencie (źródłowej kolekcji), które będzie porównywane. |
| `foreignField` | `string` | Pole w kolekcji docelowej (`from`), które ma pasować do `localField`.      |
| `as`           | `string` | Nazwa pola, w którym zostanie umieszczona wynikowa tablica dokumentów.     |

### 🔁 Przykład struktury

```js
{
  $lookup: {
    from: "products",
    localField: "orders.items.product_source_id",
    foreignField: "source_id",
    as: "product_info"
  }
}
```

**Opis:** Do każdego dokumentu klienta zostaną dołączone dopasowane dokumenty produktów, w których `source_id` pasuje do `product_source_id` w jego zamówieniach. Wynikiem jest tablica `product_info` w każdym dokumencie.

## ✅ Uwagi

* `$lookup` umożliwia łączenie danych na podstawie zgodności pól z dwóch kolekcji.
* Wymaga indeksów dla wydajnego działania przy dużej liczbie dokumentów.
* Można go łączyć z innymi etapami jak `$unwind`, `$project`, `$match` itd.
* Jeśli nie znajdzie dopasowania, `as` będzie pustą tablicą – chyba że użyto `$unwind` z `preserveNullAndEmptyArrays`.

> 💡 **Uwaga:**
> `$lookup` działa najlepiej przy dobrze zaprojektowanych schematach danych, gdzie relacje są jednoznaczne i wydajne do przetworzenia w potoku agregacyjnym.
