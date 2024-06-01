

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
```
where Iₙ is the n × n identity matrix. The resulting analysis uₐ(tₖ₊₁) is known as the best linear unbiased estimate (BLUE). 
We highlight that information at tₖ might correspond to the analysis (i.e., ūᵇᵇ(tₖ) = uₐ(tₖ)) obtained from the last data 
assimilation implementation, or just from the previous forecast if no other information is available. Thus, the Kalman filtering process 
can be summarized as follows:
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

The Kalman filter is a recursive algorithm that can be implemented in a sequential manner.

<h2 align=center> 📃 Script Explanation </h2>
  <p align="center">

**Step 1: Install Dependencies**
Since this is a simple data assimilation, we wouldnt need more packages than we do. The only package we will need is numpy
```
pip install numpy
```

**Step 2: Set the observations and the Model Data**
The observations and the model_data below are sample air temperature data
```
observations = np.array([15.2,16.1,14.5,15.8,25.0])
model_data = np.array([14.8,15.5,14.0,16.0,25.3])
```

**Step 3: Set the initial estimates**
The next step is to set the initial estimates for the state and the uncertainty of the state. Here we set the inital state estimate to be the first value of the model data and out of our choosing, we set the initial covariance estimate to be 1
E.g.
```
initial_state_estimate = model_data[0]
initial_covariance_estimate = 1
```

**Step 4: Set the Observational and Model Uncertainty**
The observation and model uncertainty are set to 1 and they correspond to the measurement_noise and the process_noise. Here what we are saying that maybe when the observational data was being taken, something happened and since we dont know the uncertainty, we equate it to 1. The same goes for the model
```
process_noise = 1
measurement_noise = 1

```




**Step 5: Create empty arrays and set initial values**
The next step is to create empty arrays to store the state estimates and the covariance estimates.

```
num_measurements = len(observations)

# Arrays to store the results
filtered_state_estimates = np.zeros(num_measurements)
filtered_covariance_estimates = np.zeros(num_measurements)

# Initial values
filtered_state_estimates[0] = initial_state_estimate
filtered_covariance_estimates[0] = initial_covariance_estimate

```

**Step 6: Loop through the number of observations**
The next step is to loop through the number of observations and update the state and the covariance estimates 
and also calculate the kalman gain for each step. The steps here are 1 to 5

Predicted State Estimate 
The predicted state estimate is a forecast of the state variable before incorporating the new observation at time step 𝑘
It is based on the previous state estimate and the system's model, and it provides a means to anticipate the state of the system using all the information available up to the previous time step.

Significance: The predicted state estimate represents the expected state of the system before considering the new measurement. It is crucial for understanding how the system evolves over time based on the internal dynamics or model.

Predicted Covariance Estimate 
The predicted covariance estimate quantifies the uncertainty or confidence in the predicted state estimate. It combines the previous uncertainty with the process noise to reflect the increasing uncertainty over time due to the inherent variability in the system.

Significance: The predicted covariance estimate provides a measure of how much uncertainty there is in the predicted state. A larger predicted covariance implies greater uncertainty, while a smaller one implies more confidence in the prediction. This estimate is critical for determining the Kalman gain, which balances the reliance on the predicted state versus the new observation during the update step.

```
for k in range(1, num_measurements):
    # Prediction step
    predicted_state_estimate = filtered_state_estimates[k-1]
    predicted_covariance_estimate = filtered_covariance_estimates[k-1] + process_noise

    # Update step
    kalman_gain = predicted_covariance_estimate / (predicted_covariance_estimate + measurement_noise)
    filtered_state_estimates[k] = predicted_state_estimate + kalman_gain * (observations[k] - predicted_state_estimate)
    filtered_covariance_estimates[k] = (1 - kalman_gain) * predicted_covariance_estimate
    

```

Assimilated Data: The filtered_state_estimates[k] represents the best estimate of the state at time 𝑘
k after assimilating both the model prediction and the observational data. It leverages the strength of both sources: the temporal consistency of the model and the accuracy of the observations.
Reduced Uncertainty: Through the Kalman gain, the filter adjusts the influence of the prediction and the observation based on their uncertainties. If the model prediction is more certain (lower predicted covariance), it will have more influence. Conversely, if the observation is more certain (lower measurement noise), it will have more influence.
Continuous Adjustment: At each time step, the filter continuously refines its state estimate, ensuring that the assimilated data reflects both the latest observation and the underlying model dynamics.