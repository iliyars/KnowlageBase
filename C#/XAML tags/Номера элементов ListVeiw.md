```xaml
 <ListView.View>
   <GridView>
    <GridViewColumn Header="N#" Width="50" DisplayMemberBinding="{Binding RelativeSource={RelativeSource FindAncestor, AncestorType={x:Type ListViewItem}}, Converter={StaticResource IndexConverter}}"/>



```



```cs
public class IndexConverter : IValueConverter
    {
        public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
        {
            ListViewItem item = (ListViewItem)value;
            ListView listView = ItemsControl.ItemsControlFromItemContainer(item) as ListView;
            int index = listView.ItemContainerGenerator.IndexFromContainer(item);
            return index.ToString();
        }

        public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
        {
            throw new NotImplementedException();
        }
    }
```