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
1. We will SELECT every column (*) in the IBM_attrition_2021 table
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

Our Attrition report will have a total of 7 figures and center around the features listed above.

### Creating the Visualization(s)
Plotly has several built in functions for different chart types including bar, histogram, and scatter. Explore Plotly's basic chart types [here](https://plotly.com/python/basic-charts/).

Let's break this code into two main components:
1. The commands that communicate to Plotly's software.
    * This is comprised of the software functions and the objects we pass through those functions. Each having varying levels of constraints. For example some functions only work with an object storing values of a specific data type.
2. The logic that governs the code.
    * This is where the creativity in development happens. Given a set of syntax rules, business requirements, and other (often varying) constraints, how can we solve this problem? This is an example of one high-level question that should guide you throughout your development journey.

For Figure 1 we are going to call Plotly which we aliased as px during our imports. This is known as a wrapper, and can be thought of as the way we access Plotly's various functions. We will also call the [histogram](https://plotly.com/python/histograms/) function because we want the y axis to hold the count of our selected features. Here is a list of the function methods we are using to create Figure 1:
* **All of Plotly's graph objects functions (i.e. bar, etc.) require the first object to be the dataframe.** Here we are using a dataframe named column_feature_top10 - this is simply a slice of our full df we initialized above.
* We then specify what column from the dataframe above we want to set as our x axis.
* In plotly, the color parameter is equivalent to a GROUP BY method in SQL / Pandas; here we want to pivot Attrition status across the x axis.
* We will then specify the title of our figure.
```python
# init the figures 
fig1 = px.histogram(
    column_feature_top10,
    x='OverTime',
    color='Attrition',
    barmode='group',
    title="Count of Over Time where Attrition is True")
```
Below is the output for Figure 1 (i.e. our code directly above above). Here we observe that employees who have left the firm are more likely to work over time. The opposite is true for employees who have stayed. This could lead decision makers to question the Firm's over time policy.

![fig1](report_attrition\fig1.png) 

We will construct Figures 2 - 7 in a very similar fashion to figure one. See below.

```python
fig2 = px.histogram(
    column_feature_top10,
    x='BusinessTravel', 
    color='Attrition', 
    barmode='group', 
    title='Level of Business Travel where Attrition is True')

fig3 = px.histogram(
    column_feature_top10, 
    x='JobRole', 
    color='Attrition', 
    barmode='group', 
    title='Headcount by Department Split by Attrition Status')

fig4 = px.histogram(
    df, 
    x='MaritalStatus',
    color='Attrition',
    barmode='group',
    title='Count of Marital Status by Attrition')

fig5 = px.histogram(
    df,
    x="EnvironmentSatisfaction",
    color="Attrition",
    barmode='group',
    title="Environment Satisfaction by Attrition Status")

fig6 = px.bar(
    df.query("Gender=='Female'"),
    x='DailyRate',
    y='Attrition',
    color='Department',
    orientation='h',
    title="Female Employee's Daily Rate by Attrition Status")
```
```python
fig7 = px.bar(
    df.query("Gender=='Male'"),
    x='DailyRate',
    y='Attrition',
    color='Department',
    orientation='h',
    title="Male Employee's Daily Rate by Attrition Status")
```
Now that our figure objects have been created for our report on Attrition, we need to export the images. To accomplish this we will store the figure objects (value), and their respective names (key) into a dictionary that is named figure_dict. 

```python
# store the figure objects into a dictionary  
figure_dict = {
    'fig1':fig1,'fig2':fig2,'fig3':fig3,'fig4':fig4,'fig5':fig5,'fig6':fig6,'fig7':fig7}
```
Because we have multiple figures in our report, we want to use a for loop to programmatically iterate over the *key* and *value* pairs that are stored in our dictionary (see code cell directly above). Our for loop consists of these core components:
1. Key word "for" and "in" to indicate to the program we are leveraging a for loop.
2. The unbound variables i (key) & x (value) that we will use to store the key value pairs inside our dictionary in each iteration.
3. The dictionary we want to iterate over, which is figure_dict. We will also apply the items function to our dictionary to isolate the key-value pairs.
4. The logic inside our for loop prints and saves each figure object stored inside our dictionary.
```python
# loop through the figure dictionary and write image to a png file
for i, x in figure_dict.items():
    x.show()
    x.write_image(f'report_attrition/{i}.png')
```
## Exploratory Analysis of Company Demographics
In order to truly understand attrition, we need to understand the landscape in which it occurs. In order to understand the firm, we need to report on categorical data that will help us paint a holistic picture of the company's demographics. At the highest level, our simple report will focus on the following areas:
1. How profit changes by department and gender.
2. How profit changes holistically across departments.
3. How attrition affects profit across departments.
4. How employee's monthly rate is distributed across department and education field.
5. Find the average profit by department.
6. How profit changes by employee age. 

