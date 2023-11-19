# What I Worked On
This week I worked on bringing equipment type support to the new project as well as integrating it with the database. This includes adding support for the various CRUD operations.

# Code

## Model
```cs
/// <summary>
    /// Represents a equipment object.
    /// </summary>
    /// <remarks>
    /// This class is used to store information about a piece of equipment.
    /// </remarks>
    public class Equipment : INotifyPropertyChanged
    {
        /// <summary>
        /// Gets or sets the unique identifier for the Rota.
        /// </summary>
        [PrimaryKey, AutoIncrement]
        public int ID { get; set; }

        private string _name;

        /// <summary>
        /// Gets or sets the name of the Rota.
        /// </summary>
        public string Name
        {
            get => _name;
            set => SetProperty(ref _name, value);
        }

        /// <summary>
        /// Occurs when a property value changes.
        /// </summary>
        public event PropertyChangedEventHandler? PropertyChanged;

        /// <summary>
        /// Raises the PropertyChanged event.
        /// </summary>
        /// <param name="propertyName">The name of the property that changed.</param>
        protected virtual void OnPropertyChanged([CallerMemberName] string propertyName = null)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }

        /// <summary>
        /// Sets the value of a property and raises the PropertyChanged event if the value has changed.
        /// </summary>
        protected bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
        {
            if (EqualityComparer<T>.Default.Equals(storage, value))
            {
                return false;
            }

            storage = value;
            OnPropertyChanged(propertyName);
            return true;
        }
    }
```

## Services
### EquipmentService
```cs
/// <summary>
    /// Represents the EquipmentService class, which provides CRUD operations for Equipment objects.
    /// </summary>
    public class EquipmentService : IEquipmentService
    {
        private SQLiteAsyncConnection _dbConnection;

        /// <summary>
        /// Sets up the SQLite database connection and creates the Equipment table if it doesn't exist.
        /// </summary>
        private async Task SetUpDb()
        {
            if (_dbConnection != null)
                return;

            _dbConnection = new SQLiteAsyncConnection(DatabaseSettings.DBPath, DatabaseSettings.Flags);
            await _dbConnection.CreateTableAsync<Equipment>();
        }

        /// <summary>
        /// Adds a new piece of equipment to the database.
        /// </summary>
        public async Task<int> AddEquipment(Equipment equipment)
        {
            try
            {
                await SetUpDb();
                return await _dbConnection.InsertAsync(equipment);
            }
            catch (Exception ex)
            {
                Console.WriteLine("Database or insert failure: " + ex.Message);
                throw;
            }
        }

        /// <summary>
        /// Deletes a piece of equipment from the database.
        /// </summary>
        public async Task<int> DeleteEquipment(Equipment equipment)
        {
            await SetUpDb();
            return await _dbConnection.DeleteAsync(equipment);
        }

        /// <summary>
        /// Retrieves a list of all equipment from the database.
        /// </summary>
        public async Task<List<Equipment>> GetEquipmentList()
        {
            await SetUpDb();
            return await _dbConnection.Table<Equipment>().ToListAsync();
        }

        /// <summary>
        /// Updates an existing piece of equipment in the database.
        /// </summary>
        public async Task<int> UpdateEquipment(Equipment equipment)
        {
            await SetUpDb();
            return await _dbConnection.UpdateAsync(equipment);
        }
    }
```
### IEquipmentService
```cs
public interface IEquipmentService
    {
        /// <summary>
        /// Retrieves a list of all Equipment.
        /// </summary>
        Task<List<Equipment>> GetEquipmentList();

        /// <summary>
        /// Adds a new piece of equipment to the service.
        /// </summary>       
        Task<int> AddEquipment(Equipment equipment);

        /// <summary>
        /// Deletes a piece of equipment from the service.
        /// </summary>      
        Task<int> DeleteEquipment(Equipment equipment);

        /// <summary>
        /// Updates an existing piece of equipment in the service.
        /// </summary>
        Task<int> UpdateEquipment(Equipment equipment);
    }
```

## View
### XAML
```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="UndacBlue.Views.EquipmentPage"
             Title="EquipmentPage">
    <VerticalStackLayout Spacing="10" Margin="5">
        <Editor x:Name="EquipmentPageEditor"
                Placeholder="Add a piece of equipment"
                HeightRequest="100" />

        <Grid ColumnDefinitions="*,*" ColumnSpacing="4">
            <Button Text="Save"
                    Clicked="OnSaveButtonClicked" />

            <Button Grid.Column="1"
                    Text="Delete"
                    Clicked="OnDeleteButtonClicked" />
        </Grid>

        <ListView x:Name="EquipmentListView" Background="White" ItemSelected="OnEquipmentItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell TextColor="Black" Text="{Binding Name}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </VerticalStackLayout>
</ContentPage>
```

