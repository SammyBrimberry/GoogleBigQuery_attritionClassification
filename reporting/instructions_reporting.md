# Installing Product Dependencies Using PyViz (Recommended)
 PyViz is a Python visualization package that provides a single platform to access multiple visualization packages, including Matplotlib, Plotly Express, hvPlot, Panel, D3.js, etc.. We will be primarily leveraging the Plotly package for this product; but the PyViz environment will give you more flexibility when developing data products for your client.

Follow the steps below to install and set up PyViz in your Python environment.

**Note:** Make sure that you are using your conda environment that has anaconda installed. Create a new environment by using:

```python
conda update anaconda
conda create -n pyvizenv python=3.7 anaconda -y
conda activate pyvizenv
```
Follow the next steps to install PyViz and all its dependencies in your Python virtual environment.

1. Download the PyViz dependencies nodejs and npm (included in nodejs). **Note:** We will never directly interact with this package.
    ```python
    conda install -c conda-forge nodejs=12 -y
    ```

2. Using Conda's package manager, run the install command for the following packages: a) PyViz HoloViz, b) Plotly

    ```python
    conda install -c pyviz holoviz -y
    conda install -c plotly plotly -y

    ```
3. Use the pip package manager to depreciate to the correct versions of matplotlib and numpy
    ```python
    pip install numpy==1.19
    pip install matplotlib==3.0.3
    ```
4. If you are using the Jupyter Lab IDE, there are additional steps needed for installation. PyViz installation also requires the installation of Jupyter Lab extensions. These extensions are used to render PyViz plots in Jupyter Lab. Execute the below commands to install the necessary Jupyter Lab extensions for PyViz and Plotly Express.

    ```python
    conda install -c conda-forge jupyterlab=2.2 -y
    ```
    Set NODE_OPTIONS to prevent "JavaScript heap out of memory" errors during extension installation:
    ```python
    # (Windows)
    set NODE_OPTIONS=--max-old-space-size=4096
    ```
    Install the Jupter Lab extensions
    ```python
    jupyter labextension install @jupyter-widgets/jupyterlab-manager --no-build

    jupyter labextension install jupyterlab-plotly --no-build

    jupyter labextension install plotlywidget --no-build

    jupyter labextension install @pyviz/jupyterlab_pyviz --no-build
    ```
    Build the extensions
    ```python
    jupyter lab build
    ```
    After the build, unset the node options that we used above
    ```python
    # Unset NODE_OPTIONS environment variable
    # (Windows)
    set NODE_OPTIONS=
    ```
# Simple Install
If installing the PyViz platform onto your machine isn't strategic, we can still run the reporting product using just the Plotly package.

Follow the steps below to install and set up Plotly in your Python environment.

**Note:** Make sure that you are using your conda environment that has anaconda installed. Create a new environment by using:

```python
conda update anaconda
conda create -n plotly python=3.7 anaconda -y
conda activate plotly
```
1. Using the conda/pip package manager please install Plotly
    ``` python
    conda install -c plotly plotly=5.3.1
    ```
2. Check if the installation was successful 
    ```python
    conda list plotly
    ```
# Interactive Reporting Tutorial
In this tutorial we will review how to create interactive charts using a pythonic package from our PyViz platform, Plotly. 

**Note:** Plotly also has methods for R and JavaScript, making it a versatile and flexible open source solution that can be deployed across many business units.

Plotly is one of the best open source reporting tools on the market. Plotly charts are interactive, allowing users to hover over values, zoom in and out of graphs, and identify outliers. Built on top of plotly.js, plotly.py is a high-level, declarative charting library. plotly.js ships with over 30 chart types, including scientific charts, 3D graphs, statistical charts, SVG maps, financial charts, and more.

## Setting our Environment Variable
An environment variable is a variable whose value is set outside the program, typically through functionality built into the operating system or microservice. An environment variable is made up of a name/value pair, and any number may be created and available for reference at a point in time.

Environment variables are helpful because they allow you to change what environment(s) use what third party service by changing an API key, endpoint, etc.

In order to set our match our env variable to our Goolge BigQuery credentials .json file (**AKA our API Key**) we need to leverage another python module called OS. This comes preinstalled with python. The OS module provides a portable way of using operating system dependent functionality.

From that module, we will be using the .environ method. Read the documentation [here](https://docs.python.org/3/library/os.html#module-os).

**Note:** you can pass an r in front of string variables to turn them into a raw string. This treats characters such as backslash (‘\’) as a literal character. This also means that this character will not be treated as a escape character.

```python
import os
os.environ["GOOGLE_APPLICATION_CREDENTIALS"]=r"C:\Users\path\to\auth.json"
```
## ETL using SQL and Python Jobs
### Create a client object that accesses Google Cloud's API
Here we will store the project Id of our Google Cloud instance where our database is located. These Project Id's are required by Google Cloud to be unique; if you try to recreate this project in your development environment, your Id will be different.

Now that our Id is stored inside a variable as a string, we need to create an object that has access to the bigquery package and leverage on of it's methods called Client. We need to pass our project Id variable into this method in order to access the assets stored in that particular instance.

It is the client object that allows us to programmatically connect to our cloud warehouse via Google's API.

Please note that connecting to Google Cloud on your local machine is a different task. The largest difference is the need to set an environment variable and pass it your API key; as well as, there is no need to use authentication for local development (VS cloud)
```python
# store Google Cloud project id into a variable
project_id = 'civil-hope-323521'

# create a client object using the bigquery method
client = bigquery.Client(project=project_id)
```
### Extracting data from the warehouse using SQL
Using our client object we will use the query method to transform the data; since the query method accepts SQL, there is a high level of customizability.

**Note:** in the transformation stage of data processing, it is **best practice to include custom validation functions**. The purpose of these are to ensure that the data being pulled from our cloud warehouse has not been comprised. It is irrelevant to this project, as the data is essentially isolated (i.e. no integration component).

### Store the entire dataset into a dataframe
1. We will SELECT every column (*) in the IBM_arrtition_2021 table
2. FROM the project_id.database.table 

We then covert the extracted data into a dataframe using the to_dataframe method.

**Note:** when working with big data, be very particular about using the SELECT * statement. In most cases, when working with big data, selecting every column will download too many GB or TB and can a) bog down runtime substantially; b) increase cloud resourcing expenses. 

```python
# query our entire data living in google cloud
df = client.query('''
select * 
from `civil-hope-323521.attrition_dataset_1.IBM_attrition_2021` # project_id.database.table
''').to_dataframe()

# show the first 5 rows
df.head()
```
## Exploratory Analysis of Attrition
After doing Principal Component Analysis (PCA) on our data set, we found that the top features responsible for predicting when Attrition is **True** are: 
1. Over Time 
2. Job Role
3. Business Travel
4. Distance From Home
5. Marital Status
6. Number of Companies worked
These features will be what our report centers around.

Our Attrition report will have a total of 7 figures.