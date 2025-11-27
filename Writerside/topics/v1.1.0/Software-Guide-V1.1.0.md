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
- pandas
- matplotlib
- scipy


## Configuration

There are some configuration options that can be set in the `config.ini` file. The options are as follows:
- `pvf`: Polynomial order to use for filtering direct signal SNR data.
- `min_rh`: Minimum reflector height to consider for height retrievals (in meters).
- `min_points`: Minimum number of data points required after filtering by azimuth and elevation to perform height retrieval.
- `max_az_diff`: Maximum azimuth difference (in degrees) between the minimum and maximum to consider.
- `max_height`: Maximum reflector height to consider for height retrievals (in meters).
- `desired_precision`: Desired precision for use in the get_ofac_hifac function.
- `pcrit`: Minimum value for the peak-noise ratio to consider a valid height retrieval.
- `emin`: Minimum elevation angle (in degrees) to consider for height retrievals.
- `emax`: Maximum elevation angle (in degrees) to consider for height retrievals.
- `ediff`: Elevation difference (in degrees) between the minimum and maximum to consider.
- `cf`: L1 GPS carrier frequency (in Hz).
- `snr_thresh`: Minimum SNR value to consider for height retrievals.
- `sampling_interval`: How many seconds between each SNR data point.
- `av_time`: Average sampling time, used for smoothing function

## Usage
To use the data processing package, run the script `matlab_translate.py`. By default the script is setup to process all data
in the `data/farm` directory. You can change this by modifying the string on line 9
```python
data_path = Path("../sample_data/farm") # change this to your data directory
```
You can also change the what files are processed by modifying the input string on line 10:
```python
files_path = data_path.rglob("250520*.LOG") # change this to your file pattern
```
In this example, only files that start with `250520` and end with `.LOG` will be processed. So this will match
files generate on the 20th of May 2025. See [Build Tutorial Data Files](Build-Tutorial-v1.1.md#data-files) for more
information on the file naming convention.

Once you have modified the script to your liking, run the script with python:
```bash
python matlab_translate.py
```
This will process all matching files in the specified directory. Once it has finished doing this it will prompt you for 
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
and the bottom graph shows the peak amplitude by azimuth degree. This graph is from our sample data from the 22nd of May 2025.

### Height Retrievals Over Time
![plot-3-height-retrievals-over-time.png](plot-3-height-retrievals-over-time.png)
This graph shows the height retrievals by day for all days processed. Each white dot represents a height retrieval, and
the blue dot represents the average height retrieval for that day. This graph is from our sample data from the 20th to the 25th of May 2025.