
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
# Dispatcher thing

One possible way to achieve your goal is to use a DispatcherTimer to update the marker position on each tick event, instead of using a for loop. You can store the computed values in a collection, and then use an index variable to access them in the timer tick event handler. For example, you can try something like this:

```CS
// Assuming you have a collection of computed values, such as a list of coordinates
List<Coordinate> coordinates = new List<Coordinate>();
// Fill the list with your computed values here

// Create a DispatcherTimer object
DispatcherTimer timer = new DispatcherTimer();

// Set the interval to 1000 milliseconds (1 second)
timer.Interval = TimeSpan.FromMilliseconds(1000);

// Declare an index variable to keep track of the current position
int index = 0;

// Add a tick event handler
timer.Tick += (s, e) =>
{
    // Check if the index is within the bounds of the collection
    if (index < coordinates.Count)
    {
        // Get the current coordinate from the collection
        Coordinate coordinate = coordinates[index];

        // Update the marker position on the map using the coordinate
        // You can use the GMap library methods here
        // For example: map.AddMarker(coordinate.Latitude, coordinate.Longitude);

        // Increment the index for the next tick
        index++;
    }
    else
    {
        // Stop the timer when the index reaches the end of the collection
        timer.Stop();
    }
};

// Start the timer
timer.Start();
```

I hope this helps you with your WPF application. 😊

Source: Conversation with Bing, 9/18/2023
(1) c# - How do I create a timer in WPF? - Stack Overflow. https://stackoverflow.com/questions/11559999/how-do-i-create-a-timer-in-wpf.
(2) c# - How to make a wpf countdown timer? - Stack Overflow. https://stackoverflow.com/questions/16748371/how-to-make-a-wpf-countdown-timer.
(3) Timer In WPF - C# Corner. https://www.c-sharpcorner.com/uploadfile/mahesh/timer-in-wpf/.
(4) vb.net - Making a timer in WPF - Stack Overflow. https://stackoverflow.com/questions/6644989/making-a-timer-in-wpf.
(5) undefined. http://schemas.microsoft.com/winfx/2006/xaml/presentation.
(6) undefined. http://schemas.microsoft.com/winfx/2006/xaml.
(7) wpf - How I can set DispatcherTimer into the for loop - Stack Overflow. https://stackoverflow.com/questions/4544147/how-i-can-set-dispatchertimer-into-the-for-loop.
(8) c# - DispatcherTimer waiting in for loop - Stack Overflow. https://stackoverflow.com/questions/24075350/dispatchertimer-waiting-in-for-loop.
(9) DispatcherTimer in WPF - C# Corner. https://www.c-sharpcorner.com/blogs/dispatchertimer-in-wpf.