# Connecting to Google BigQuery (GBQ)

### Authenticate access
This code bit will leverage google's auth method and require users to login to an email address that is registered with privilideges in the cloud project.

``` python
# step 1: imports
from google.colab import auth
from google.cloud import bigquery

# step 2: authentication using the auth method
auth.authenticate_user()
```
### Create a client object that accesses Google Cloud's API
Here we will store the project Id of our Google Cloud instance where our database is located. These Project Id's are required by Google Cloud to be unique; if you try to recreate this project in your development environment, your Id will be different.

Now that our Id is stored inside a variable as a string, we need to create an object that has access to the bigquery package and leverage on of it's methods called Client. We need to pass our project Id variable into this method in order to access the assets stored in that particular instance.

It is the client object that allows us to programatically connect to our cloud warehouse via Google's API.

Please note that connecting to Google Cloud on your local machine is a different task. The largest difference is the need to set an environment variable and pass it your API key; as well as, there is no need to use authentication for local development (VS cloud)
```python
# step three: store Google Cloud project id into a variable
project_id = 'civil-hope-323521'

# step four: create a client object using the bigquery method
client = bigquery.Client(project=project_id)
```
# Extracting data from the wharehouse using SQL
Using our client object we will use the query method to transform the data; since the query method accepts SQL, there is a high level of customizability.

**Note:** in the transformation stage of data processing, it is **best practice to include custom validation functions**. The purpose of these are to ensure that the data being pulled from our cloud warehouse has not been comprised. It is irrelevant to this project, as the data is essentially isolated (i.e. no integration component).

## Deconstructing the SQL querys

### Store the entire dataset into a dataframe
1. We will SELECT every column (*) in the IBM_arrtition_2021 table
2. FROM the project_id.database.table 

We then covert the extracted data into a dataframe using the to_dataframe method.

**Note:** when working with big data, be very particular about using the SELECT * statement. In most cases, when working with big data, selecting every column will download too many GB or TB and can a) bog down runtimes substantially; b) increase cloud resourcing expenses. 

``` python
df = client.query('''
select * 
from `civil-hope-323521.attrition_dataset_1.IBM_attrition_2021` 
''').to_dataframe()
```
### Count the number of employees where attrition equals True and where False
1. SELECT a count aggregation of the attrition column
2. FROM the project_id.database.table 
3. GROUP BY attirion column
``` python
# this will show us if this dataset is an imbalanced classification problem
df = client.query('''
select count(attrition), attrition
from `civil-hope-323521.attrition_dataset_1.IBM_attrition_2021`
group by attrition
''').to_dataframe()
```

