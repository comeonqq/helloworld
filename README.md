# helloworld
import numpy as np  # For computations
import pandas as pd  # For indexing our data

np.random.seed(12345)
import matplotlib.pyplot as plt  # for visualizing our data

plt.rc('figure', figsize=(10, 6))
PREVIOUS_MAX_ROWS = pd.options.display.max_rows
pd.options.display.max_rows = 20
np.set_printoptions(precision=4, suppress=True)

# Our temporal data types
from datetime import datetime
from datetime import timedelta

# For grabbing data sets
import requests

# For hiding code blocks, as they aren't necessary at this point
from IPython.display import HTML


#Code setup to download data sets
def download_file(url):
    local_filename = url.split('/')[-1]
    r = requests.get(url, stream=True)
    with open(local_filename, 'wb') as f:
        for chunk in r.iter_content(chunk_size=1024): 
            if chunk: 
                f.write(chunk)
    return local_filename
# Get GDP Data
csvUrl = "https://raw.githubusercontent.com/datasets/gdp/master/data/gdp.csv"
csvFile = download_file(csvUrl)
csvFile = "gdp.csv"

df = pd.read_csv(csvFile)
canDFgdp = df[df.loc[:]["Country Name"] == "Canada"]

# Get Population Data
csvUrl = "https://raw.githubusercontent.com/datasets/population/master/data/population.csv"
csvFile = download_file(csvUrl)
csvFile = "population.csv"
df2 = pd.read_csv(csvFile)
canDFpop = df2[df2.loc[:]["Country Name"] == "Canada"]

#Read time correctly
canDFgdp.iloc[:]["Year"] = pd.to_datetime(canDFgdp.iloc[:]["Year"], format='%Y')
canDFpop.iloc[:]["Year"] = pd.to_datetime(canDFpop.iloc[:]["Year"], format='%Y')


#Calculate GDP per person
canDFgdpPerPerson = canDFgdp.merge(canDFpop, on="Year")
canDFgdpPerPerson["GDP Per Person (USD)"] = (canDFgdpPerPerson["Value_x"]).divide(canDFgdpPerPerson["Value_y"])
canDFgdpPerPerson["GDP (USD)"] = canDFgdpPerPerson["Value_x"]
canDFgdpPerPerson["Population Size (# of people)"] = canDFgdpPerPerson["Value_y"]

##Plot

#GDP
canDFgdpPerPersonPlot = canDFgdpPerPerson.plot(x="Year", y="GDP (USD)")
canDFgdpPerPersonPlot.set_title("GDP vs. Time")
canDFgdpPerPersonPlot.set_ylabel("Dollars (USD)")
canDFgdpPerPersonPlot.set_xlabel("Time (Years)")
canDFgdpPerPersonPlot.plot()
# population
canDFgdpPerPersonPlot = canDFgdpPerPerson.plot(x="Year", y="Population Size (# of people)")
canDFgdpPerPersonPlot.set_title("Population vs. Time")
canDFgdpPerPersonPlot.set_ylabel("Population Size (# of people)")
canDFgdpPerPersonPlot.set_xlabel("Time (Years)")
canDFgdpPerPersonPlot.plot()
# GDP per person 
# **NOTE:** Pay attention to the scales (top-left-corner of plot), and not just the y-values
canDFgdpPerPersonPlot = canDFgdpPerPerson.plot(x="Year", y="GDP Per Person (USD)")
canDFgdpPerPersonPlot.set_title("GDP Per Person vs. Time")
canDFgdpPerPersonPlot.set_ylabel("GDP Per Person (USD)")
canDFgdpPerPersonPlot.set_xlabel("Time (Years)")
canDFgdpPerPersonPlot.plot()