### Dataframe Transformation
In order to find the profit, we need to preform some calculations on our dataframe. These 'adjustments' we are making to our parent dataframe could be called a) dataframe manipulation or b) data transformation. In order to accomplish these transformations we will be leveraging the functionality of Python's Pandas package. We will do a total of 5 transformations to our parent dataframe.

In step 1 we are grabbing a slice of a few columns of interest in the dataframe named 'df'. Because we are selecting more than 2 column names, we pass the list through double brackets. 

```python
# step 1
# Select the columns of interest from our parent dataframe 'df'
avg_profit = df[['Department','JobRole','MonthlyIncome','MonthlyRate','Attrition','Gender','EducationField','Education','Age','DailyRate']]
```
Now that we have the columns we need stored in the avg_profit dataframe, we can begin calculations. We will initialize a column named MonthlyProfit in our dataframe. The MonthlyProfit column will equal the MonthlyRate minus the MonthlyIncome columns. To do this simple computation we will leverage two methods:
1. **Pandas [apply](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html?highlight=apply#pandas.DataFrame.apply) method:** 
2. **lambda function:** Lambda functions can be thought of as *throw-away* functions. These are especially handy for tasks you only need to accomplish once, like a calculation! The alternative would be to define a custom function (remember there can be multiple ways to solve the same problem). The lambda function is comprised of the unbound variable x, which moves through each row and subtracts the instance of MonthlyRate and the instance of MonthlyIncome. We tell the program we are going to be moving through the columns by indicating the axis as 1. If we wanted to move through certain index positions we would use an axis of 0. For example:

||Axis 1|Axis 1|
|-|-----|-----|
|Axis 0|
|Axis 0|

```python
# step 2
# Creating a Monthly Profit column
avg_profit['MonthlyProfit'] = avg_profit.apply(lambda x: x['MonthlyRate'] - x['MonthlyIncome'],axis=1)
```
```python
# step 3
# Creating a Profit Percentage column
avg_profit['ProfitPCT'] = avg_profit.apply(lambda x: (x['MonthlyProfit'] / sum(avg_profit['MonthlyProfit']))*100, axis=1)

# step 4 
# Creating a Monthly Rate Percentage column
avg_profit['MonthlyRatePCT'] = avg_profit.apply(lambda x: (x['MonthlyRate'] / sum(avg_profit['MonthlyRate'])*100),axis=1)

# step 5
# Find the average profit of each department
mean_profit_slice = avg_profit.groupby(avg_profit['Department']).mean().round(2)
```
```python
# init the figures
fig1 = px.histogram(
    avg_profit,
    x=avg_profit['ProfitPCT'],
    y='Department',
    color='Gender',
    title='Distribution of Profit as Percent by Department and Gender')

fig2 = px.histogram(
    avg_profit,
    x=avg_profit['ProfitPCT'],
    color='Department',
    title='Distribution of Profit as Percent by Department')

fig3 = px.histogram(
    avg_profit,
    x=avg_profit['ProfitPCT'],
    y='Attrition',
    color='Department',
    title='Distribution of Profit as Percent by Attrition and Department')

fig4 = px.histogram(
    avg_profit,
    x="MonthlyRatePCT",
    y='Department',
    color='EducationField',
    title='Monthly Rate as Percentage by Department and Education Field')

fig5 = px.bar(
    mean_profit_slice,
    x=mean_profit_slice.index,
    y='MonthlyProfit',
    title="Average Monthly Profit by Department")

fig6 = px.scatter(
    avg_profit, 
    x="MonthlyProfit",
    y='Age',
    color='Department', 
    size="DailyRate", 
    title="Distribution of Profit by Employee Age")
```
```python
# store the figure objects into a list  
figure_dict = {'fig1':fig1,'fig2':fig2,'fig3':fig3,'fig4':fig4,'fig5':fig5,'fig6':fig6}

# loop through the figure list and print
for i, x in figure_dict.items():
    x.show()
    x.write_image(f'report_company/{i}.png')
```