Scheduling GCP instances Start and Stop - How To
================================================

Description
-----------

This repository is here to explain how to schedule automatically the start and the stop of GCP Instances.

Steps
-----

1. **Labels**

	When creating an compute instance on GCP, add a label either automatically or manually like so:
	* Go to "Compute Engine" then "VM Instances".
	* On the right side, click on "Show Info Panel".
	* Select the instances for which you want to add a label.
	* Select the "Label" Tab on the "Info Panel".
	* Click "Add Label".
	* Choose a key and a value, for example: instance_type and vault.
	* Save.

	This label will allow us to target the right instance to manage.

2. **Functions**
	
	Create the Start function:
	* Access the "Cloud Functions" page in the GCP Console.
	* Click on "CREATE FUNCTION".
	* Set Name to "startInstancePubSub".
	* Leave the "Memory allocated" parameter to its default value.
	* For the "Trigger", choose "Cloud Pub/Sub".
	* For the "Topic", select "Create new topic...".
	* A box "Create a topic" should appear.
	* Set the Name to "start-instance-event".
	* Click on "Create Topic" to create the topic and close the box.
	* For the Source Code, leave "Inline editor".
	* Set the runtime to "Node.js 8" or "Node.js 10".
	* Above the text block, select the "index.js" tab.
	* Replace the content there with the one from the start.index.js
	* Above the text block, select the "package.json" tab.
	* Replace the content there with the one from the start.package.json
	* For the field "Function to execute", write "startInstancePubSub".
	* Click on "Create".

	Create the Stop function:
	* Access the "Cloud Functions" page in the GCP Console.
	* Click on "CREATE FUNCTION".
	* Set Name to "stopInstancePubSub".
	* Leave the "Memory allocated" parameter to its default value.
	* For the "Trigger", choose "Cloud Pub/Sub".
	* For the "Topic", select "Create new topic...".
	* A box "Create a topic" should appear.
	* Set the Name to "stop-instance-event".
	* Click on "Create Topic" to create the topic and close the box.
	* For the Source Code, leave "Inline editor".
	* Set the runtime to "Node.js 8" or "Node.js 10".
	* Above the text block, select the "index.js" tab.
	* Replace the content there with the one from the stop.index.js
	* Above the text block, select the "package.json" tab.
	* Replace the content there with the one from the stop.package.json
	* For the field "Function to execute", write "stopInstancePubSub".
	* Click on "Create".

3. **Tasks**
	
	Create the scheduled task to run the function:

	The "start" task:
	* Access the "Cloud Scheduler" page in the GCP Console.
	* Click on "CREATE JOB".
	* Set Name to startup-vault-instances.
	* Add a description.
	* For frequency, it works on CRON principles. You can generate the "timer string" at https://crontab.guru/ if needed.
	* Paste it in "frequency".
	* For the Timezone, select yours depending on which country you are. ex: Central Europeen Summer Time (CEST) UTC+2 Paris.
	* In Target, use "Pub/Sub".
	* In Topic, put the topic name we've choose earlier: "start-instance-event".
	* And the payload is:
	<pre><code>{"zone":"europe-west1-c","label":"instance_type=vault"}
	</code></pre>
	* Click on "Create".

	The "stop" task:
	* Access the "Cloud Scheduler" page in the GCP Console.
	* Click on "CREATE JOB".
	* Set Name to shutdown-vault-instances.
	* Add a description.
	* For frequency, it works on CRON principles. You can generate the "timer string" at https://crontab.guru/ if needed.
	* Paste it in "frequency".
	* For the Timezone, select yours depending on which country you are. ex: Central Europeen Summer Time (CEST) UTC+2 Paris.
	* In Target, use "Pub/Sub".
	* In Topic, put the topic name we've choose earlier: "stop-instance-event".
	* And the payload is:
	<pre><code>{"zone":"europe-west1-c","label":"instance_type=vault"}
	</code></pre>
	* Click on "Create".


Source
------
https://cloud.google.com/scheduler/docs/start-and-stop-compute-engine-instances-on-a-schedule