### C#
```cs

/// <summary>
/// Represents the EquipmentPage class, which is responsible for managing and displaying Equipment data.
/// </summary>
public partial class EquipmentPage : ContentPage
{
    private Equipment _selectedEquipment = null;
    private IEquipmentService _equipmentService;
    private ObservableCollection<Equipment> _equipmentCollection = new ObservableCollection<Equipment>();

    /// <summary>
    /// Initializes a new instance of the EquipmentPage class.
    /// </summary>
    public EquipmentPage()
	{
		InitializeComponent();
        BindingContext = new Rota();
        _equipmentService = new EquipmentService();

        Task.Run(async () => await LoadEquipments());
        EquipmentPageEditor.Text = "";
    }

    /// <summary>
    /// Loads the list of Equipment asynchronously.
    /// </summary>
    private async Task LoadEquipments()
    {
        _equipmentCollection = new ObservableCollection<Equipment>(await _equipmentService.GetEquipmentList());
        EquipmentListView.ItemsSource = _equipmentCollection;
    }

    /// <summary>
    /// Handles the click event of the Save button.
    /// </summary>
    private void OnSaveButtonClicked(object sender, EventArgs e)
    {
        if (string.IsNullOrEmpty(EquipmentPageEditor.Text)) return;

        if (_selectedEquipment == null)
        {
            AddNewEquipment();
        }
        else
        {
            UpdateExistingEquipment();
        }

        ClearUIComponents();
    }

    /// <summary>
    /// Adds a new piece of equipment to the collection and the data service.
    /// </summary>
    private void AddNewEquipment()
    {
        Equipment newEquipment = CreateEquipmentFromInput();
        _equipmentService.AddEquipment(newEquipment);
        _equipmentCollection.Add(newEquipment);
    }

    /// <summary>
    /// Updates an existing piece of equipment in the collection and the data service.
    /// </summary>
    private void UpdateExistingEquipment()
    {
        _selectedEquipment.Name = EquipmentPageEditor.Text;
        _equipmentService.UpdateEquipment(_selectedEquipment);
        Equipment existingEquipment = _equipmentCollection.FirstOrDefault(x => x.ID == _selectedEquipment.ID);
        existingEquipment.Name = EquipmentPageEditor.Text;
    }

    /// <summary>
    /// Creates a new equipment object from the input.
    /// </summary>
    /// <returns>A new equipment object.</returns>
    private Equipment CreateEquipmentFromInput()
    {
        Equipment equipment = new Equipment();
        equipment.Name = EquipmentPageEditor.Text;

        return equipment;
    }

    /// <summary>
    /// Clears the UI components and resets the selected Equipment.
    /// </summary>
    private void ClearUIComponents()
    {
        EquipmentListView.SelectedItem = null;
        EquipmentPageEditor.Text = "";
        _selectedEquipment = null;
    }

    /// <summary>
    /// Handles the click event of the Delete button.
    /// </summary>
    private async void OnDeleteButtonClicked(object sender, EventArgs e)
    {
        if (EquipmentListView.SelectedItem == null)
        {
            await DisplayAlert("No Equipment Selected", "Select the equipment you want to delete from the list", "OK");
            return;
        }

        await _equipmentService.DeleteEquipment(_selectedEquipment);
        _equipmentCollection.Remove(_selectedEquipment);

        ClearUIComponents();
    }

    /// <summary>
    /// Handles the selection of a piece of equipment in the list.
    /// </summary>
    private void OnEquipmentItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        _selectedEquipment = e.SelectedItem as Equipment;
        if (_selectedEquipment == null) return;

        EquipmentPageEditor.Text = _selectedEquipment.Name;
    }
```

# Code Review
I did not get my code reviewed as of writing this unfortunately.

# Reflection
This week I learned when working on my issue that I should thoroughly test my code before submitting for a pull request. After submitting my PR, I realised that I made a critical mistake that would cause none of my code to work as intended. It was a quick fix but it would have been less of a headache if I caught it before submitting and requesting a review. This I think is a common problem in software development, especially a team situation where you are working towards deadlines and asking for reviews.
