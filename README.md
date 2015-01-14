# IBM Smart Cloud Analytics Predictive Insights (SCAPI) Health and Performance Content Pack

# Description

This is a cookbook for creating your own content packs for managing the health and performance of your SCAPI deployment.  Within IBM, we have referred to this in other products as being able to "manage our stuff with our stuff" or MOSWOS.  This repository provides you with the ability to use logstash to parse most of the SCAPI log files and stream them into custom DSV Content Packs for search and visualization within your SCALA instance.

# What's Included?

1. A sample logstash configuration **(scapi-health-performance-logstash.conf)** to parse and annotate the following SCAPI 1.3 log files in detail: 

	type => "SCAPI-TOPIC-DS" 	: TopicDataSourcescapi_log_DataSource.log
	type => "SCAPI-STAT" 		: GrangerDetectionscapiFw_stat.log
	type => "SCAPI-MODEL" 		: GrangerDetectionscapi_log_GrangerTrainWrapper.log
	type => "SCAPI-BULK"		: All other SCAPI logs are parsed in a more generic manner. (there are A LOT of SCAPI logs!)
	
	From the SCAPI-STAT log type, four additional outputs are derived for READ, MERGE, OVERALL and DATAAVAIL messages.
	
2. A logstash grok pattern file for parsing the above SCAPI 1.3 log files. **(SCAPIPATTERNS)**

3. Custom DSV Content Pack properties files to create CSV based SCALA source types for the following:

	- scapi-bulk.props
	- scapi-model.props
	- scapi-stat-read.props
	- scapi-stat-merge.props
	- scapi-stat-overall.props
	- scapi-stat-dataavail.props
	- scapi-topic-general.props

4. Sample SCALA Dashboard Apps (coming soon)

5. SCAPI Server Health and Performance Add-On (coming soon)

# Deploying the Content Pack

1. Determine how you will ship the SCAPI logs to logstash. There are many options including using rsyslog/syslog-ng, logstash-forwarder, LFA or the logstash file input if you plan to run logstash on the SCAPI server or manually copy logs over to your logstash server for bulk processing.  

2. Review the sample logstash configuration **scapi-health-performance-logstash.conf** and determine how you will use the inputs, filters and outputs.  You can use this example or just copy the needed parts into your main logstash configuration file. Pay close attention to the NOTES within the **scapi-health-performance-logstash.conf** 

3. Review the filters section and determine if any changes are needed based upon how you use logstash, which version you use, etc. 

4. Place the logstash GROK pattern file **SCAPIPATTERNS** into the appropriate location.  If you are using logstash 1.4.2, you may place this into the /patterns directory and you'll be all set. If you are using logstash 1.5 beta, you can place it into the same /patterns directory, but you will need to use the patterns_dir setting to this location in each filter using the GROK patterns (eg multiline, grok).

5. Review the output section and update based on your current logstash integration with SCALA. This example configuration will only work with the upcoming release of the SCALA-Logstash Integration.  If you do not have this, contact Doug McClure for assistance.

6. Copy the DSV Pack properties files you plan on using into the $SCALAHOME/unity_content/DSVToolkit_v1.1.0.2 directory.

7. Edit each DSV Pack properties file and update the scalaHome directory path.  You may also want to optimize the various index configuration fields in this file. For example, setting filterable = false for the processID field if you don't feel faceting on processID would be useful.

8. From the $SCALAHOME/unity_content/DSVToolkit_v1.1.0.2 directory, execute this command to build and deploy each DSV Content Pack you plan on using into SCALA: **python dsvGen.py [FILENAME].props -d -f -u unityadmin -p unityadmin**

9. Create SCALA datasources which use the new DSV Content Pack source types that you have deployed for each SCALA log type. Be sure to set the host and path values to match what you're setting in your logstash configuration. 

10. Start up logstash and verify logs are being consumed properly. Watch for activity in logstash stdout or within SCALA search results.

# Need Help?

This is a community contributed content pack and no explicit support, guarantee or warranties are provided by IBM nor the contributor. Feel free to engage the community on the ITOAdev forum if you need help!