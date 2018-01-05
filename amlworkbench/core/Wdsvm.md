## Instructions

### Accessing your Windows Data Science VM

You can access your newly created Data Science VM with Remote Desktop:

* *Computer*: {Outputs.WvmName}
* *User name*: {Outputs.WadminUsername}

Click [here]({Outputs.dataScienceVmUrl}) to view your Windows DSVM on the Azure portal.

### Install Azure Machine Learning Workbench on Windows
Install Azure Machine Learning Workbench on your computer running Windows 10, Windows Server 2016, or newer.

1. Download the latest Azure Machine Learning Workbench installer
[AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi).

2. Double-click the downloaded installer **AmlWorkbenchSetup.msi** from File Explorer.

   >[!IMPORTANT]
   >Download the installer fully on disk, and then run it from there. Do not run it directly from your browser's download widget.

3. Finish the installation by following the on-screen instructions.

   The installer downloads all the necessary dependent components, such as Python, Miniconda, and other related libraries. The installation might take around half an hour to finish all the components. 

4. Azure Machine Learning Workbench is now installed in the following directory:
   
   `C:\Users\<user>\AppData\Local\AmlWorkbench`

### Complete a Real-world example 
Once you have completed the Workbench install you might want to try a How-to guides or Real-world example.
Such and example is [Server workload forecasting on terabytes of data](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-big-data)

#### Create a new Workbench project

Create a new project by using this example as a template:
1.	Open Machine Learning Workbench.
2.	On the **Projects** page, select the **+** sign, and select **New Project**.
3.	In the **Create New Project** pane, fill in the information for your new project.
4.	In the **Search Project Templates** search box, type **Workload Forecasting on Terabytes Data**, and select the template.
5.	Select **Create**.

Have a look at the [Data Description](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-big-data#data-description) and the [Scenario structure](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-big-data#scenario-structure).  Have a look at the folders, code, and configuration files.

Once you are finished the AML Workbench install and created a new project using the **Workload Forecasting on Terabytes Data** template you can click the Next button to proceed to installing the Linux DSVM which will be used for a remote Docker container for completing this Real-world example.

   >[Note]
   >If you get an error when clicking the Next button, please hit F5 to refresh your browser session and click the Next button again.