# Documentation
The CaTSper tool extracts the frequency-dependent optical constants from terahertz time-domain waveforms. It is intended to serve as a simple-to-use but powerful tool for the terahertz time-domain spectroscopy (THz-TDS) community.

* TOC
{:toc}

## Basic Workflow
The process of extracting the spectra from the raw time-domain data involves two separate steps:

### cambridgeTHzconverter 
Collating the raw sample and reference waveform(s) into a single standardised .thz file. Additional metadata is captured in this file, including, for example, sample thickness, temperature or other contextual information. 

![catsperMATconverter main GUI](/images/catsper_converter_main_gui_new.png)

### catsper
The main CaTSper tool is designed to read the .thz file and carry out all subsequent signal processing steps. The tool can be used to display the time-domain waveform, apply any required truncation to the waveforms, select and apply suitable Fourier transformation parameters and output of the spectra.

![catsper main GUI](/images/catsper_main_gui_new.png)

## Detailed Information
- [CaTSper .thz Converter](/CatsperConverter.md)
- [CaTSper step-by-step tutorial](/catsper_tutorial.md)
- [CaTSper detailed description of processing steps](/catsper_function_ref.md)
- [The .thz file format](/thz_file_format.md)
