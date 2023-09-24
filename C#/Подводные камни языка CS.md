
### 1. Пармаетры по умолчанию в иерархии

```cs
public interface IFoo
{
	public void M(int x = 10);
}

public class Foo : IFoo
{
	public void M(int x = 5);
}

public static void Main()
{
	IFoo foo = new Foo();
	Foo foo1 = new Foo();

	foo.M() // x = 10 (берется значение указанннео в типе интерфейса)
	foo1.M() // x = 5 (берется значение указанное а типе класса)
}
```