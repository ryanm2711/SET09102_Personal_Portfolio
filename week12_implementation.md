# Code Snippet

## Model
```cs
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace UndacBlue.Models
{
    [Table("system_type")]
    public class System : BaseEntity
    {
        [Required]
        public string Name { get; set; }
    }
}
```

## View
### XAML
```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="UndacBlue.Views.RoomPage"
             Title="SystemTypePage">
    <VerticalStackLayout Spacing="10" Margin="5">
        <Editor x:Name="SystemTypeEditor"
                Placeholder="Add a System Type"
                HeightRequest="100" />

        <Grid ColumnDefinitions="*,*" ColumnSpacing="4">
            <Button Text="Save"
                    Clicked="OnSaveButtonClicked" />

            <Button Grid.Column="1"
                    Text="Delete"
                    Clicked="OnDeleteButtonClicked" />
        </Grid>

        <ListView x:Name="SystemTypeListView" Background="White" ItemSelected="OnSystemTypeSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell TextColor="Black" Text="{Binding SystemType}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </VerticalStackLayout>
</ContentPage>
```

### C#
```cs
using System.Collections.ObjectModel;
using System.Diagnostics.Metrics;
using System.Linq;
using System.Runtime.CompilerServices;
using System.Threading.Tasks;
using UndacBlue.Services;
using UndacBlue.Models;
using static System.Net.Mime.MediaTypeNames;
using Microsoft.Maui.Controls.Compatibility;

namespace UndacBlue.Views;

public partial class SystemTypePage : ContentPage
{
	private readonly IBaseService<UndacBlue.Models.System> _service;

	public ObservableCollection<UndacBlue.Models.System> ItemsList { get; set; }
	private UndacBlue.Models.System _selectedItem;

	public SystemTypePage()
	{
		InitializeComponent();

		_service = new BaseService<UndacBlue.Models.System>();

        ItemsList = new ObservableCollection<Models.System>();
        SystemTypeListView.ItemsSource = SystemTypes;

        LoadItems();
    }

    private async void LoadItems()
    {
        var Rooms = new ObservableCollection<Room>(await _service.GetAllAsync());
        SystemTypeListView.ItemsSource = Rooms;
    }

    private async void OnSaveButtonClicked(object sender, EventArgs e)
    {
        var newName = SystemTypeEditor.Text;

        if (!string.IsNullOrWhiteSpace(newName))
        {
            var newRoom = new Room { RoomName = newName };

            if (_selectedItem != null)
            {
                await _service.UpdateAsync(_selectedItem.Id, newName);
                _selectedItem.Name = newName;
            }
            else
            {
                await _service.CreateAsync(newName);
            }

            SystemTypeEditor.Text = string.Empty;
            _selectedItem = null;

            LoadItems();
        }
    }

    private async void OnDeleteButtonClicked(object sender, EventArgs e)
    {
        if (_selectedItem == null)
            return;
        await _service.DeleteAsync(_selectedItem.Id);
        ItemsList.Remove(_selectedItem);
        _selectedItem = null;
        LoadItems();
    }

    private void OnSystemTypeSelected(object sender, SelectedItemChangedEventArgs e)
    {
        if (e.SelectedItem is Models.System selectedItem)
        {
            this._selectedItem = selectedItem;
        }
    }
}
```

# Software Design Practices
This code snippet is demonstrating good design practices such as DRY because the model being used is derived off of a base entity class. This allows for extensibility alongside saving development time. For example if I need to make a change to ALL models, I simply need to edit the base entity class and it'll apply the changes to the rest. I'm also demonstrating good naming conventions for my methods, variables, and classes. For example, public variables is accepted to use capital letters. Whereas private variables have been agreed to stay lowercase and begin with an _. 

# Changes Requested
My PR has not been reviewed yet so I cannot say, but if I were to make an educated guess, it would most likely be the lack of doxygen comments present - this is something I will address for next week.

# Reflection
I learned this week how vital it is to follow DRY. The new system sped up my development time this week by tremendous amounts. Going forward for next week I hope to make more doxygen comments, I will admit I left the code for this week a little last minute, so the comments came as of an after thought. Which brings me to my next part that I learned, the importance of scheduling, especially for projects like this that have deadlines every week. Getting it done early, giving myself that breathing room can come a long way, and I hope to fix that for next week.
