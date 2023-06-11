- One-to-one (один к одному)
- One-to-many (один ко многим)
- Many-to-many (многие ко многим)



### Loading related data

По дэфолту  связанные данные не загружаются. и свойства основного entity будут *null*.
- Eager loading
- explicit loading
- select loading
- lazy loading

#### Eager loading (загрузка собственных классов вместе с основным)
	Для такое загрузки во **Fluent API** испльзуются два метода (`Include` И `ThenInclude`)


```cs
	var book = context.Books.Include(r=>r.AuthorsLinks).ThenInclude(r=>r.Author).Include(r=>r.Reviews).Include(r=> r.Propmotion).First();
```


#### Explicit loading (загрузка связей после загрузки основного класса)
```cs
	var book  = context.Books.First();
	context.Entry(book).Collection(c=>c.AuthorsLink).Load();
	foreach (var authorLink in book.AuthorsLink)
	{
		context.Entry(authorsLink).Reference(r=>r.Author).Load();
	}
	context.Entry(book).Collection(c=>c.Reviews).Load();
```