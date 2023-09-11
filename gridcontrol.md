
# Adding Custom Actions
```
  <DataGridTemplateColumn Header="Actions" IsReadOnly="True" Width="Auto">
      <DataGridTemplateColumn.CellTemplate>
          <DataTemplate>
              <StackPanel Orientation="Horizontal">
                  <Button Click="Button_Click">
                      +
                  </Button>
                  <Button>
                      -
                  </Button>
              </StackPanel>
          </DataTemplate>
      </DataGridTemplateColumn.CellTemplate>
  </DataGridTemplateColumn>
```

# Accessing Row Value

```
        private void Button_Click(object sender, RoutedEventArgs e)
        {
            var btn = sender as Button;

            var dataObj = (Person)btn.DataContext;

            MessageBox.Show(dataObj.FirstName);
        }
```
