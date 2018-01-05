## Instructions

####  Model development on the HDInsight cluster

##### 1. Create the compute target in Machine Learning Workbench for the HDInsight cluster

```az ml computetarget attach --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password --type cluster```

The following two files are created in the aml_config folder:
    
-  myhdo.compute: This file contains connection and configuration information for a remote execution target.
-  myhdi.runconfig: This file is set of run options used within the Workbench application.


Browse to myhdi.runconfig, and change the configuration of these fields to the following:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Prepare the project environment by running:

```az ml experiment prepare -c myhdi```

This step can take up to seven minutes.

##### 2. Data preparation and feature engineering on HDInsight cluster

Run the script `etl.py`, with full data on the HDInsight cluster:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Because this job lasts for a relatively long time (approximately two hours), you can use `-a` to disable output streaming. When the job is done, in **Run History**, you can view the driver and controller logs. If you have a larger cluster, you can always reconfigure the configurations in `Config/hdi_spark_dependencies.yml` to use more instances or cores. For example, if you have a four-worker-node cluster, you can increase the value of `spark.executor.instances` from 5 to 7. You can see the output of this step in the **fullmodel** container in your storage account. 

### 1) Accessing your Windows Data Science VM

You can access your newly created Data Science VM with Remote Desktop:

* *Computer*: {Outputs.WvmName}
* *User name*: {Outputs.WadminUsername}

Click [here]({Outputs.dataScienceVmUrl}) to view your Windows DSVM on the Azure portal.

### 2) Accessing your Ubuntu Linux Data Science VM

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

### 3) Accessing your Spark Cluster
[Open the Spark Cluster](https://portal.azure.com/#resource/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup.Name}/providers/Microsoft.HDInsight/clusters/{Outputs.sparkClusterName}/overview)

## Next steps
