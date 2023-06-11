#### Binding комманды c параметром в xaml
```cs
namespace SilmpleTarder.WPF.States.Navigators
{

    public enum ViewType
    {
        Home,
        Portfolio
    }
}
```


```xaml
UserControl x:Class="SilmpleTarder.WPF.Controls.NavigationBar"
             xmlns:nav="clr-namespace:SilmpleTarder.WPF.States.Navigators">
         <Button Command="{Binding SomeCommand}" CommandParameter="{x:Static nav:ViewType.Home}"/>
             
```

#### DataTemplate
```xaml
<Application x:Class="SilmpleTarder.WPF.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:viewmodels="clr-namespace:SilmpleTarder.WPF.ViewModels"
             xmlns:views="clr-namespace:SilmpleTarder.WPF.Views"
             xmlns:local="clr-namespace:SilmpleTarder.WPF">
    <Application.Resources>
        <ResourceDictionary>
            <DataTemplate DataType="{x:Type viewmodels:HomeViewModel}">
                <views:HomeView />
            </DataTemplate>

            <DataTemplate DataType="{x:Type viewmodels:PortfolioViewModel}">
                <views:PortfolioView />
            </DataTemplate>
            
        </ResourceDictionary>
    </Application.Resources>

</Application>

```