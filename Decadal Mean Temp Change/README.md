

**User Guide: Decadal Mean Temperature Change**

This user guide provides step-by-step instructions on how to use the Decadal Mean Temperature Change script in this repository. This script uses data from the ECMWF (European Centre for Medium-Range Weather Forecasts) from 1970 to 2009 to plot the decadal difference of the mean temperature for West Africa.

**Step 1: Install Dependencies**
Before you can use the Air Pollution Prediction script, you need to make sure that you have all the necessary dependencies installed. This script requires Python 3 and the requests and matplotlib libraries. To install these dependencies, open a terminal or command prompt and run the following command:
```
pip install xarray matplotlib cartopy 
```

**Step 2: Set Directory Location For Data**
After download the data from ECMWF sites, you can set the directory for the location of the data. Assuming the data is on your pendrive, you need to do something like this;
```
data_dir = 'D:/' # Assuming the drive letter of your pendrive is D
```

**Step 3: Plug in the Filenames**
Next, you go to the last line of the function then you put in the filenames of the data you downloaded. Make sure they are separate files for each decade. 
E.g.
```
plot_armor('1970_1979.nc','1980_1989.nc','1990_1999.nc','2000_2009.nc')
```

**Step 4: Run the Script**
Then you run the script. It is advisable to use Jupyter Notebook. But you can also use Spyder or any application that runs python


**Step 5: View the Results**
After entering your city name and API key, the script will retrieve air pollution data from the World Air Quality Index API and display it in a pie chart. The chart shows the relative amounts of different pollutants in the air, such as PM2.5, PM10, NO2, SO2, and O3.

You can use this information to get an idea of air pollution levels in your city and take appropriate precautions.

You can experiment with different cities to see how their air pollution levels compare.

<h2 align=center> 📃 Script Explanation </h2>
  <p align="center">

```
def plot_armor(dat1, dat2, dat3, dat4, cmap='gist_rainbow_r'):
```
- How you normally start writing a python function. The function name is plot_armor and we are passing in dat1, dat2, dat3, dat4, and cmap as an argument for the function. Which means that the function needs these argument in order to run.

```
dat1 = xr.open_dataset(os.path.join(f,dat1))
dat1 = dat1.groupby('time.year').mean('time').mean('year')
dat2 = xr.open_dataset(os.path.join(f,dat2))
dat2 = dat2.groupby('time.year').mean('time').mean('year')
dat3 = xr.open_dataset(os.path.join(f,dat3))
dat3 = dat3.groupby('time.year').mean('time').mean('year')
dat4 = xr.open_dataset(os.path.join(f,dat4))
dat4 = dat4.groupby('time.year').mean('time').mean('year')

```
- Using xarray, you open the individual dataset. The **os.path.join(f,dat(number))** in the code helps join the directory and the location of the ECMWF data. The directory in this case can be a pendrive or the location where the data files are and in this case is f which has been set ahead of the function and the dat1, dat2, dat3, dat4 are the arguments passed in when defining the function which represent the filenames. 

```
dat1.groupby('time.year').mean('time').mean('year')
``` 
- The is used to find the mean of the whole decade by finding the mean of each year.

```
longitude, latitude, ds1 = dat1['longitude'], dat1['latitude'], dat1['t2m']
ds2, ds3, ds4 = dat2['t2m'], dat3['t2m'], dat4['t2m']

```
- Accessing the t2m (2 meters surface temperature data) for each decade and assigning it to a variable. We also access the longitudes and latitudes of the dataset.

```
fig, axes = plt.subplots(nrows=2, ncols=2, figsize=(12,12) ,subplot_kw={'projection': crs.PlateCarree()})

ds1_plot = ds1.plot(ax=axes[0,0], add_colorbar=False, cmap=cmap, vmax=ds1.max(), vmin=ds1.min())
ds2_plot = ds2.plot(ax=axes[0,1], add_colorbar=False, cmap=cmap, vmax=ds2.max(), vmin=ds2.min())
ds3_plot = ds3.plot(ax=axes[1,0], add_colorbar=False, cmap=cmap, vmax=ds3.max(), vmin=ds3.min())
ds4_plot = ds4.plot(ax=axes[1,1], add_colorbar=False, cmap=cmap, vmax=ds4.max(), vmin=ds4.min())

```
- Create a plotting figure and axes for each decade and set up the axis coordinate for each plot. Cmap represents the color mapping used, vmax for the maximum t2m value for that decade and vmin for the minimum t2m for each decade.


```
title = ['Mean Temperature 1970_1979', 'Mean Temperature 1980_1989', 'Mean Temperature 1990_1999',
'Mean Temperature 2000_2009']
```
- The title for each axes plot.


```
for ax, title in zip(axes.flatten(), title):
    ax.set_title(title, fontsize=9)
    ax.add_feature(feature.COASTLINE)
    ax.add_feature(feature.BORDERS)
    ax.add_feature(feature.STATES, linewidth=0.2)
    ax.gridlines(draw_labels=True)
    
cbar_kwargs = {
    'orientation': 'horizontal',
    'fraction': 0.03,  
    'pad': 0.10,       
}
VAR = 'Mean Temp'
```
- Using python for loop to loop through the axes and add each title for the decade plots, add cartopy features such as coastline, borders and states. The **cbar_kwargs** represent the color bar's arguments. **VAR** equals the name for the color bar.

```
cbar1 = plt.colorbar(ds1_plot, ax=axes[0,0], **cbar_kwargs,label= VAR,extend='both')
cbar2 = plt.colorbar(ds2_plot, ax=axes[0,1], **cbar_kwargs,label= VAR,extend='both')
cbar3 = plt.colorbar(ds3_plot, ax=axes[1,0], **cbar_kwargs,label= VAR,extend='both')
cbar4 = plt.colorbar(ds4_plot, ax=axes[1,1], **cbar_kwargs,label= VAR,extend='both')


plt.tight_layout()
plt.savefig('POL.png')
plt.show()
```
- Adding the colorbar for each plot based on the values of each decade and finally plotting the figure and saving it 

```
plot_armor('1970_1979.nc','1980_1989.nc','1990_1999.nc','2000_2009.nc')
```
- Run the script by calling out the function and adding the necessary arguments.

