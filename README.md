# Documentation
The CaTSper tool extracts the frequency-dependent optical constants from terahertz time-domain waveforms. It is intended to serve as a simple-to-use but powerful tool for the terahertz time-domain spectroscopy (THz-TDS) community.

<img src="/images/dotTHzProject_logo.png" height ="150">

**Table of Contents**
- Basic Workflow
* CaTx (Cambridge THz Converter)
+ CaTSper (Cambridge THz Spectrum Analyser)

>[!NOTE]
> Please download CaTx and CaTSper packages at the following links. The packages are named in 'Install_releaseDate.zip' format.

  [**CaTx Source Code Page**](https://github.com/dotTHzTAG/catx)

  [**CaTSper Source Code Page**](https://github.com/dotTHzTAG/CaTSper)

## Basic Workflow
The process of extracting the spectra from the raw time-domain data involves two separate steps:

![Workflow schematic](/images/workFlow.png)

### CaTx (Cambridge THz Converter) 
Collating the raw sample and reference waveform(s) into a single standardised [.thz file](/thz_file_format.md). Additional metadata is captured in this file, including, for example, sample thickness, temperature or other contextual information. A simple CaTx tutorial can be found [here](/CaTx_tutorial.md).

![catx main GUI](/images/catx_gui.png)

### CaTSper (Cambridge THz Spectrum Analyser)
The main CaTSper tool is designed to read the .thz file and carry out all subsequent signal processing steps. The tool can be used to display the time-domain waveform, apply any required truncation to the waveforms, select and apply suitable Fourier transformation parameters and output of the spectra. Please find the [CaTSper tutorial](/catsper_tutorial.md) and additional explanation on the [mathematical steps](/catsper_function_ref.md) for data processing using CaTSper.

![catsper main GUI](/images/catsper_gui.png)

## Detailed Information
- [CaTx (CambridgeTHzConverter) tutorial](/CaTx_tutorial.md)
- [CaTSper step-by-step tutorial](/catsper_tutorial.md)
- [CaTSper detailed description of processing steps](/catsper_function_ref.md)
- [The .thz file format](/thz_file_format.md)
