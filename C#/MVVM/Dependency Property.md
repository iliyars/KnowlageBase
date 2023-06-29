DependencyProperty - Особая реализация свойств в WPF, предоставляющая дополнительные взможности по сравнению с свойствами .NET.

*Эти свойства заключены в оболучку обычных свойств CLR и взаимодействовать с ними можно как с обычными совойствами*

**Назначение свойств зависимостей -** предоставлять способ вычисления значения свойства **на основе значения других входных данных** таких как:
- Системные свойства, такие как темы и предпочтения пользователя.
- JIT-механизмы определения свойств, такие как привязка данных, анимация и расадровка.\
- Шаблоны с множественными использованием, такие как ресурсы и стили.
- Значения, известные по отношениям родители-потомки с другими элементами в дереве элементов.
Кроме того свойства зависимостей может предоставлятьь следующие возможности:
- Автономная проверка
- Значение по умолчанию
- Обратные вызовы, отслеживающие изменения других свойств.
- Система, которая может применять значения свойств на основе сведений о среде выполнения.


### Определение свойств зависемостей
- Класс, определяющий св-во зависимости должен иметь в цепочке наследования класс `DependencyObject` 
- Единственное св-во зависимости предоставляется как `public static readonly` в поле типа `DependencyProperty`. По соглащению имя этого поля закнчивается на `Property`. (`HeaderProperty`, `WidthProperty`).
- Переменная `DependencyProperty` зарегистрирована с помощью статического вызова `DependencyProperty.Register()`, что обычно происходит в конструкторе либо встроенным образом, при обявлении переменной.
- Класс определяет дружественное к XAML свойство CLR, которое осуществляет вызов методов, предоставленных `DependencyObject`, для получения и установки значения.

### Возможности свойств зависимостей
- Свойства зависимостей могут наследовать свои знаения от XAML-определения родительского элемента. Например, если вы определите значение атрибута `FontSize` в открывающем дескрипторе `<Window>`, то все элементы управления в этом Window будут по умолчанию иметь этот размер шрифта.
- Свойства зависимости поддерживают возможность иметь значения, установленные элементами, находящимися в области видимости XAML, такие как установка Button свойства Dock родительского DockPanel.
- Свойства зависимости позволяют WPF вычислять значение на основе нескольких внешних значений, что может быть очень важно для анимации и служб привязки данных.
- Свойства зависимости предоставляют инфраструктуру поддержки для триггеров WPF (так же довольно часто используемых при работе с анимацией и привязкой данных).


### Регестрирование Dependency Property
Пример св-ва зависимости Height из FrameworkElement

```cs
// FrameworkElement "является" DependencyObject.
public class FrameworkElement : UIElement, IFrameworkInputElement, IInputElement, ISupportInitialize, IHaveResources, IQueryAmbient
{
   // Статическое свойство только для чтения DependencyProperty.
   public static readonly DependencyProperty HeightProperty;
   
   // Поле DependencyProperty часто регистрируется в статическом конструкторе класса.
   static FrameworkElement()
   {
      HeightProperty = DependencyProperty.Register (
      "Height",
      typeof(double),
      typeof(FrameworkElement),
      new FrameworkPropertyMetadata((double) 1.0 / (double) 0.0,
         FrameworkPropertyMetadataOptions.AffectsMeasure,
         new PropertyChangedCallback(FrameworkElement.OnTransformDirty)),
      new ValidateValueCallback(FrameworkElement.IsWidthHeightValid));
   }
   
   // Оболочка CLR, реализованная унаследованными
   // методами GetValue()/SetValue().
   public double Height
   {
      get { return (double)base.GetValue(HeightProperty); }
      set { base.SetValue(HeightProperty, value); }
   }
}
```

Объект `DependencyProperty` Создаётся статическим методом `DependencyProperty.Register()`
Аргументы:
1. имя совщства-оболочки CLR
2.  несет информацию о типе инкапсулированных данных
3. информация о типе класса, к которому относиться это свойство.
4. объект FrameworkPropertyMetadata - описывает различные детали, 
5. ValidateValueCallback - делегат проверяющий правильность значения.


**! Несмотря на то, что у св-ва зависимости есть оболочка в виде обычного св-ва - Нельзя писать логику проверки значения в блоке Set. Потомучто исполняющая среда wpf полностью минует блок Set и сразу вызывает SetValue(), обращаясь к нему из памяти.**
**XAML в WPF не позволяет вызывать функции в разметке**:
```xaml
<!--Ошибка!! Вызывать методы в XAML разметке нельзя -->
<Button this.SetValue("100")/>
```


## Приклепляемые свойства (Attached properties)
**Attached property** - это свойство зависимости, которым управляет система свойств WPF. Его отличительной чертой является то, что оно применяется к классу отличному от того, в котором оно определно.

Например класс `Grid` определяет прикрепляемые свойства Row и Column.

Для определениия прикрепляемого св-ва используется метод `RegisterAttached()` 

```cs
FrameworkPropertyMetadata metadata = new FrameworkPropertyMetadata(
   0, new PropertyChangedCallback(Grid.OnCellAttachedPropertyChanged));

Grid.RowProperty = DependencyProperty.RegisterAttached("Row", typeof(int),
typeof(Grid), metadata, new ValidateValueCallback(Grid.IsIntValueNotNegative));  
```

При создании такого св-ва оболчка свойства .NET не определяется. Это связвно с тем, что прикрепляемые св-ва могут быть заданы в любом объекте зависимости. 

Вместо оболчки св-ва .NET для прикрепления св-ва требуется пара статических методов, которые могут для установки и получения значения св-ва. `SetValue()` и `GetValue()` (Унаследованные от класса Dependency Object).



### Пример использования Dependency Property

Создадим UserControl 
```xaml
<UserControl x:Class="AP.Components.ShowNumberControl"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" 
             xmlns:d="http://schemas.microsoft.com/expression/blend/2008" 
             xmlns:local="clr-namespace:AP.Components"
             mc:Ignorable="d" 
             d:DesignHeight="450" d:DesignWidth="800">
    <Grid>
        <Label x:Name="numberDisplay"
               Height="50"
               Width="250"
               Background="LightBlue" />
    </Grid>
</UserControl>

```
в `CodeBehind` Создадим св-во зависимости, отвечающее за `Content` `Lable'a`.
```cs
public partial class ShowNumberControl : UserControl
    {
        public ShowNumberControl()
        {
            InitializeComponent();
        }


		//Св-во оболчка .NET
        public int CurrentNumber
        {
            get { return (int)GetValue(CurrentNumberProperty); }
            set 
            {
                SetValue(CurrentNumberProperty, value); }
            }

        // Using a DependencyProperty as the backing store for CurrentNumber.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty CurrentNumberProperty =
	            DependencyProperty.Register("CurrentNumber", typeof(int),
	             typeof(ShowNumberControl), 
                new UIPropertyMetadata(0,new 
	                PropertyChangedCallback(CurrentNumberChanged)),
                new ValidateValueCallback(ValidateCurrentNumber));
		//Метод проверки введенного значения
        public static bool ValidateCurrentNumber(object value)
        {
            if (Convert.ToInt32(value) >= 0 && Convert.ToInt32(value) <= 500)
                return true;
            else
                return false;
        }
		// Callback метод, срабатывающий при изменении знгачения св-ва
        public static void CurrentNumberChanged(DependencyObject depObj, DependencyPropertyChangedEventArgs e)
        {
            ShowNumberControl s = (ShowNumberControl)depObj;
            Label theLabel = s.numberDisplay;
            theLabel.Content = e.NewValue.ToString();
        }
```