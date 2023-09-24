В Ef можно писать команды, которые нельзя исполнять на серевере БД. Они исполняются в клиентской части приложеиня.
```cs
var result = dbContext.Books.Select(p => new
            {
                p.Id,
                p.Title,
                AuthorsString = string.Join(", ", p.AuthorLink.Select(q=>q.Author.Name)),
            }
            ).ToList();
```