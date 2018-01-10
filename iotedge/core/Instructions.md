## Instructions

### 1) Accessing your Device/Linux DSVM

Use the following credentials to log in to the Device/Linux DSVM:

* *VM Name*: {Outputs.DvmName}
* *Admin Username*: {Outputs.DadminUsername}
* *SSH Command*: {Outputs.DsshConnection}

Check Docker to see that the IoT Edge agent is running as a module:
```cmd
sudo docker ps
```

![See edgeAgent in Docker](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/iot-edge/media/tutorial-simulate-device-linux/docker-ps.png)

### 2) Deploy a module

Manage your Azure IoT Edge device from the cloud to deploy a module which will send telemetry data to IoT Hub.

One of the key capabilities of Azure IoT Edge is being able to deploy modules to your IoT Edge devices from the cloud. An IoT Edge module is an executable package implemented as a container. In this section, you deploy a module that generates telemetry for your simulated device. 

1. In the Azure portal, navigate to your IoT hub.
1. Go to **IoT Edge (preview)** and select your IoT Edge device.
1. Select **Set Modules**.
1. Select **Add IoT Edge Module**.
1. In the **Name** field, enter `tempSensor`. 
1. In the **Image URI** field, enter `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`. 
1. Leave the other settings unchanged, and select **Save**.

   ![Save IoT Edge module after entering name and image URI](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/includes/media/iot-edge-deploy-module/name-image.png)

1. Back in the **Add modules** step, select **Next**.
1. In the **Specify routes** step, select **Next**.
1. In the **Review template** step, select **Submit**.
1. Return to the device details page and select **Refresh**. You should see the new tempSensor module running along the IoT Edge runtime. 

   ![View tempSensor in list of deployed modules][1]

<!-- Images -->
[1]: https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png

### 3) View generated data

In this tutorial, you created a new IoT Edge device and installed the IoT Edge runtime on it. Then, you used the Azure portal to push an IoT Edge module to run on the device without having to make changes to the device itself. In this case, the module that you pushed creates environmental data that you can use for the tutorials. 

Open the command prompt on the computer running your simulated device again. Confirm that the module deployed from the cloud is running on your IoT Edge device:

```cmd
sudo docker ps
```

![View three modules on your device](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/iot-edge/media/tutorial-simulate-device-linux/docker-ps2.png)

View the messages being sent from the tempSensor module to the cloud:

```cmd
sudo docker logs -f tempSensor
```

![View the data from your module](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/iot-edge/media/tutorial-simulate-device-linux/docker-logs.png)

### 4) Visualize the device telemetry in Time Series Insights

You can also view the telemetry the device is sending by using the [Time Series Insights][lnk-iothub-explorer]. 


### Ways to connect to the Device/Linux DSVM

You can access your newly created Device/Linux DSVM in several ways.

#### Terminal

Use the following credentials to log in:

* *VM Name*: {Outputs.DvmName}
* *Admin Username*: {Outputs.DadminUsername}
* *SSH Command*: {Outputs.DsshConnection}

#### X2Go Client

You can download The X2Go client from the [X2Go site](http://wiki.x2go.org/doku.php/start).

* *Host*: {Outputs.DdnsName}
* *Login*: {Outputs.DadminUsername}
* *SSH port*: 22
* *Session Type*: Change the value to _XFCE_

#### PuTTY

You can download PuTTY from the [PuTTY site](http://www.putty.org/).

* *Host Name*: {Outputs.DdnsName}
* *Port*: 22
* *Connection Type*: SSH
* *login as*: {Outputs.DadminUsername}

#### Azure Portal

* Click [here]({Outputs.DfirstDeviceVmUrl}) to view the Device/Linux DSVM on the Azure portal.

* Click [here](https://quickstart.azure.ai/Deployments/new/datasciencevm?source=CiqsGallery) to install the Windows Data Science VM

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