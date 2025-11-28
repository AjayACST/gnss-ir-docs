# Software Guide

## Installation
To install the data processing python package, first clone the repository from GitHub:

```bash
git clone https://github.com/AjayACST/gnss-ir-nz.git
```

Then you will need to install the dependencies. There are only 3 dependeices requires, which can be installed via pip:

```bash
pip install -r requirements.txt
```
The required dependencies are:
- numpy
- matplotlib
- scipy


## Configuration

There are some configuration options that can be set in the `config.ini` file. The options are as follows:
- `pvf`: Polynomial order to use for filtering direct signal SNR data.
- `min_rh`: Minimum reflector height to consider for height retrievals (in meters).
- `min_points`: Minimum number of data points required after filtering by azimuth and elevation to perform height retrieval.
- `max_az_diff`: Maximum azimuth difference (in degrees) between the minimum and maximum to consider.
- `max_height`: Maximum reflector height to consider for height retrievals (in meters).
- `pcrit`: Minimum value for the peak-noise ratio to consider a valid height retrieval.
- `ediff`: Elevation difference (in degrees) between the minimum and maximum to consider.
- `snr_thresh`: Minimum SNR value to consider for height retrievals.
- `av_time`: Average sampling time, used for smoothing function

## Usage
To use the data processing package, run the script `matlab_translate.py`. By default the script is setup to process all data
in the `sample_data` directory. You can change this by modifying the string on line 9
```python
data_path = Path("../sample_data") # change this to your data directory
```

Once you have modified the script to your liking, run the script with python:
```bash
python matlab_translate.py
```
Once started the script will first ask what files you would like to process. To match multiple files use an * as a wildcard.
For example `250520*.LOG` will match only the files that start with `250520` and end with `.LOG` will be processed. So this will match
files generated on the 20th of May 2025. See [Build Tutorial Data Files](Build-Tutorial-v1.1.md#data-files) for more
information on the file naming convention.

The script will then ask you if you would like to use the default azimuth range bins of 0-90, 90-180, 180-270, 270-360 degrees.
You can choose to use these default bins, or specify your own custom azimuth bins, of which you can have up to 4 bins.
After the Azimuths it will then ask for the minimum and maximum elevation angles to consider for height retrievals.

Fianlly, the script will start processing all matching files in the specified directory. Once it has finished doing this it will prompt you for 
what type of graphs to output. See below for more information on the graph options.

## Graph Options
Once the data processing script has finished processing the data files, it will prompt you for what type of graphs to output.
You can choose from the following options:
1. Graph showing height retrievals by Azimuth region for a single day.
2. Graph showing retrieval metrics for a single day.
3. Graph showing height retrievals over time

For options 1 and 2, you will also be prompted to enter the date of the data you wish to graph in the format `YYYY-MM-DD`.

### Height Retrieval by Azimuth Region
![plot-1-azimuth-region.png](plot-1-azimuth-region.png)
This graph shows the height retrievals for a single day, grouped by azimuth region. The top left graph represents 0-90 
degrees, the top right 90-180 degrees and so on. This graph is from our sample data from the 22nd of May 2025.

### Retrieval Metrics for a Single Day
![plot-2-retrieval-metrics.png](plot-2-retrieval-metrics.png)
This graph shows the retrieval metrics for a single day. The top graph shows the reflector height by the azimuth degree,
the middle graph shows the peak amplitude by azimuth degree, and the bottom graph shows the peak to noise ratio. This graph is from our sample data from the 22nd of May 2025.

### Azimuth vs Elevation Polar Plot
![plot-3-polar-plot.png](plot-3-polar-plot.png)
This graph shows a polar plot of azimuth vs elevation for all height retrievals on a single day. This graph is from our sample data from the 22nd of May 2025.
### Height Retrievals Over Time
![plot-4-height-retrievals-over-time](plot-4-height-retrievals-over-time.png)
This graph shows the height retrievals by day for all days processed. Each white dot represents a height retrieval, and
the blue dot represents the average height retrieval for that day. This graph is from our sample data from the 20th to the 25th of May 2025.