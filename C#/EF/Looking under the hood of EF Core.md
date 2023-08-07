
### 1. Modeling the database
Первым дело Ef создае базу данных, на основе созданных DbSet и параметров в метода `OnConfiguring`. Полученная модель испоьзуется в дальнещем EF для доступа к это БД.
Моделирование базы данных:
- Просмотр `<DbSet>`  сво-тв для создания таблиц.
- Прсмотр  сво-тв в `<DbSet>` для создания колонок.
- Просмотр зависимых сущностей в `<DbSet>` Например:
```cs
 public class Book
    {
        public int BookId { get; set; }

        public string Title { get; set; }
        public string Description { get; set; }
        public DateTime PublishedOn { get; set; }

        public int AuthorId { get; set; }
        public Author Author { get; set; }
    }
```

- Вызывет виртуальный метод `OnModelCreating` в котором нужно определить доплнителную информацию по создаия базы данных 