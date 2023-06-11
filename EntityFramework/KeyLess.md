
### Owned entity type
Если у dbSet сущности нет свойства ключа int Id - создать migration не получится.
Необходимо определить эту сущность как KeyLess либо как "собственный тип сущности" (*owned entity type*). Сущность, содержащая этот тип - является её *владельцем* (*owner*).

Чтобы определить *собственный тип* необходимо переопределть метод `OnModelCreating` или добавить к типу аттрибут `OwnedAttribute`

Пример:
```cs
 public class AssetTransaction
    {
        public int Id { get; set; }
        public Account Account { get; set; }
        public bool IsPurchase { get; set; }
        public Stock Stock { get; set; }
        public int Shares { get; set; }
        public DateTime DateProcessed { get; set; }
    }
```

```cs
    public class Stock
    {
        public string Symbol { get; set; }
        public double PricePerShare { get; set; }
    }
```

Класс `Stok` не имеет *Id* и является собственным типом класса `AssetTransaction`.
При создании контекста данных переопределям метод  `OnModelCreating`:
```cs
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<AssetTransaction>().OwnsOne(a => a.Stock);

            base.OnModelCreating(modelBuilder);
        }
```

Теперь `Stok` является частью `AssetTransaction`.