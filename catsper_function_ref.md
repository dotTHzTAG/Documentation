# CaTSper Processing Steps: A Detailed Description

This document aims to provide a detailed description to the scientific basis of the processing steps involved in [CaTSper](https://github.com/CamTHz/catsper). A step-by-step guide to using CaTSper is available [here](https://github.com/CamTHz/CamTHz.GitHub.io/blob/main/catsper_tutorial.md#catsper-step-by-step-tutorial).
<!-- A detailed in-line annotation of CaTSper's code is available here. -->

## Time Domain Analysis

The time delay $\Delta t$ is the extra time needed for the THz pulse to traverse through the sample thickness $H$, compared to the THz pulse traversing the same thickness in the reference measurement (air, refractive index $n_{ref} = 1$). The time-domain effective refractive index $n_{eff,TD}$ of the sample is thus calculated by

$$ n_{eff,TD} = \frac{c \Delta t}{H} + 1 $$

where $c$ is the speed of light with a value of $3 \times 10^8$ ms $^{-1}$. $n_{eff}$ is calculated to four significant figures.

The time delay due to one internal reflection occurring $\Delta t_{1etl}$ is also considered. For one internal reflection, the THz pulse additionally travels through two times the sample thickness, compared to the original $\Delta t$. $\Delta t_{1etl}$ is thus calculated by

$$ \Delta t_{1etl} = \Delta t + \frac{2H n_{eff}}{c}$$

A step-by-step guide to CaTSper's time-domain analysis in CaTSper can be found [here](https://github.com/CamTHz/CamTHz.GitHub.io/blob/main/catsper_tutorial.md#time-domain-td-tab).  

## Fourier Transform

The following and the user-selected processing options in CaTSper apply to both the reference and sample data.

### Windowing

The time range in which relevant data needs to be Fourier transformed shall be specified. This can be done manually, or via the auto window function. The auto window has a time range of $(-\Delta t_{1etl} + \Delta t, \Delta t_{1etl})$. This makes sure the reference and sample signal are equally spaced from the auto window's axis of symmetry. In addition, the reference and sample signal are respectively spaced from the start and end of the auto window function at a time equivalent to the additional time taken for one internal reflection.

The selected data does not have a time range that extends over $(- \infty, \infty)$, and may not have an integer number of periods (i.e. the start and end value of the data are different over the specified time range). This may lead to discontinuities in the subsequent Fourier transform results. To mitigate this situation, the selected data should be multiplied with apodisation functions, which gradually tends to zero at both ends. The following lists the available apodisation functions in CaTSper:

- Boxcar: Heaviside step function. The values of the selected data are not changed and hence the function is suitable for transient data.
- [Bartlett](https://uk.mathworks.com/help/signal/ref/bartlett.html): Symmetrical triangular function with zero as the two end values. The value at the triangular peak positively scales with the length of the data. The function length is the same as the data length. It gives little ripple in the results obtained after Fourier transform. 
- [Blackman](https://uk.mathworks.com/help/signal/ref/blackman.html): Summation of three cosine terms. The function is created with a length greater than the data length by one, and then the last value is removed from the function. It is suitable for applications where minimal leakage is required.
- [Hann](https://uk.mathworks.com/help/signal/ref/hann.html): Raised cosine. The two end values are at zero. The function length is one greater than the data length. It is suitable for random signals and is good against spectral leakage.
- [Hamming](https://uk.mathworks.com/help/signal/ref/hamming.html): Raised cosine. The two end values are not at zero. The function length is one greater than the data length. After Fourier transform, the side lobes has a value lower than that of Hann, making Hamming suitable for optimising signal quality. 
- [Taylor](https://uk.mathworks.com/help/signal/ref/taylorwin.html): The MATLAB default settings are used. The coefficients in the function are not normalised. After Fourier transform, it gives a narrow main lobe with side lobe values that decrease monotonically. It is suitable for radar applications.
- [Triangular](https://uk.mathworks.com/help/signal/ref/triang.html): Symmetrical triangular function. If the length of the data has an odd value, the two end values are zero and the triangular peak is at one. If the length is instead even, the two end values are equal to the reciprocal of the length and a plateau, instead of a triangular peak, is resulted. The function length is the same as the data length.

### Fast Fourier Transform

The data is usually upsampled before Fourier transform. Upsampling approximates the situation when the signal is sampled at a higher rate. This is done by extending the data length, where the new length is determined by multiplying the original length of data by a power of two. The exponent is specified by the user and should have a value greater than zero. The additional entries created beyond the original data length are filled with zeros.

The augmented data is then respectively discrete Fourier transformed into frequency domain via [fast Fourier transform (MATLAB built-in function)](https://uk.mathworks.com/help/matlab/ref/fft.html). A $N\text{-by-}N$ transformation matrix is multiplied with the data. $N$ is the length of the augmented data, or the original data length if upsampling is not performed. After Fourier transform, the values in the frequency domain data are divided by the original data length (before upsampling) for scaling. 

### Frequency Range and Spectral Resolution

The frequency domain data will be trimmed according to the user-specified frequency range, which should be set based on considerations such as the instrument's signal-to-noise ratio, the range that gives relevant features, etc. Values beyond the upper limit, which is the cutoff frequency, can be trimmed right after Fourier transform, but those below the lower limit are only trimmed after [phase unwrapping](catsper_function_ref.md#amplitude-and-phase), as otherwise erroneous values may result.

The spectral resolution $v_{res}$ of the frequency-domain data is defined by 

$$ v_{res} = \frac{1}{t_{res} N} $$

where $t_{res}$ is the time resolution of the measured signal in time domain.

### Amplitude and Phase

Amplitude data are the scaled data obtained after fast Fourier transform. Phase data is obtained by unwrapping the frequency domain data. The built-in MATLAB ['unwrap'](https://uk.mathworks.com/help/matlab/ref/unwrap.html) function is adopted as it eliminates discontinuities between consecutive phases by adding multiples of $\pm 2 \pi$ until the difference is less than $\pi$.
Due to the high signal-to-noise ratio at 0.8 THz, it is set as the starting point for unwrapping phase to reduce errors. This is instrument specific and one can change the value accordingly by accessing the 'TDSunwrap' function in the [Catsper.m](https://github.com/CamTHz/catsper/blob/main/Catsper.m) code.
Frequency domain data, that corresponds to frequencies greater than 0.8 THz, will be unwrapped in increasing values starting at 0.8 THz, and vice versa for data corresponding to frequencies less than 0.8 THz.
A straight line is then fitted to unwrapped phase against frequency from 0.05 to 0.4 THz. The intercept of the straight line at 0 THz gives the phase offset. The phase offset is then applied to all phase data for correction.

A step-by-step guide to Fourier transform in CaTSper can be found [here](https://github.com/CamTHz/CamTHz.GitHub.io/blob/main/catsper_tutorial.md#fast-fourier-transformation-fft).

## Frequency Domain Analysis

### Dynamic Range

In CaTSper's DR Filter app, the user can first specify the cutoff frequency $v_{cutoff}$. The noise floor $E_{ref}(v_{cutoff}$ is defined as the reference signal amplitude at $v_{cutoff}$. The dynamic range $\text{DR}$ can thus be defined as

$$ \text{DR}(v) = \frac{E_{ref}(v)}{E_{ref}(v_{cutoff})} $$

where $E_{ref}(v)$ is the amplitude of the reference signal at frequency $v$.

The upper limit frequency can also be specified in CaTSper's DR Filter app so that data at frequencies that are greater than the upper limit frequency will not be considered for analysis in the next steps.

### Transmittance

Transmittance measures the fraction of the terahertz wave that is transmitted through the sample to the detector. The transmission amplitude $T$ is defined as

$$ T(v) = \frac{|E_{sample(v)}|}{|E_{ref(v)}|} $$

where $E_{sample}$ is the frequency domain amplitude of the sample measurement and $E_{ref}$ is the frequency domain amplitude of the reference.

The transmission phase $\theta_T$ is defined as

$$ \theta_T(v) = \theta_{ref}(v) - \theta_{sample}(v) $$

where $\theta_{sample}$ is the frequency domain phase of the sample measurement $\theta_{ref}$ is the frequency domain phase of the reference measurement.

### Refractive Index

Refractive index is a material property which measures the ratio between the speed of light in vacuum to that in the material. Both the refractive index of the reference $n_{ref}$ and the medium $n_{medium}$ are taken as one to match the methods in [Jepsen and Fischer (2005)](https://doi.org/10.1364/OL.30.000029)[^Jepsen&Fischer2005] for subsequent analysis. The frequency-domain effective refractive index $n_{eff,FD}$ of the sample can thus be calculated as

$$ n_{eff,FD}(v) = \frac{c \theta_T(v)}{2 \pi v \Delta H} + 1 $$

where $\Delta H$ is the thickness difference between the sample and the reference.

### Absorption Coefficient

The absorption coefficient $\alpha$ quantifies the extent of loss in terahertz wave intensity through absorption. A higher value indicates higher absorption. The method by [Jepsen and Fischer (2005)](https://doi.org/10.1364/OL.30.000029)[^Jepsen&Fischer2005] is used to calculate $\alpha$.

The reference factor is first determined using 

$$ \text{Reference factor} = \frac{4 n_{medium} n_{ref}}{\left( n_{medium} + n_{ref} \right)^{2}} $$

As discussed earlier, both $n_{medium}$ and $n_{ref}$ take a value of one to match the methods in [Jepsen and Fischer (2005)](https://doi.org/10.1364/OL.30.000029)[^Jepsen&Fischer2005].

The sample factor is similarly defined as 

$$ \text{Sample factor}(v) = \frac{4 n_{medium} n_{eff,FD}(v)}{\left( n_{medium} + n_{eff,FD}(v) \right)^{2}} $$

$\alpha$ is then calculated by

$$ \alpha (v) = -\frac{2}{\Delta H} \ln \left(T(v) \frac{\text{Reference factor}}{\text{Sample factor} (v)}  \right) $$

In CaTSper's DR Filter app, the dynamic range of $\alpha (v)$ can be checked by the maximum absorption coefficient $\alpha_{max} (v)$, which can be calculated by

$$ \alpha_{max} (v) = \frac{2}{H} \ln \left(\text{DR} \times \frac{\text{Reference factor}}{\text{Sample factor}(v)}  \right) $$

which references the method in [Jepsen and Fischer (2005)](https://doi.org/10.1364/OL.30.000029)[^Jepsen&Fischer2005].

### Extinction Coefficient

Similar to $\alpha$, the extinction coefficient $k$ is defined as the extent that terahertz wave can penetrate through the material. A higher value indicates a lower degree of penetration. $k$ is calculated using the Beer-Lambert Law

$$ k(v) = \frac{\alpha(v) c}{4 \pi v} $$

### Dielectric Constant

Permittivity measures the tendency of a material to be polarised by an electric field. The dielectric constant $\kappa$ is defined as the ratio between the permittivity of the material to that of vacuum, which takes a value of one. 
The real and imaginary part of  $\kappa$ is calculated separately by

$$ \text{Re}(\kappa(v)) = n_{eff,FD}(v)^2 - k(v)^2 $$

$$ \text{Im}(\kappa(v)) = 2 n_{eff,FD}(v) k(v) $$

A step-by-step guide to CaTSper's frequency domain analysis can be found [here](https://github.com/CamTHz/CamTHz.GitHub.io/blob/main/catsper_tutorial.md#frequency-domain-fd-tab).

## Data Manipulation

### Finding Peaks

The MATLAB built-in function ['findpeaks'](https://uk.mathworks.com/help/signal/ref/findpeaks.html) is used to identify peaks for a set of selected data (e.g. absorption coefficient $\alpha$) against another (e.g. frequency). A peak is defined such that it has a value greater than its adjacent neighbours or has a value of infinity. A minimum peak [prominence](https://uk.mathworks.com/help/signal/ug/prominence.html) can be specified such that only peaks with prominence greater than that will be recorded.

A step-by-step guide to data manipulation in CaTSper can be found [here](https://github.com/CamTHz/CamTHz.GitHub.io/blob/main/catsper_tutorial.md#data-manipulation-dm-tab).

## Bibliography
[^Jepsen&Fischer2005]: Jepsen, P.U. and Fischer, B.M., 2005. Dynamic range in terahertz time-domain transmission and reflection spectroscopy. _Optics letters, 30_(1), pp.29-31.
