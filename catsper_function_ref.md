# CaTSper Processing Steps: A Detailed Description

This document aims to provide a detailed description to the scientific basis of the processing steps involved in [CaTSper](https://github.com/dotTHzTAG/CaTSper). A step-by-step guide to using CaTSper is available [here](https://github.com/dotTHzTAG/Documentation/blob/main/catsper_tutorial.md).
<!-- A detailed in-line annotation of CaTSper's code is available here. -->

## Time-Domain Analysis

The time delay $\Delta t$ is the extra time needed for the THz pulse to traverse through the sample thickness $H$, compared to the THz pulse traversing the same thickness in the reference measurement (air, refractive index $n_{\text{ref}} = 1$). The time-domain effective refractive index $n_{\text{eff,TD}}$ of the sample is thus calculated by

$$ n_{\text{eff,TD}} = \frac{c \Delta t}{H} + 1 $$

where $c$ is the speed of light with a value of $3 \times 10^8$ ms $^{-1}$. $n_{\text{eff}}$ is calculated to four significant figures.

The time delay due to one internal reflection occurring $\Delta t_{1\text{etl}}$ is also considered. For one internal reflection, the THz pulse additionally travels through two times the sample thickness, compared to the original $\Delta t$. $\Delta t_{1\text{etl}}$ is thus calculated by

$$ \Delta t_{1\text{etl}} = \Delta t + \frac{2H n_{\text{eff}}}{c}$$

A step-by-step guide to CaTSper's time-domain analysis in CaTSper can be found [here](https://github.com/dotTHzTAG/Documentation/blob/main/catsper_tutorial.md#time-domain-td-tab).  

## Fourier Transform

The following and the user-selected processing options in CaTSper apply to both the reference and sample data.

### Windowing

The time range in which relevant data needs to be Fourier transformed shall be specified. This can be done manually, or via the auto window function. The auto window has a time range of $(-\Delta t_{1\text{etl}} + \Delta t, \Delta t_{1\text{etl}})$. This makes sure the reference and sample signal are equally spaced from the auto window's axis of symmetry. In addition, the reference and sample signal are respectively spaced from the start and end of the auto window function at a time equivalent to the additional time taken for one internal reflection.

The selected data does not have a time range that extends over $(- \infty, \infty)$, and may not have an integer number of periods (i.e. the start and end value of the data are different over the specified time range). This may lead to discontinuities in the subsequent Fourier transform results. To mitigate this situation, the selected data should be multiplied with apodisation functions, which gradually tends to zero at both ends. The common apodisation functions used in terahertz spectroscopy analysis include [rectangular](https://uk.mathworks.com/help/signal/ref/rectwin.html), [Hann](https://uk.mathworks.com/help/signal/ref/hann.html) and [Hamming](https://uk.mathworks.com/help/signal/ref/hamming.html). The following lists the available apodisation functions in CaTSper:
 
- [Barthann](https://uk.mathworks.com/help/signal/ref/barthannwin.html): Linear combination of weighted [Bartlett](https://uk.mathworks.com/help/signal/ref/bartlett.html) and [Hann](https://uk.mathworks.com/help/signal/ref/hann.html) apodisation functions. The function is triangular with cosine edges to reduce discontinuity in signals. It is suitable in cases where a balance of spectral leakage control and frequency resolution is required.
- [Blackman](https://uk.mathworks.com/help/signal/ref/blackman.html): Summation of two cosine terms. The function is created with a length greater than the data length by one, and then the last value is removed from the function. It is suitable for applications where minimal leakage is required.
- [Blackman Harris](https://uk.mathworks.com/help/signal/ref/blackmanharris.html): Summation of three cosine terms. Compared to [Blackman](https://uk.mathworks.com/help/signal/ref/blackman.html), [Blackman Harris](https://uk.mathworks.com/help/signal/ref/blackmanharris.html) can further reduce side lobe levels in frequency domain. It is suitable in cases where minimising spectral leakage is important.
- [Bohnman](https://uk.mathworks.com/help/signal/ref/bohmanwin.html): Summation of a sine term, and a product consisting a linear triangular function and cosine term. It offers smooth tapering but a steeper transition to zeros at the edges.
- [Chebyshev](https://uk.mathworks.com/help/signal/ref/chebwin.html): Product of the Chebyshev polynomial and a cosine term, which is used to control oscillations in frequency response. It reduces side lobe levels to minimise spectral leakage.
- [Flattop](https://uk.mathworks.com/help/signal/ref/flattopwin.html): Summation of a series of cosines. Before returning to zero at the edges, parts of the apodisation function are negative to minimise scalloping loss. It preserves the amplitude of sinusoidal components, but the broad bandwidth leads to a noiser bandwith and lower frequency resolution.
- [Gaussian](https://uk.mathworks.com/help/signal/ref/gausswin.html): Gaussian function. As the Gaussian function spans $\[-\infty,\infty\]$, the Gaussian apodisation function does not end at zero at the edges. It is a general-purpose apodisation function used for smoothing signals whilst preserving key spectral features.
- [Hann](https://uk.mathworks.com/help/signal/ref/hann.html): Raised cosine. The two end values are at zero. The function length is one greater than the data length. It is suitable for random signals and is good against spectral leakage. This is one of the common apodisation functions used in terahertz spectroscopy analysis.
- [Hamming](https://uk.mathworks.com/help/signal/ref/hamming.html): Raised cosine. The two end values are not at zero. The function length is one greater than the data length. After Fourier transform, the side lobes has a value lower than that of Hann, making Hamming suitable for optimising signal quality. This is one of the common apodisation functions used in terahertz spectroscopy analysis.
- [Kaiser](https://uk.mathworks.com/help/signal/ref/kaiser.html): Ratio of 2 zeroth order modified Bessel function. It emphasises the main lobe, so it is most suitable for applications associated with finite impulse response. 
- [Nuttall](https://uk.mathworks.com/help/signal/ref/nuttallwin.html): Summation of three cosine terms. Nuttall only differs from [Blackman Harris](https://uk.mathworks.com/help/signal/ref/blackmanharris.html) by the coefficient values and hence slightly lower sidelobes. It minimises spectral leakage and so it is suitable for cases where distinguishing closely adjacent frequency components are important.
- [Parzen](https://uk.mathworks.com/help/signal/ref/parzenwin.html): Piecewise cubic functions approximated from the Gaussian apodisation function. It offers good smoothing and minimises spectral leakage, but has a lower frequency resolution due to a relatively wider main lobe.
- [Rectangular](https://uk.mathworks.com/help/signal/ref/rectwin.html): Similar to heaviside step function. All points within the apodisation function take a value of 1 and those outside take a value of 0. The values of the selected data are thus unchanged and hence the function is suitable for transient data.  This is one of the common apodisation functions used in terahertz spectroscopy analysis.
- [Taylor](https://uk.mathworks.com/help/signal/ref/taylorwin.html): The MATLAB default settings are used. The coefficients in the function are not normalised. After Fourier transform, it gives a narrow main lobe with side lobe values that decrease monotonically. It is suitable for radar applications.
- [Triangular](https://uk.mathworks.com/help/signal/ref/triang.html): Symmetrical triangular function. If the length of the data has an odd value, the two end values are zero and the triangular peak is at one. If the length is instead even, the two end values are equal to the reciprocal of the length and a plateau, instead of a triangular peak, is resulted. The function length is the same as the data length.
- [Tukey](https://uk.mathworks.com/help/signal/ref/tukeywin.html): Tapered cosine function. Half of the apodisation function centred around the mid-point is defined as 1, and both sides descends via a cosine function to zero at the edges of the apodisation function. It is useful for data tapering where the original signal needs to be preserved but edge effects need to be reduced.

### Fast Fourier Transform

The data is usually upsampled before Fourier transform. Upsampling approximates the situation when the signal is sampled at a higher rate. This is done by extending the data length, where the new length is determined by multiplying the original length of data by a power of two. The exponent is specified by the user and should have a value greater than zero. The additional entries created beyond the original data length are filled with zeros.

The augmented data is then respectively discrete Fourier transformed into frequency domain via [fast Fourier transform (MATLAB built-in function)](https://uk.mathworks.com/help/matlab/ref/fft.html). A $N\text{-by-}N$ transformation matrix is multiplied with the data. $N$ is the length of the augmented data, or the original data length if upsampling is not performed. After Fourier transform, the values in the frequency domain data are divided by the original data length (before upsampling) for scaling. 

### Frequency Range and Spectral Resolution

The frequency domain data will be trimmed according to the user-specified frequency range, which should be set based on considerations such as the instrument's signal-to-noise ratio, the range that gives relevant features, etc. Values beyond the upper limit, which is the cutoff frequency, can be trimmed right after Fourier transform, but those below the lower limit are only trimmed after [phase unwrapping](catsper_function_ref.md#amplitude-and-phase), as otherwise erroneous values may result.

The spectral resolution $\nu_{\text{res}}$ of the frequency-domain data is defined by 

$$ \nu_{\text{res}} = \frac{1}{t_{\text{res}} N} $$

where $t_{\text{res}}$ is the time resolution of the measured signal in time domain.

### Amplitude and Phase

Amplitude data are the scaled data obtained after fast Fourier transform. Phase data is obtained by unwrapping the frequency domain data. The built-in MATLAB ['unwrap'](https://uk.mathworks.com/help/matlab/ref/unwrap.html) function is adopted as it eliminates discontinuities between consecutive phases by adding multiples of $\pm 2 \pi$ until the difference is less than $\pi$.
Due to the high signal-to-noise ratio at 0.8 THz, it is set as the starting point for unwrapping phase to reduce errors. This is instrument specific and one can change the value accordingly by accessing the 'TDSunwrap' function in the [Catsper.m](https://github.com/CamTHz/catsper/blob/main/Catsper.m) code.
Frequency domain data, that corresponds to frequencies greater than 0.8 THz, will be unwrapped in increasing values starting at 0.8 THz, and vice versa for data corresponding to frequencies less than 0.8 THz.
A straight line is then fitted to unwrapped phase against frequency from 0.05 to 0.4 THz. The intercept of the straight line at 0 THz gives the phase offset. The phase offset is then applied to all phase data for correction.

A step-by-step guide to Fourier transform in CaTSper can be found [here](https://github.com/dotTHzTAG/Documentation/blob/main/catsper_tutorial.md#frequency-domain-fd-tab).

## Frequency-Domain Analysis

### Dynamic Range

In CaTSper's DR Filter app, the user can first specify the cutoff frequency $\nu_{\text{cutoff}}$, beyond which the signals are mostly noise. The noise floor $E_{\text{ref}}(\nu_{\text{cutoff}}$ is defined as the reference signal amplitude at $\nu_{\text{cutoff}}$. The dynamic range $\text{DR}$ can thus be defined as

$$ \text{DR}(\nu) = \frac{E_{\text{ref}}(\nu)}{E_{\text{ref}}(\nu_{\text{cutoff}})} $$

where $E_{\text{ref}}(\nu)$ is the amplitude of the reference signal at frequency $\nu$. Details on the application of dynamic range are described in the later section of [absorption coefficient](#absorption-coefficient).

The upper limit frequency can also be specified in CaTSper's DR Filter app so that data at frequencies that are greater than the upper limit frequency will not be considered for analysis in the next steps.

### Transmittance

Transmittance measures the fraction of the terahertz wave that is transmitted through the sample to the detector. The transmission amplitude $T$ is defined as

$$ T(\nu) = \frac{|E_{\text{sample}(\nu)}|}{|E_{\text{ref}(\nu)}|} $$

where $E_{\text{sample}}$ is the frequency domain amplitude of the sample measurement and $E_{\text{ref}}$ is the frequency domain amplitude of the reference.

The transmission phase $\phi_T$ is defined as

$$ \phi_T(\nu) = \phi_{\text{ref}}(\nu) - \phi_{\text{sample}}(\nu) $$

where $\phi_{\text{sample}}$ is the frequency domain phase of the sample measurement $\phi_{\text{ref}}$ is the frequency domain phase of the reference measurement.

### Refractive Index

Refractive index is a material property which measures the ratio between the speed of light in vacuum to that in the material. Both the refractive index of the reference $n_{\text{ref}}$ and the medium $n_{\text{medium}}$ are taken as one to match the methods in [Jepsen and Fischer (2005)](https://doi.org/10.1364/OL.30.000029)[^Jepsen&Fischer2005] for subsequent analysis. The frequency-domain effective refractive index $n_{\text{eff,FD}}$ of the sample can thus be calculated as

$$ n_{\text{eff,FD}}(\nu) = \frac{c \phi_T(\nu)}{2 \pi v \Delta H} + 1 $$

where $\Delta H$ is the thickness difference between the sample and the reference.

### Absorption Coefficient

The absorption coefficient $\alpha$ quantifies the extent of loss in terahertz wave intensity through absorption. A higher value indicates higher absorption. The method by [Jepsen and Fischer (2005)](https://doi.org/10.1364/OL.30.000029)[^Jepsen&Fischer2005] is used to calculate $\alpha$.

The reference factor is first determined using 

$$ \text{Reference factor}(\nu) = \frac{4 n_{\text{medium}} n_{\text{ref}}}{\left( n_{\text{medium}} + n_{\text{ref}} \right)^{2}} $$

As discussed earlier, both $n_{\text{medium}}$ and $n_{\text{ref}}$ take a value of one to match the methods in [Jepsen and Fischer (2005)](https://doi.org/10.1364/OL.30.000029)[^Jepsen&Fischer2005].

The sample factor is similarly defined as 

$$ \text{Sample factor}(\nu) = \frac{4 n_{\text{medium}} n_{\text{eff,FD}}(\nu)}{\left( n_{\text{medium}} + n_{\text{eff,FD}}(\nu) \right)^{2}} $$

$\alpha$ is then calculated by

$$ \alpha (\nu) = -\frac{2}{\Delta H} \ln \left(T(\nu) \frac{\text{Reference factor}(\nu)}{\text{Sample factor} (\nu)}  \right) $$

In CaTSper's DR Filter app, the user-specified noise floor defines the [dynamic range](#dynamic-range). The  [dynamic range](#dynamic-range) is then used to calculate the maximum absorption coefficient at each frequency, $\alpha_{max} (\nu)$, by

$$ \alpha_{max} (\nu) = \frac{2}{H} \ln \left(\text{DR} \times \frac{\text{Reference factor}(\nu)}{\text{Sample factor}(\nu)}  \right) $$

which references the method in [Jepsen and Fischer (2005)](https://doi.org/10.1364/OL.30.000029)[^Jepsen&Fischer2005]. $\alpha_{max} (\nu)$ specifies the maximum threshold of $\alpha$ which is valid for analysis at each $\nu$. For $\alpha (\nu) > \alpha_{max} (\nu)$, the $\alpha (\nu)$ at the relevant $\nu$ are convoluted with noise and are therefore unreliable for analysis. Users can check their $\alpha (\nu)$ against $\alpha_{max} (\nu)$ in CaTSper's DR Filter app.

### Extinction Coefficient

Similar to $\alpha$, the extinction coefficient $\kappa$ is defined as the extent that terahertz wave can penetrate through the material. A higher value indicates a lower degree of penetration. $\kappa$ is calculated using the Beer-Lambert Law

$$ \kappa(\nu) = \frac{\alpha(\nu) c}{4 \pi v} $$

### Dielectric Constant

Permittivity measures the tendency of a material to be polarised by an electric field. The dielectric constant $\varepsilon$ is defined as the ratio between the permittivity of the material to that of vacuum, which takes a value of one. 
The real and imaginary part of  $\varepsilon$ is calculated separately by

$$ \text{Re}(\varepsilon(\nu)) = n_{\text{eff,FD}}(\nu)^2 - \kappa(\nu)^2 $$

$$ \text{Im}(\varepsilon(\nu)) = 2 n_{\text{eff,FD}}(\nu) \kappa(\nu) $$

A step-by-step guide to CaTSper's frequency domain analysis can be found [here](https://github.com/dotTHzTAG/Documentation/blob/main/catsper_tutorial.md#frequency-domain-fd-tab).

## Data Manipulation

### Finding Peaks

The MATLAB built-in function ['findpeaks'](https://uk.mathworks.com/help/signal/ref/findpeaks.html) is used to identify peaks for a set of selected data (e.g. absorption coefficient $\alpha$) against another (e.g. frequency). A peak is defined such that it has a value greater than its adjacent neighbours or has a value of infinity. A minimum peak [prominence](https://uk.mathworks.com/help/signal/ug/prominence.html) can be specified such that only peaks with prominence greater than that will be recorded.

A step-by-step guide to data manipulation in CaTSper can be found [here](https://github.com/dotTHzTAG/Documentation/blob/main/catsper_tutorial.md#data-manipulation-dm-tab).

## Bibliography
[^Jepsen&Fischer2005]: Jepsen, P.U. and Fischer, B.M., 2005. Dynamic range in terahertz time-domain transmission and reflection spectroscopy. _Optics letters, 30_(1), pp.29-31.
