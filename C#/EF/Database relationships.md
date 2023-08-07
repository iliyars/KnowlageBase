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
Загрузка основных и связанных данных в одном  sql querry

```cs
	var book = context.Books.Include(r=>r.AuthorsLinks).ThenInclude(r=>r.Author).Include(r=>r.Reviews).Include(r=> r.Propmotion).First();
```
Преимущество такого способа в том, что EF загружает данные очень эффективно, используя мнимальное обращение к БД. *database round-trips*. Однако такой способ взаимодействия с БД загружает **все** поля основного класса из БД, хотя они не всегда бывают нужны.

#### Explicit loading (загрузка связей после загрузки основного класса)
```cs
	var book  = context.Books.First();
	var numReviews = context.Entry(book).Collection(c=>Reviews).Query().Count();
	var startRatings = context.Entry(book).Collection(c=>c.Reviews)
							.Query().Select(x=>x.NumStars).ToList();
```

#### Select Loading (Загрузка только указанных полей таблицы)

```cs
var book = contex.Books.Select(p => new 
							  {
								  p.Title,
								  p.Price,
								  NumReviews = p.Reviews.Count()
							  });
```
Загружаются только необходимые данные.
Минус - необхоимо писать код для каждого свойстваю