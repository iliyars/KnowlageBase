Интерфейс предназначен для реализации проверки полей по собственныи правилам.

```cs
using System.Collections;
namespacee System.ComponentModel
{
	    //
    // Summary:
    //     Defines members that data entity classes can implement to provide custom  
    //     synchronous and asynchronous validation support.
    public interface INotifyDataErrorInfo
    {
        //
        // Summary:
        //     Gets a value that indicates whether the entity has validation errors.
        //
        // Returns:
        //     true if the entity currently has validation errors; otherwise, false.
        bool HasErrors { get; }

        //
        // Summary:
        //     Occurs when the validation errors have changed for a property or for the  
        //     entity.
        event EventHandler<DataErrorsChangedEventArgs> ErrorsChanged;

        //
        // Summary:
        //     Gets the validation errors for a specified property or for the entire   
        // entity.
        //
        // Parameters:
        //   propertyName:
        //     The name of the property to retrieve validation errors for; or null or   
        //     System.String.Empty, to retrieve entity-level errors.
        //
        // Returns:
        //     The validation errors for the property or entity.
        IEnumerable GetErrors(string propertyName);
    }
}
```

- **HasErrors** - свойство, сообщающее о наличии ошибок.
- **GetErrors(string propertyName)** - метод возвращающий коллекцию _IEnumerable_, содержащую ошибки у конкретного свойства (с именем propertyName). Если не указывать имя свойства - вернуться все имеющиеся ошибки.
- **ErrorsChanged** - событие, которое необходимо вызывать при каждом изменении ошибок присущих одному или нескольким свойствам.


### Реализация интерфейса
Интерфейс реализуется во *ViewModel*, проверка введенных данных осуществляется в сэеттерах связанного свойства.
Для хранения ошибок создаётся словарь `Dictionary<string, List<string>>()` (ключь - имя свойства, значение - коллеция ошибок у данных этого свойства).


**Пример:**

```cs
public class UserViewModel : ViewModelBase, INotifyDataErrorInfo
{
	private readonly Dictionary<string, List<string>> _errorPropertyNames = new  
	Dictionary<string, List<string>>();
	
	private string _userName;
	public string UserName
	{
		get { return _userName; }
		set
		{
			_userName = value;
			ValidateUserName();
			OnPropertyChanged(nameof(UserName));
		}
	}
	#region INotifyDataErrorInfo
		public bool HasErrors => _errorPropertyNames.Any();
		public event EventHandler<DataErrorsChangedEventArgs>? ErrorsChanged;

		public IEnumerable GetErrors(string? propertyName)
		{
			return _errorPropertyNames.ContainsKey(propertyName) ?
			_errorPropertyNames[propertyName] : Enumerable.Empty<string>();
		}
		private void OnErrorsChanged(string propertyName)
		{
			ErrorsChanged?.Invoke(this, new DataErrorsChangedEventArgs(propertyName));
		}
		#endregion

	private void AddError(string propertyName, string errorMessage)
	{
		if(!_errorPropertyNames.ContainsKey(propertyName))
		{
			_errorPropertyNames[propertyName] = new List<string>();
		}

		if (!_errorPropertyNames[propertyName].Contains(errorMessage))
		{
			_errorPropertyNames[propertyName].Add(errorMessage);
			OnErrorsChanged(propertyName);
		}
	}
	private void ClearErrors(string propertyName)
	{
		if(_errorPropertyNames.ContainsKey(propertyName))
		{
			_errorPropertyNames.Remove(propertyName);
			OnErrorsChanged(propertyName);
		}
	}
	private void ValidateUserName()
	{
		ClearErrors(nameof(UserName));
		if(string.IsNullOrEmpty(UserName))
		{
			AddError(nameof(UserName), "Username cannot be empty.");
			return;
		}
		if(UserName.Length < 5)
		{
			AddError(nameof(UserName), "Username must be at least 6 character long.");
		}
	}
}
```

Для отображеня текста ошибки во *View* необходимо описать тэг `<Validation.ErrorTemplate>`

**Пример:**
```xaml
<TextBox Grid.Row="1"
		 Text="{Binding UserName, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}">
	<Validation.ErrorTemplate>
		<ControlTemplate>
			<StackPanel Orientation="Vertical">
				<AdornedElementPlaceholder>
					<Border BorderBrush="Red"
							BorderThickness="2" />
				</AdornedElementPlaceholder>
				<ItemsControl ItemsSource="{Binding}">
					<ItemsControl.ItemTemplate>
						<DataTemplate>
							<TextBlock Text="{Binding ErrorContent}"
									    Foreground="Red" />
						</DataTemplate>
					</ItemsControl.ItemTemplate>
				</ItemsControl>
			</StackPanel>
		</ControlTemplate>
	</Validation.ErrorTemplate>
</TextBox>
```

**[Validation.ErrorTemplate](Validation.ErrorTemplate)** - Присоединенное свойство ([Attached Property](# Attached properties)) устаннавливается к пользовательскому [ControlTemplate](ControlTemplate) объекту для отображения возможных сообщений об ошибке.
[AdornedElementPlaceholder](AdornedElementPlaceholder) указывет где должен размещаться элемент управления.

