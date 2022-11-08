# Ornstein_Uhlenbeck

## Models

### Dataset Generation

Dataset generation was handled in the *sample_model* method in each of the generation files.

#### Ornstein_Uhlenbeck

These make use of the *DifferenetialEquations* package to simulate an Ornstein Uhlenbeck process. This equation uses the differential equation form $dx_t = -\theta(\mu - x_t) dt + \sigma dW_t$.


In the code we reference the process in the Fokker-Planck equation representation (representing the process in terms of a probability density function). Given the markovian nature then we can view the process as,


$P(x,t | x', t') = \sqrt{\frac{\theta}{2\pi D(1-e^{-2\theta (t-t')})}}\exp{\left[ - \frac{\theta}{2D} \frac{\left( x-x'e^{-\theta (t - t')}\right)^2}{1-e^{-2\theta (t-t')}}\right]}$ where $D = \frac{\sigma^2}{2}$

We refer to Amplitude as $\frac{D}{\theta}$ (the variance of the guassian as $(t - t') \rightarrow \infty$ ), and $\tau$ as $\frac{1}{\theta}$

#### Noise

In addition to the ornsein uhlenbeck process we also generate additional gaussian noise. Here we have two types, Thermal and Multiplicative. The thermal noise is sampled from a gaussian distribution with variance Tnoise. The muliplicative noise is generated as a normal centered about zero, and its variance proportional to the noise. These noises are then summed with the noisy process.


Mixed noise is a comination of both. We define a ratio to dictate the relative thermal to multiplicative noise. Since the multiplicative noise is dimensionless, to make the ratio dimensionless, we multiply the multiplicative noise by the amplitude, although it may be just as reasonable to use the expectation of the absolute value of the signal $\sigma \sqrt{2}{pi}$ where sigma is the square root of the variance. **Review this paragraph**


### Parameter Prediction

The Probabilistic models and sampling was done using [Turing.jl](https://github.com/TuringLang/Turing.jl)

#### Turing Model

Two different models where created using the Turing interface assuming different types of noise. We treat the Ornstein Uhlenbeck parameters aswell as the true values of the signal at each sample as paramaters to be fit.

##### Thermal Model

The thermal model is labeled as *oupn_thermal*, and simply assumes adative gaussian noise, whose STD is T_noise

##### Mixed Model

The mixed model also assumes the same format as the thermal model, but with the addition of gaussian noise whose variance depends on the magnitude of the signal. This is labeled as *oupn_mt*, and assumes that the ratio to thermal and multiplicative noise is known. Since the therma and multiplicative noises are assumed to be gaussian and independant, we can treat the total noise as a gaussian with the appropriate variance.


#### Posterior Sampling

The posterior is sampled using the sample method provided by turing. The specifics of the sampling can be tweaked on a script by script basis.

## Data

### CSV format

Each csv file is formatted in columns with labels as the first row. If there is a number at the end of the label, then that corresponds to multiple different simulations measuring the same value, with each number labeling the specifics simulation.

### Files

#### Mixed_course.csv
##### Data generated by [mixed_coarse_gen.jl](mixed_coarse_gen.jl)

20 different simulations where run each containing 1500 points of data. The mixed Gaussian model was used with a ratio of 1 and a thermal noise of 0.2. The varying parameter was the ratio of sample rate to time constant, $\frac{\Delta t}{\tau}$, using 0:0.1:2. The posterior was sampled using markov chain monte carlo using a NUTS sampler at 0.65 for 1000 iterations.

#### Mixed_fine.csv
##### Data generated by [mixed_fine_gen.jl](mixed_fine_gen.jl)

A single simulation was run where each containing 1500 points of data. The mixed Gaussian model was used with a ratio of 1 and a thermal noise of 0.2. The varying parameter was the ratio of sample rate to time constant, $\frac{\Delta t}{\tau}$, using 0:0.005:1. The posterior was sampled using markov chain monte carlo using a NUTS sampler at 0.65 for 1000 iterations.

#### Mixed_many.csv
##### Data generated by [mixed_many_gen.jl](mixed_many_gen.jl)

20 simulations were run where each containing 1500 points of data. The Mixed Gaussian model was used with a ratio of 1 and a thermal noise of 0.2. The varying parameter was the ratio of sample rate to time constant, $\frac{\Delta t}{\tau}$, using 0.05:0.05:1. The posterior was sampled using markov chain monte carlo using a NUTS sampler at 0.65 for 1000 iterations.

#### Thermal.csv
##### Data generated by [thermal_gen.jl](thermal_gen.jl)
A single simulation was run where each containing 1500 points of data. The Thermal model was used with a ratio of 1 and a thermal noise of 0.2. The varying parameter was the ratio of sample rate to time constant, $\frac{\Delta t}{\tau}$, using 0:0.005:1. The posterior was sampled using markov chain monte carlo using a NUTS sampler at 0.65 for 1000 iterations.

#### Mixed_ratio.csv
##### Data generated by [mixed_ratio_gen.jl](mixed_ratio_gen.jl)
A single simulation was run where each containing 1500 points of data. The Mixed model was used with a $\frac{\Delta t}{\tau} = 0.5$. The ratio of mixed to thermal noise was varied by 0.1:0.1:10. The thermal noise was calculated such that the summation of the thermal and multiplicative noise amounted to 0.2. The posterior was sampled using markov chain monte carlo using a NUTS sampler at 0.65 for 1000 iterations.
