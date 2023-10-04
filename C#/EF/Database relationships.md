-  One-to-one (один к одному)
Главная сущность может ссылаться **только на один объек**т зависимой сущности. А зависимая сущность может ссылаться только на один объект главной сущности.
```cs
public class User
{
	public int Id {get;set;}
	public string Login{get;set;}
	public string Password {get;set;}

	public UserProfile Profile{get;set;}
}

public class UserProfile
{
	public int Id {get;set;}
	public string Name{get;set;}
	public int UserId {get;set;}
	public User User{get;set;}
}

```
`UserProfile` является зависимой по отношению к `User`. Чтобы установить связь один к одному, - у зависимой сущности усанавливается св-во `UserId` (Foring Key) 


- One-to-many (один ко многим)
Главная сущность может ссылаться на коллекцию объектов модели, а модель хранит ссылку только на один объект.
Например, в одной компании могут работать несколько сотрудников, а каждый сотрудник официально может работать только в одной компании.

```cs
public class Company
{
	public int Id {get;set;}
	public string Name {get;set;}
	public List<User> Users {get;set;} = new List<User>();
}

public class User
{
	public int Id {get;set;}
	public string Name {get;set;}
	
	public int CompanyId{get;set;}
	public Company Company{get;set;}
}
```
- Many-to-many (многие ко многим)
Пример: посещение студентами лекций. один студент может поситить множество лекций и на лекциях могут присутствовать множество студентов.
```cs
public class Course
{
	public int Id {get;set;}
	public string Name {get;set;}
	public List<Student> Students {get;set;} = new List<Student>();
}

public class Student
{
	public int Id {get;set;}
	public string Name {get;set;}
	public List<Course> Course {get;set;} = new List<Course>();
}
```
Ef core может автоматически сздать свзяь many-to-many, создав дополнительную таблицу, которая хранит связь между студентами и курсами.

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