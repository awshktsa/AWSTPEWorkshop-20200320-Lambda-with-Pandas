

# AWSTPEWorkshop-20200320-Lambda-with-Pandas
This workshop will show you how to use Pandas lib in your Lambda function, which implement with Lambda layer.

----
## 1. Prepare environment

### a. Create a cloud9 environment
![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/10.png)

![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/11.png)


----
## 2. Create a Lambda Layer
Create a new Lambda Layer which include Pandas and required python lib.

### a. Create an **required.txt** file 
Create an **requirement.txt** file for pip to install selected lib from file.

<pre><code>pandas==0.23.4
pytz==2018.7
</pre></code>

### b. Create and run script to prepare required resources
Next, create an script called **prepare_layer_packages.sh**
https://aws.amazon.com/tw/premiumsupport/knowledge-center/lambda-layer-simulated-docker/
<pre><code>#!/bin/bash

export DIR="python"

rm -rf ${DIR} && mkdir -p ${DIR}

docker run --rm -v $(pwd):/foo -w /foo lambci/lambda:build-python3.6 \
    pip install -r requirement.txt --no-deps -t ${DIR}
</pre></code>

### c. Package files
Run command as following

<pre><code>chmod +x prepare_layer_packages.sh
./prepare_layer_packages.sh
zip -r my-pandas23-layer.zip .
</pre></code>

### d. Create Lambda layer in AWS
1. Create a new AWS Lambda Layer
![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/01.png)

![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/02.png)

2. Input **my-pandas23-layer** as your Layer name, and then upload your my-pandas23-layer.zip file. In the end, select **python 3.6** as runtime.
![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/03.png)

![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/04.png)

----

## 3. Use Lambda layers into functions
Use required Lambda layers into your Lambda functions

### a. Creating Lambda function with Lambda Layer
Creating Lambda function and using build-in Lambda layer and customized layer you created.

1. Create a new Lambda function called **pandas-sample**. 
![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/05.png)

2. Add new Layer to this function
![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/06.png)

3. Add AWS provided **AWSLambda-Python36-SciPy1x**
![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/07.png)

![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/08.png)

4. Add your layer **my-pandas23-layer**
![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/09.png)

### b. Testing Lambda function

1. Testing Lambda function working like expected.
<pre><code>import json

import numpy as np
import pandas as pd

def lambda_handler(event, context):
    # TODO implement
    dates = pd.date_range('20181201', periods=6)
    df = pd.DataFrame(np.random.randn(6, 4), index=dates, columns=list('ABCD'))
    print(df)
    return {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda!')
    }
</code></pre>

2. Config a test event
![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/12.png)

3. Test your Lambda function with Lambda Layer
![Image](https://github.com/awshktsa/AWSTPEWorkshop-20200320-Lambda-with-Pandas/blob/master/assets/13.png)