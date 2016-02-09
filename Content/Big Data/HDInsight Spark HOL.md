<a name="HOLTitle"></a>
# Data Analytics with Apache Spark for Azure HDInsight #

---

<a name="Overview"></a>
## Overview ##

A recent report from networking giant Cisco predicted that more than 50 billion devices will be connected to the Internet by 2020. Other estimates range higher than that. When you consider the volume of data that can be produced by that many devices, you begin to understand the true implications of **BIG DATA**. Data is being collected in ever-escalating volumes, at increasingly higher velocities, and in a widening variety of formats, and it's being used in increasingly diverse contexts. "Data" used to be something stored in a table in a SQL database, but today it can be a collection of sensor readings, tweets, GPS locations, or almost anything else. The challenge for information scientists is to make sense of that data.

Traditionally, [Apache Hadoop](https://hadoop.apache.org/) was the researcher's choice for big-data analysis. Hadoop enjoys tremendous mindshare in the research community, but because it is inherently disk-based, it is comparatively slow. A new tool that is garnering a lot of attention is [Apache Spark](http://spark.apache.org/). Spark is an open-source framework for large-scale data analytics. Because it is memory-based, Spark can process data up to 100 times faster than Hadoop. Azure HDInsight reduces the complexity of deploying Spark clusters to a few button clicks, with no hardware to buy and no software to install and configure. Additionally, Spark clusters created with Azure HDInsight come complete with the popular notebooking tools [Apache Zeppelin](https://zeppelin.incubator.apache.org/) and [Jupyter](https://jupyter.org/) and offer seamless integration with business-intelligence tools such as Microsoft Power BI.

In this lab, you will experience [Apache Spark for Azure HDInsight](https://azure.microsoft.com/en-us/services/hdinsight/apache-spark/) first-hand. After creating a Spark cluster, you will use Zeppelin and Jupyter to analyze a data set. The goal is to learn how to create and utilize your own Spark clusters, and to experience the ease with which they are provisioned and used in Azure.

<a name="Objectives"></a>
### Objectives ###

In this hands-on lab, you will learn how to:

- Deploy an HDInsight Spark cluster
- Use Zeppelin to run interactive queries on data loaded into a Spark cluster
- Use Jupyter to run interactive queries on data loaded into a Spark cluster
- Delete a Spark cluster to avoid incurring unnecessary charges

<a name="Prerequisites"></a>
### Prerequisites ###

The following are required to complete this hands-on lab:

- A Microsoft Azure subscription - [sign up for a free trial](http://aka.ms/WATK-FreeTrial)
- Google's [Chrome](https://www.google.com/chrome/) browser. Parts of this lab do not work in Safari and exhibit problems in other browsers. To ensure that the lab works as intended, use Chrome. 

---
<a name="Exercises"></a>
## Exercises ##

This hands-on lab includes the following exercises:

1. [Exercise 1: Create a Spark cluster on HDInsight](#Exercise1)
1. [Exercise 2: Use Zeppelin to analyze data on a Spark cluster](#Exercise2)
1. [Exercise 3: Use Jupyter to analyze data on a Spark cluster](#Exercise3)
1. [Exercise 4: Remove the HDInsight Spark cluster](#Exercise4)

Estimated time to complete this lab: **60** minutes.

<a name="Exercise1"></a>
## Exercise 1: Create a Spark cluster on HDInsight

Hadoop is a popular tool for analyzing big data, but it's not the only choice that Azure HDInsight offers you. In this exercise, you will create an HDInsight cluster running [Apache Spark](http://spark.apache.org/). Spark is renowned for its ease of use and is built for speed, performing some operations 100 times faster than Hadoop in memory and 10 times faster on disk.

1. Start by logging in to the [Azure Portal](https://portal.azure.com).

1. Click **+ NEW** in the upper-left corner. Then click **Data + Analytics**, followed by **HDInsight** to display the "New HDInsight Cluster" blade.

    ![Creating an HDInsight cluster](Images/ex1-data-analytics-hdinsight.png)

    _Creating an HDInsight cluster_

1. The "New HDInsight Cluster" blade asks you to enter several parameters for the cluster that's about to be created. **Cluster Name** specifies a unique Domain Name System (DNS) name for the cluster. Enter a name, and make sure a green check mark appears next to it indicating that the name is valid and unique.
 
	**Cluster Type** allows you to choose between various types of clusters (Hadoop, HBase, and so on). For this lab, select **Spark (PREVIEW)** from the list of cluster types.

	**Cluster Operating System** specifies the operating system for the nodes in the cluster. You have two choices: Linux or Windows. At the time of this writing, HDInsight Spark clusters provisioned with Linux don't have Zeppelin preinstalled, so for this lab, choose **Windows**.

	**Version** specifies which version Spark to install on all the nodes. Choose the latest version offered.

	**Subscription** specifies which Azure subscription to charge for the cluster. If you have multiple subscriptions, select **Azure Pass** from the drop-down list to avoid incurring charges in other subscriptions.

    ![Specifying the cluster's name and type](Images/ex1-spark-cluster-name.png)

    _Specifying the cluster's name and type_

1. In the **Resource Group** section of the "New HDInsight Cluster" blade, click **New** and enter a resource-group name. The name you enter must be unique to your subscription, but it doesn't have to be unique across Azure. Look for the green check mark next to the name before proceeding.

    ![Creating a new resource group](Images/ex1-create-resource-group.png)

    _Creating a new resource group_

1. The next step is to provide credentials for logging in to the cluster. Click **Credentials** to bring up the "Cluster Credentials" blade. Then enter a password. The password must be at least 10 characters in length and contain at least one digit, one non-alphanumeric character, and one uppercase or lowercase letter. **Remember this password because you will need it later** to connect to the cluster and use Zeppelin and Jupyter. When both password boxes show green check marks, click the **Select** button at the bottom of the blade.
 
	> If you wanted to access the Spark cluster directly through Remote Desktop, you could enable that in this blade. You won't be using Remote Desktop in this lab, so you can leave **Enable Remote Desktop** set to **NO**.

    ![Entering cluster credentials](Images/ex1-cluster-credentials.png)

    _Entering cluster credentials_

1. When creating an HDInsight cluster, you have the choice of using an existing storage account or creating a new one. For this exercise, you want to create a new storage account. Click **Data Source** in the "New HDInsight Cluster" blade. Then click **Create New** under **Select storage account** and enter a name for the new storage account. Remember, the name must be all lowercase, and it must be unique within Azure. Then enter a container name into the **Choose Default Container** field. This is the container that the cluster will use for blob storage. Optionally select the region nearest you under **Location**, and then click the **Select** button at the bottom of the blade.

	> This storage account is where all the data for the cluster will be stored, including virtual hard disks (VHDs) for the cluster's nodes, the sample data included with the cluster, and the Zeppelin and Jupyter notebooks that you create. Because the storage account is being created with the cluster, deleting the resource group in Exercise 4 will delete the storage account, too. One motivation for specifying an existing storage account is to preserve the cluster's data, including your Zeppelin and Jupyter notebooks, when the cluster is deleted. To do that, you would create a separate storage account in a separate resource group and select that storage account here.

    ![Specifying the data source](Images/ex1-data-source.png)

    _Specifying the data source_

1. Click **Node Pricing Tiers** to bring up the "Node Pricing Tiers" blade. Here, you specify the number of nodes in the cluster and the type of virtual machines you want to run. You have complete control over the size of the cluster so you can strike the right balance between processing power and cost. Set the number of worker nodes to 4. Then click the **Select** button at the bottom of the blade.

    ![Specifying the size of the cluster](Images/ex1-node-pricing.png)

    _Specifying the size of the cluster_

1. Now click the **Create** button at the bottom of the "New HDInsight Cluster" blade to start deploying the cluster.

    ![Deploying a Spark cluster](Images/ex1-create-button.png)

    _Deploying a Spark cluster_

1. Open the blade for the resource group whose name you entered in Step 4. (An easy way to do that is to click **Resource groups** in the ribbon on the left side of the portal, and then click the resource group's name.) The "Last deployment" section of the blade shows the deployment status. Once the cluster has been deployed, the status will change from "Deploying" to "Succeeded." **Deployment can require up to 45 minutes**. Wait for the deployment to finish, and then proceed to the next exercise.

	![Successful cluster deployment](Images/ex1-successful-deployment.png)

	_Successful cluster deployment_

In this exercise, you learned how to spin up a Spark cluster on Azure, and about some of the options you can choose from when doing so. In the next exercise, you will use Zeppelin to analyze a data set and visualize the results.

<a name="Exercise2"></a>
## Exercise 2: Use Zeppelin to analyze data on a Spark cluster

In this exercise, you will process data in a [Zeppelin](https://zeppelin.incubator.apache.org/) notebook. Zeppelin is built in to HDInsight Spark clusters provisioned with Windows, so no installation is required. You will analyze and graph heating, ventilating, and air-conditioning (HVAC) data for a group of buildings. **This exercise works best in Google Chrome and doesn't work at all in some browsers, notably Safari.**

1. If the resource group containing your HDInsight Spark cluster is not open in the portal, open it now.

1. Click the **Hadoop elephant icon** in the resource group.

	![Selecting the Spark cluster](Images/ex2-spark-in-resource-group.png)

	_Selecting the Spark cluster_

1. In the blade for the HDInsight Spark cluster, click **Cluster Dashboards**.

	![Opening the cluster dashboards](Images/ex2-spark-dashboards-button.png)

	_Opening the cluster dashboards_

1. In the "Cluster Dashboards" blade, click **Zeppelin Notebook**.

	![Opening Zeppelin](Images/ex2-zeppelin-button.png)

	_Opening Zeppelin_

1. When prompted for a user name and password, log in with the user name ("admin" if you accepted the default) and password you specified for the cluster credentials in Exercise 1, Step 5.

1. The next step is to create a new note. Click **Create new note**.

	![Creating a new note](Images/ex2-create-new-zeppelin-notebook.png)

	_Creating a new note_
    
1. A dialog will pop up asking you to name the note. Name it something appropriate, such as "Research" and click the **Create Note** button.

	![Naming the Zeppelin note](Images/ex2-name-zeppelin-note.png)

	_Naming the Zeppelin note_

1. After naming your note, open it by clicking its name in the Zeppelin window.

    ![Opening a Zeppelin note](Images/ex2-click-open-note.png)
    
    _Opening a Zeppelin note_

1. The first step in analyzing data is loading it into a temporary table in the HDInsight Spark cluster. The sample data you will use is already present in the storage account that was created for the cluster, but you'll have to write some code to load it from storage into a temporary table. Begin by copying the following statements to the clipboard:

	<pre>
	// Create an RDD using the default Spark context, sc
	val hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")

	// Define a schema
	case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

	// Map the values in the .csv file to the schema
	val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
	    s => Hvac(s(0),
	            s(1),
	            s(2).toInt,
	            s(3).toInt,
	            s(6)
	    )
	).toDF()

	// Register as a temporary table called "hvac"
	hvac.registerTempTable("hvac")
	</pre>

1. In a Zeppelin notebook, you work in paragraphs. Click the empty paragraph in the notebook in preparation for pasting the code you copied to the clipboard in the previous step.

	![The empty Zeppelin paragraph](Images/ex2-empty-zeppelin-paragraph.png)

	_The empty Zeppelin paragraph_

	If you cannot place the cursor into the paragraph and type, that means the paragraph or notebook is set to read-only. To enable editing, click the second button from the left (the one with four arrows pointing to the center).

	![Enabling editing](Images/ex2-set-to-edit-mode.png)

	_Enabling editing_

1. Now paste the code from the clipboard into the paragraph. The paragraph should look like this:

	![Code pasted into the paragraph](Images/ex2-zeppellin-import-data-code.png)

	_Code pasted into the paragraph_

1. Now you're ready to run the code to import the HVAC data into your cluster. To run the paragraph, either press Shift+Enter, or click the **Run** button highlighted below.

	![Running the paragraph](Images/ex2-zeppelin-run-button.png)

	_Running the paragraph_

1. The word "READY" will turn to "RUNNING xx%" and the paragraph will gray out. The run is finished when "RUNNING xx%" changes to "FINISHED." Additionally, a new paragraph will appear in the notebook. If you would like, you can give a title to the paragraph by clicking the **Settings** button in the paragraph options and choosing **Show title**. If you do change the title, remember to press Enter to complete the change.

	![A finished run](Images/ex2-zeppelin-paragraph-settings.png)

	_A finished run_

1. With the data now loaded into a temporary table named "hvac," you can run Spark SQL queries on it. Copy the following code to the clipboard and paste it into the new paragraph in your notebook. Then click the **Run** button or press Shift+Enter.

	<pre>
	%sql select buildingID, (targettemp - actualtemp) as temp_diff, date
	from hvac
	where date = "6/1/13"
	</pre>

	> %sql tells Spark to use the built-in Spark SQL interpreter. You can see all the provided interpreters by clicking **Interpreter** at the top of the page.

1. The query you just executed created a table containing columns for the building ID, the difference between the target temperature and the actual temperature for that building, and the date the data was recorded. The default output is the table itself. But part of Zeppelin's appeal is that it allows you to visualize data in various ways. To demonstrate, click the bar-graph icon. The resulting graph shows the temperature differences in each building. The graph indicates that the actual temperature in building 2 was 58 degrees less than the target temperature, which is deceptive because it's actually the sum of all temperature differences recorded for that building. 

	![Bar graph showing temperature differences in each building](Images/ex2-default-sql-graph.png)

	_Bar graph showing temperature differences in each building_

1. A more useful graph would be one that shows the *average* difference between target temperature and actual temperature for each building. Click **Settings** to reveal boxes labeled Keys, Groups, and Values. In the Values box, click **temp_diff SUM** and select **avg** from the menu to change SUM to AVG.

	![Modifying graph settings](Images/ex2-settings-change-value-to-avg.png)

	_Modifying graph settings_

	The graph updates to show the *average* difference between target temperature and actual temperature in each building:

	![Bar graph showing average temperature differences](Images/ex2-average-temp-diff-graph.png)

	_Bar graph showing average temperature differences_

1. Spark SQL allows you to work with data interactively by including variables in your queries. To demonstrate, copy the query below into a new paragraph in your notebook, and then run the query. Because the query includes a variable named "Temp" (short for "Temperature"), you will be prompted to enter a value for it.

	<pre>
	%sql select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
	from hvac
	where targettemp > "${Temp}" 	
	</pre>

	![Prompting for a variable value](Images/ex2-temp-query-code-pasted.png)

	_Prompting for a variable value_

1. In the **Temp** field, enter 65 so the result set will only include buildings in which the target temperature is greater than 65 degrees. Press the Enter key to execute the query. Then click the bar-graph icon to graph the results. By default, the resulting graph uses the first field as the key, and the sum of the second field as the value. This isn't very useful because in this example, the second field holds a set of dates.

	![Graph showing building numbers and date sums](Images/ex2-temp-query-initial-graph.png)

	_Graph showing building numbers and date sums_

1. To make this graph more useful, click **Settings**. Then drag **targettemp** from "All fields" to the Groups box, and drag **temp\_diff** to the Values box. Change SUM to AVG for the **temp_diff** field and click the X in the **date** field to remove it from the Values box. The resulting graph reveals that the HVAC systems are running below the target temperature on cooler days and above the target temperature on warmer days.

	![Graph showing average temperature differences grouped by target temperature](Images/ex2-controlling-zeppelin-settings.png)

	_Graph showing average temperature differences grouped by target temperature_

1. Spark SQL is one way to query data loaded into Spark, but it's not the only way. Spark supports several other languages, including Scala, a functional programming language that is getting a lot of traction in the big-data world. In fact, Spark is written in Scala.

	Suppose you wanted to determine which buildings are the hottest and coldest. One way to do that would be to sum up all the temperature differences recorded for each building and look for the values that are highest and lowest. You could then explore various heating and cooling options for the buildings to get the actual temperatures more in line with the target temperatures.

    The following code shows one way to go about that with Scala. The first line executes a SQL query from Scala to query the "hvac" table created earlier and sum temperature differences for each building. The second and third lines use Scala to sort the data and grab the first row. The fourth and fifth lines register the resulting data set as a Spark table and run a SQL query against it, sorting items in descending order and grabbing the top item. The final two lines display the results.

    <pre>
    // Pull the sum of all temp differences into a DataFrame
    val temp = sql("select buildingID, SUM(targettemp-actualtemp) as sumTemp from hvac group by buildingID")

    // The pure Scala way of manipulating the data to get the coolest building.
    val tempSorted = temp.sort("sumTemp")
    val coolestBuilding = tempSorted.first()

    // Add this as a table and do the SQL way of getting the hottest building.
    temp.registerTempTable("test")
    val hottestBuilding = sql("select * from test order by sumTemp DESC limit 1")

    // Display the data in a rudimentary way. :)
    coolestBuilding.toString()
    hottestBuilding.show()
    </pre>

1. Paste the code above into a new paragraph and run it. Which buildings are the hottest? Which ones are the coldest?

For more information about Zeppelin, visit the [Zeppelin Web site](https://zeppelin.incubator.apache.org/), where you'll find documentation and numerous examples regarding its use.

<a name="Exercise3"></a>
## Exercise 3: Use Jupyter to analyze data on a Spark cluster

Zeppelin makes it easy to do interactive data analysis in Spark. Another popular tool is [Jupyter](https://jupyter.org/), which also uses a notebook paradigm, but boasts support for more than 40 programming languages including Python, R, and Scala. Jupyter excels at numerical simulation, statistical modeling, machine learning, and more. In this exercise, you will use a Jupyter notebook and a Python script to query the HVAC sample data that was installed with your cluster.

1. To start a Jupyter notebook, browse to the Spark cluster in the Azure Portal by clicking **Resource groups**, clicking the cluster's resource group, and clicking the Hadoop elephant icon. Click **Cluster Dashboards**, and then click **Jupyter Notebook**.

	![Opening a Jupyter notebook](Images/ex2-jupyter-button.png)

	_Opening a Jupyter notebook_

1. If prompted for a user name and password, log in with the user name ("admin" if you accepted the default) and password you specified for the cluster credentials in Exercise 1, Step 5..

1. Once you are logged in, you will see the default Jupyter workspace. With the **Files** tab selected, click the **New** button on the right-hand side and select **Python 2** from the menu to create a new Jupyter notebook.

	![Creating a new Jupyter notebook](Images/ex2-new-jupyter-notebook.png)

	_Creating a new Jupyter notebook_

1. The default name for a new Jupyter notebook is "Untitled," which you will want to change. Click "Untitled" and enter a name such as "HVAC Experiment." When you're done, click the **OK** button.

	![Renaming a Jupyter notebook](Images/ex2-rename-jupyter-notebook.png)

	_Renaming a Jupyter notebook_

1. The following Python script imports several required libraries and initializes a Spark SQL context so you can execute Spark SQL commands using the **sqlContext** variable. Copy this code into the edit box in the notebook.

	<pre>
	from pyspark import SparkContext
	from pyspark.sql import SQLContext
	from pyspark.sql.types import *

	# Create Spark and SQL contexts
	sc = SparkContext('spark://headnodehost:7077', 'pyspark')
	sqlContext = SQLContext(sc)
	</pre>

1. Click the **Run** button in the toolbar (or press Shift+Enter) to run the code.

	![Running code in a Jupyter notebook](Images/ex2-jupyter-run-code-first-part.png)

	_Running code in a Jupyter notebook_

1. When code is running in a Jupyter notebook, the circle in the upper-right corner changes from hollow to filled. Wait for the run to finish (that is, for the circle to become hollow again).

	![The running-code indicator](Images/ex2-jupyter-running-icon.png)

	_The running-code indicator_

1. After the code finishes running, a new edit box will appear. Copy the following code into the box. This code uses the Spark SQL interpreter to load the HVAC data file, create a schema, and run a query against the data:

	<pre>
	# Load the data
	hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")

	# Create the schema
	hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

	# Parse the data in hvacText
	hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

	# Create a data frame
	hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

	# Register the data fram as a table to run queries against
	hvacdf.registerAsTable("hvac")

	# Run queries against the table and display the data
	data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = \"6/1/13\"")
	data.show()
	</pre>

1. Click the **Run** button (or press Shift+Enter) to run the code. When the code finishes running, you will see the following output:

	<pre>
	buildingID temp_diff date  
	4          8         6/1/13
	3          2         6/1/13
	7          -10       6/1/13
	12         3         6/1/13
	7          9         6/1/13
	7          5         6/1/13
	3          11        6/1/13
	8          -7        6/1/13
	17         14        6/1/13
	16         -3        6/1/13
	8          -8        6/1/13
	1          -1        6/1/13
	12         11        6/1/13
	3          14        6/1/13
	6          -4        6/1/13
	1          4         6/1/13
	19         4         6/1/13
	19         12        6/1/13
	9          -9        6/1/13
	15         -10       6/1/13
	</pre>

The fact that Jupyter is preinstalled in Spark clusters created with Azure HDInsight means that you can get a notebook up and running with minimal effort. If you're new to Jupyter and want to learn more about its features and capabilities, visit the [Jupyter Web site](http://jupyter.org/) and check out the documentation and examples there.

<a name="Exercise4"></a>
## Exercise 4: Remove the HDInsight Spark cluster

When you are finished with an HDInsight Spark cluster, you should remove it because you are charged for it while it exists, regardless of whether it's doing any work. In this exercise, you will delete the cluster used in the previous exercises.

1. If the resource group containing your HDInsight Spark cluster is not open in the Azure Portal, click **Resource groups** in the ribbon on the left side of the page, and then click the resource group's name. This will open a blade for the resource group.

1. In the blade for the resource group, click the **Delete** button.

    ![Deleting a resource group](Images/ex3-delete-button.png)

    _Deleting a resource group_  

1. As a safeguard against accidental deletion, you must type the resource group's name to delete it. Type in the name, and then click the **Delete** button at the bottom of the blade.

    ![Confirming deletion of a resource group](Images/ex3-confirm-delete.png)

    _Confirming deletion of a resource group_  

After 10 minutes or so, the cluster and all of its associated resources will be deleted.

<a name="Summary"></a>
## Summary ##

Here is a quick summary of the key items you learned in this lab:

- Apache Spark for Azure HDInsight is Microsoft Azure's implementation of Hadoop, Spark, and supporting big-data tools
- The Azure Portal makes it easy to create, configure, and delete HDInsight Spark clusters
- HDInsight Spark clusters come with Zeppelin and Jupyter preinstalled
- Zeppelin and Jupyter notebooks provide a powerful means for querying and visualizing data
- HDInsight Spark clusters should be deleted when they're no longer needed to avoid incurring unwanted charges

With Apache Spark for Azure HDInsight, high-performance computing clusters with all the tools you need to handle big data are just a few button clicks away. And it's just one example of why cloud computing is changing the face of research. 

---

Copyright 2016 Microsoft Corporation. All rights reserved. Except where otherwise noted, these materials are licensed under the terms of the Apache License, Version 2.0. You may use it according to the license as is most appropriate for your project on a case-by-case basis. The terms of this license can be found in http://www.apache.org/licenses/LICENSE-2.0.