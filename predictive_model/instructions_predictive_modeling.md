# Predictive Modeling

### Imports 
It is common practice to place all the imports at the top of a file. Listed below are all the libraries and tools that will be leveraged in this section. 

``` python
import pandas as pd
import numpy as np
import io
import plotly.express as px
import matplotlib.pyplot as plt
import seaborn as sns
from imblearn.over_sampling import SMOTE
import warnings
warnings.filterwarnings('ignore')
import pandas as pd
import numpy as np
from collections import Counter
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import balanced_accuracy_score
from sklearn.metrics import accuracy_score
from sklearn.preprocessing import StandardScaler
from sklearn.svm import SVC
from sklearn.metrics import confusion_matrix
from imblearn.metrics import classification_report_imbalanced
from imblearn.over_sampling import RandomOverSampler
from imblearn.over_sampling import SMOTE
from imblearn.under_sampling import ClusterCentroids
from sklearn.decomposition import PCA

```

### Connecting to GBQ and Retrieve Data
As explained in a pevious section, our data needs to be pulled from the cloud and extacted using SQL. 
``` python
from google.colab import auth
from google.cloud import bigquery
from google.cloud import bigquery

auth.authenticate_user()
project_id = 'civil-hope-323521'
client = bigquery.Client(project=project_id)
df = client.query('''
select * 
from `civil-hope-323521.attrition_dataset_1.IBM_attrition_2021` # project_id.database.table
''').to_dataframe()
df.head() 
```

## Leveraging Pandas to Explore the Data 

Before attempting to build predictive models, it is important to take a closer look at your data and inspect the variation in the data (specifically in how it related to the target data). After all, predictive modeling is about considering the relationship between features and the target variable. Now that our data is stored in a dataframe, we will use pandas methods to learn more about the data.

### [Groupby](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.groupby.html)

In this section, we are trying to compare the group where attrition equals True to the group where attrition equals False. The Groupby function groups larger ammounts of data and computes operations on the groups. It returns a dataframe. 

``` python
# divide the data into two groups
df1 = df.groupby(by='Attrition').size()
#plot the counts
df1.plot(kind='bar',title = "Count of employees by attrition status", figsize=(10,10))
```

To group the data by attrition status, the groupby function requires an input of which column to use to form the groups. The [.size()](https://pandas.pydata.org/docs/reference/api/pandas.core.groupby.GroupBy.size.html) opperation is then used to count the number of employees in each group. A bar plot can be used to plot the output dataframe to visualize the resulting counts. Visualizing the imbalanced results is critical because it is very important to pay attention to balanced and imbalanced data in classification problems. 
