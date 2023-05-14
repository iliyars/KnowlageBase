
Интерфейс [ICommand](https://learn.microsoft.com/en-us/dotnet/api/system.windows.input.icommand?view=net-7.0) представляет собой контракт для взаимодействия команд с приложением 

```cs
public interface ICommand
{
   void Execute(object parameter);
   bool CanExecute(object parameter);
   event EventHandler CanExecuteChanged;
}
```

### Методы
| [CanExecute(Object)](https://learn.microsoft.com/ru-ru/dotnet/api/system.windows.input.icommand.canexecute?view=net-7.0#system-windows-input-icommand-canexecute(system-object)) | Определет, может ли команда выполниться в данный момент. |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- |
|  [Execute(Object)](https://learn.microsoft.com/ru-ru/dotnet/api/system.windows.input.icommand.execute?view=net-7.0#system-windows-input-icommand-execute(system-object))                  |Метод, выполняющий команду.                                                                                                                                                             |            |

### Событя
| [CanExecuteChanged](https://learn.microsoft.com/ru-ru/dotnet/api/system.windows.input.icommand.canexecutechanged?view=net-7.0) | Событие для отлова изменения, влияющих на возможность выполлнения  |
| ---- | ---- |

Для использоования этого интерфейс в приложении нужно реализовать этот интерффейс в базовом классе каманд.
```cs

public abstarct class CommandBase : ICommand
{
	public event EventHandler CanExecuteChanged;
	///для переопределения в наследниках необходимой проверкой на возможность выполнения
	public virtual bool CanExecute(object parameter)
	{
		return bool;
	}
	///Метод для реализации в наследниках необходимой логики командного метода.
	public abstract void Execute(object parameter);

	protected void OnCanExecuteChanged()
	{
		CanExecuteChanged?.Invoke(this, new EventArgs());
	}
}
```

Теперь все команды будут наследоваться от `CommandBase`
Пример:

```cs
public class ShowMessageCommand : CommandBase
{
	private readonly ViewModelBase _viewModel;

    public ShowMessageCommand(SomeViewModel viewModel)
    {
        _viewModel = viewModel;
		///Подписываемя на события изменения свойств во ViewModel
        _viewModel.PropertyChanged += ViewModel_PropertyChanged;
    }

	public ovveride bool CanExecute(objetc parameter)
	{
		/// В качестве проверки на возможноость выполнения указываем св-во из ViewModel 
		return _viewModel.IsValid;
	}

	public override Execute(object parameter)
	{
		MessageBox.Show($"{_viewModel.Message}");
	}

	private void ViewModel_PropertyChanged(object sender, PropertyChangedEventArgs e)
    {
	    // При изменении св-ва в ViewModel проверяем возможность выполнения команды.
        OnCanExecuteChanged();
    }
        
}

```
 
В данном примере комманда `ShowMessageCommand` выдает `MessageBox` с текстом свойства из преданной ViewModel. В качестве проверки возможности выполнения используется свойство `IsValid` из `ViewModel`.
***В конструктор класса команды можно передовать объекты для использования их в команде.***

### Приявзка команды в XAML

Определяем свойство с типом ICommand во ViewModel и определяем его в кострукторе
```cs
public clas SomeViewModel : ViewModelBase
{
	public ICommand ShowMessageCommand {get;}
	public bool IsValid {get;set;}

	public SomeViewModel()
	{
		ShowMessageCommand(this);
	}

} 
```
(Эта ViewModel является контекстом данных для View в к которому привяжем команду)

В Xaml в теге привязываем свойство Command к имени команды во ViewModel (ShowMessageCommand)
```xaml
	<Button Margin="10 0 0 0" Padding="10 5"
                    Command="{Binding ShowMessageCommand}"
                    Content="Show message" />
```
