

**User Guide: Simple Data Assimilation**

This user guide provides step-by-step instructions on how to implement a simple data assimilation using techniques such as the Kalman Filtering to combine observational data with Numerical Weather Prediction (Model) data.  

**What is Data Assimilation**

Data assimilation (DA) refers to a class of techniques that lie at the interface between
computational sciences and real measurements, and aim at fusing information from both sides to
provide better estimates of the system’s state. One of the very mature applications that significantly
utilize DA is weather forecast, that we rely on in our daily life. In order to predict the weather (or the
state of any system) in the future, a model has to be solved, most often by numerical simulations.
However, a few problems rise at this point and we refer to only few of them here. First, for accurate
predictions, these simulations need to be initiated from the true initial condition, which is never known
exactly. For large scale systems, it is almost impossible to experimentally measure the full state of the
system at a given time. For example, imagine simulating the atmospheric or oceanic flow, then you need
to measure the velocity, temperature, density, etc. at every location corresponding to your numerical
grid! Even in the hypothetical case when this is possible, measurements are always contaminated
by noise, reducing the fidelity of your estimation. Second, the mathematical model that completely
describes all the underlying processes and dynamics of the system is either unknown or hard to
deal with. Then, approximate and simplified models are adopted instead. Third, the computational
resources always constrain the level of accuracy in the employed schemes and enforce numerical
approximations. Luckily, DA appears at the intersection of all these efforts and introduces a variety
of approaches to mitigate these problem, or at least reduce their effects. In particular, DA techniques
combines possibly incomplete dynamical models, prior information about initial system’s state and
parameterization, and sparse and corrupted measurement data to yield optimized trajectory in order
to describe the system’s dynamics and evolution.

**Kalman's Filtering**
The idea behind Kalman filtering techniques is to propagate the mean as well as the covariance
matrix of the system’s state sequentially in time. That is, in addition to providing an improved state
estimate (i.e., the analysis), it also gives some information about the statistical properties of this state
estimate. This is one main difference between Kalman filtering and variational methods, which often
assumes a fixed (stationary) background covariance matrices. Kalman filters are also very popular in
systems engineering, robotics, navigation, and control. Almost all modern control systems use the
Kalman filter. It assisted the guidance of the Apollo 11 lunar module to the moon’s surface, and most
probably will do the same for next generations of aircraft as well.
Although most application in fluid dynamics involve nonlinear systems, we first describe the
standard Kalman filter developed for the linear dynamical system case with linear observation operator
described as
```
uₜ(tₖ+1) = Mₖuₜ(tₖ) + ξ p(tₖ+1)
w(tₖ) = Hₖuₜ(tₖ) + ξm(tₖ)
```


where M ∈ ℝⁿˣⁿ is a non-singular system matrix defining the underlying governing processes and ξᵖ ∈ ℝⁿ describes the process noise (or model error). 
H ∈ ℝᵐˣⁿ represents the measurement system with a measurement noise of ξᵐ ∈ ℝᵐ.

As presented in Section 2, the true state uₜ(tₖ) is assumed to be a random variable with known mean E[uₜ(tₖ)] = ū(tₖ) 
and covariance matrix of E[(uₜ(tₖ) − ū(tₖ))(uₜ(tₖ) − ū(tₖ))ᵀ] = Bₖ.

In Kalman filtering, we note that the covariance matrix evolves in time, and thus appears the subscript. 
We also assume that the process noise is unbiased with zero mean and a covariance matrix Q. 
That is E(ξᵖ(tₖ)) = 0 and E(ξᵖ(tₖ)ξᵖ(tₖ)ᵀ) = Qₖ.

Thus, the goal of the filtering problem is to find a good estimate (analysis) uₐ(tₖ) of the true system’s state uₜ(tₖ) 
given a dynamical model and a set of noisy observations {w(tᵢ)} collected at some time instants tᵢ ∈ (0, tₖ]. 
The optimality of the estimate uₐ(tₖ) is defined as the one which minimizes E[(uₜ(tₖ) − uₐ(tₖ))ᵀ(uₜ(tₖ) − uₐ(tₖ))]. 
This filtering process generally consists of two steps: the forecast step and the data assimilation step.

The forecast step is performed using the predictable part of the given dynamical model starting 
from the best known information at time tₖ (denoted as ūᵇ(tₖ)) to produce a forecast or background 
estimate ū(tₖ+1) = Mₖūᵇ(tₖ). 

