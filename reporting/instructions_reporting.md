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

## Loading in Data

## Exploratory Analysis of Attrition

