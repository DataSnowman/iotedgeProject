## Instructions

Use the following credentials to log in:

* *VM Name*: {Outputs.LvmName}
* *Admin Username*: {Outputs.LadminUsername}
* *SSH Command*: {Outputs.sshConnection}

#### Azure Portal

* Click [here]({Outputs.firstDataScienceVmUrl}) to view Linux DSVM on the Azure portal.

#### Model development on the Docker of Ubuntu DSVM

#####  1. Set up the compute target

Start the command line from Machine Learning Workbench by selecting **File** > **Open Command Prompt**. Then run:

```az login```

```az account set --subscription $subscriptionId``` 

```az ml computetarget attach --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password --type remotedocker```

You can find the DSVM IP address by clicking [here]({Outputs.firstDataScienceVmUrl}).  The Username and Password are the ones you entered when you deployed this Azure Machine Learning Workbench tutorial.  You can find your subscriptionId when you login to the CLI.

The following two files are created in the aml_config folder of your project:

-  dockerdsvm.compute: This file contains the connection and configuration information for a remote execution target.
-  dockerdsvm.runconfig: This file is a set of run options used within the Workbench application.

Browse to dockerdsvm.runconfig, and change the configuration of these fields to the following:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Prepare the project environment by running:

```az ml experiment prepare -c dockerdsvm```


With `PrepareEnvironment` set to true, Machine Learning Workbench creates the runtime environment whenever you submit a job. `Config/conda_dependencies.yml` and `Config/dsvm_spark_dependencies.yml` contain the customization of the runtime environment. You can always modify the Conda dependencies, Spark configuration, and Spark dependencies by editing these two YMAL files. For this example, we added `azure-storage` and `azure-ml-api-sdk` as extra Python packages in  `Config/conda_dependencies.yml`. We also added `spark.default.parallelism`, `spark.executor.instances`, and `spark.executor.cores` in `Config/dsvm_spark_dependencies.yml`. 

#####  2. Data preparation and feature engineering on DSVM Docker

Run the script `etl.py` on DSVM Docker. Use a debug parameter that filters the loaded data with specific server IP addresses:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Browse to the side panel, and select **Run** to see the run history of `etl.py`. Notice that the run time is about two minutes. If you plan to change your code to include new features, providing FILTER_IP as the second argument provides a faster iteration. You might need to run this step multiple times when dealing with your own machine learning problems, to explore the data set or create new features. 

With the customized restriction on what data to load, and further filtering of what data to process, you can speed up the iteration process in your model development. As you experiment, you should periodically save the changes in your code to the Git repository. Note that we used the following code in `etl.py` to enable the access to the private container:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Next, run the script `etl.py` on DSVM Docker without the debug parameter FILTER_IP:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Browse to the side panel, and select **Run** to see the run history of `etl.py`. Notice that the run time is about four minutes. The processed result of this step is saved into the container, and is loaded for training in train.py. In addition, the string indexers, encoder pipelines, and standard scalers are saved to the private container. These are used in operationalization. 


##### 3. Model training on DSVM Docker

Run the script `train.py` on DSVM Docker:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

This step loads the intermediate compute results from the run of `etl.py`, and  trains a machine learning model. This step takes about two minutes.

When you have successfully finished the experimentation on the small data, you can continue to run the experimentation on the full data set. You can start by using the same code, and then experiment with argument and compute target changes.  


### Accessing your Ubuntu Linux Data Science VM

You can access your newly created Data Science VM for Linux in several ways.

#### Terminal

Use the following credentials to log in:

* *VM Name*: {Outputs.LvmName}
* *Admin Username*: {Outputs.LadminUsername}
* *SSH Command*: {Outputs.sshConnection}

#### X2Go Client

You can download The X2Go client from the [X2Go site](http://wiki.x2go.org/doku.php/start).

* *Host*: {Outputs.dnsName}
* *Login*: {Outputs.LadminUsername}
* *SSH port*: 22
* *Session Type*: Change the value to _XFCE_

#### PuTTY

You can download PuTTY from the [PuTTY site](http://www.putty.org/).

* *Host Name*: {Outputs.dnsName}
* *Port*: 22
* *Connection Type*: SSH
* *login as*: {Outputs.LadminUsername}

#### Azure Portal

* Click [here]({Outputs.firstDataScienceVmUrl}) to view the first of your {Outputs.numInstances} DSVM instances on the Azure portal.

Once you are finished the Linux DSVM install you can click the Next button to proceed to installing a HDInsight Spark cluster which can be used scale your AML workbench activities.

   >[!IMPORTANT]
   >If you get an error when clicking the Next button, please hit F5 to refresh your browser session and click the Next button again.