The difference between the background forecast and true state at tₖ+1 can be written as follows,
```
ξᵇ(tₖ₊₁) = uₜ(tₖ₊₁) − ūᵇ(tₖ₊₁)
           = (Mₖuₜ(tₖ) + ξᵖ(tₖ₊₁)) − Mₖūᵇ(tₖ)
           = Mₖ(uₜ(tₖ) − ūᵇᵇ(tₖ)) + ξᵖ(tₖ₊₁)
           = Mₖξᵇ(tₖ) + ξᵖ(tₖ₊₁),

```
where ξᵇ(tₖ) = (uₜ(tₖ) − ūᵇᵇ(tₖ)) is the error estimate at tₖ, with zero mean and covariance matrix of Bᵇₖ.
The covariance matrix of the background estimate at tₖ₊₁ can be evaluated as Bₖ₊₁ =
E[ξᵇ(tₖ₊₁)ξᵇ(tₖ₊₁)ᵀ] = E[(Mₖξᵇ(tₖ) + ξᵖ(tₖ₊₁))(Mₖξᵇ(tₖ) + ξᵖ(tₖ₊₁))ᵀ].

Since ξᵇ(tₖ) and ξᵖ(tₖ₊₁) are assumed to be uncorrelated (i.e., E[ξᵇ(tₖ)ξᵖ(tₖ₊₁)ᵀ] = 0), the background covariance matrix at tₖ₊₁ can be computed as follows:
Bₖ₊₁ = MₖBᵇₖMₖᵀ + Qₖ₊₁.

Now, with the forecast step, we have a background estimate at tₖ₊₁ defined as ūᵇ(tₖ₊₁) with a covariance matrix Bₖ₊₁. 
Then, measurements w(tₖ₊₁) are collected at tₖ₊₁ with a linear operator Hₖ₊₁ and measurement noise ξᵐ(tₖ₊₁) 
with zero mean and a covariance matrix of Rₖ₊₁. Thus, we would like to fuse these pieces of information to create an optimal 
unbiased estimate (analysis) uₐ(tₖ₊₁) with a covariance matrix Pₖ₊₁. This can be defined as a linear function of ūᵇ(tₖ₊₁) 
and w(tₖ₊₁) as follows,


```
uₐ(tₖ₊₁) = ūᵇ(tₖ₊₁) + Kₖ₊₁ [w(tₖ₊₁) − Hₖ₊₁ūᵇ(tₖ₊₁)]
```

where [w(tₖ₊₁) − Hₖ₊₁ūᵇ(tₖ₊₁)] is the innovation vector and K ∈ ℝⁿˣᵐ is called the Kalman gain matrix. 
We highlight that the Kalman gain matrix is defined in such a way to minimize E[(uₜ(tₖ₊₁) − uₐ(tₖ₊₁))ᵀ(uₜ(tₖ₊₁) − uₐ(tₖ₊₁))] = tr(Pₖ₊₁).

This can be written as

```
Kₖ₊₁ = Bₖ₊₁Hₖ₊₁ᵀ [Hₖ₊₁Bₖ₊₁Hₖ₊₁ᵀ + Rₖ₊₁]⁻¹,
```
resulting in an analysis covariance matrix defined as
```
Pₖ₊₁ = (Iₙ − Kₖ₊₁Hₖ₊₁)Bₖ₊₁,

where Iₙ is the n × n identity matrix. The resulting analysis uₐ(tₖ₊₁) is known as the best linear unbiased estimate (BLUE). 
We highlight that information at tₖ might correspond to the analysis (i.e., ūᵇᵇ(tₖ) = uₐ(tₖ)) obtained from the last data 
assimilation implementation, or just from the previous forecast if no other information is available. Thus, the Kalman filtering process 
can be summarized as follows:
```
```
Inputs: ūᵇᵇ(tₖ), Bᵇₖ, Mₖ, Qₖ₊₁, w(tₖ₊₁), Rₖ₊₁, Hₖ₊₁

Forecast: ūᵇ(tₖ₊₁) = Mₖūᵇᵇ(tₖ)
Bₖ₊₁ = MₖBᵇₖMₖᵀ + Qₖ₊₁

Kalman gain: Kₖ₊₁ = Bₖ₊₁Hₖ₊₁ᵀ [Hₖ₊₁Bₖ₊₁Hₖ₊₁ᵀ + Rₖ₊₁]⁻¹

Analysis: uₐ(tₖ₊₁) = ūᵇ(tₖ₊₁) + Kₖ₊₁ [w(tₖ₊₁) − Hₖ₊₁ūᵇ(tₖ₊₁)]
Pₖ₊₁ = (Iₙ − Kₖ₊₁Hₖ₊₁)Bₖ₊₁,

where the inputs at tₖ are defined as
(ūᵇᵇ(tₖ), Bᵇₖ) = 
(uₐ(tₖ), Pₖ) if w(tₖ) is available,
(ūᵇ(tₖ), Bₖ) otherwise.
```


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

