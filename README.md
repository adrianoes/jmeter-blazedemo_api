# jmeter-blazedemo_api

This project presents a performance test using JMeter for the scenario of purchasing an airline ticket on the [BlazeDemo](https://www.blazedemo.com) website. Two scripts were developed: a load test and a spike test, both configured to reach a throughput of 250 requests per second, as required by the acceptance criteria (90th percentile response time below 2 seconds). The repository includes execution instructions, generated reports, and an analysis explaining whether the acceptance criteria were met, along with the reasons for the observed results.

# Pre-requirements:

| Requirement                     | Version        | Note         |
| :------------------------------ |:---------------| :----------- |
| Java                            | 17             | -            |
| JMeter                          | 5.6.3          | -            |  
| JMeter Plugins Manager          | 1.12           | -            | 
| jpgc - Standard Set             | 2.0            | -            | 

# Installation:

- See [Java SE 17 Archive Downloads](https://www.oracle.com/br/java/technologies/javase/jdk17-archive-downloads.html), download the proper version for your OS and install it by keeping the preferenced options.
    - Right click :point_right: **My Computer** and select :point_right: **Properties**. On the :point_right: **Advanced** tab, select :point_right: **Environment Variables**, :point_right: **New** in System Variables frame and create a variable called JAVA_HOME containing the path that leads to where the JDK software is located (e.g. C:\Program Files\Java\jdk-17).
    - Right click :point_right: **My Computer** and select :point_right: **Properties**. On the :point_right: **Advanced** tab, select :point_right: **Environment Variables**, and then edit Path system variable with the new %JAVA_HOME%\bin entry.
- See [JMeter download](https://jmeter.apache.org/download_jmeter.cgi) and download apache-jmeter-5.6.3.zip. Extract it in the prorject directory. Access bin folder and look for ApacheJMeter.jar file. Double click it to execute it.
- See [JMeter Plugins Manager](https://jmeter-plugins.org/wiki/PluginsManager/) and download jmeter-plugins-manager-1.10.jar. Paste it into JMeter's lib/ext directory. Restart JMeter to conclude the installation of JMeter Plugins Manager.
- Hit :point_right:**Options**, :point_right:**Plugins Manager**, :point_right:**Available Plugins**, check :white_check_mark:**jpgc - Standard Set** and :point_right:**Apply Changes and Restart JMeter** to install jpgc - Standard Set plugin. 

# Tests:

- ```apache-jmeter-5.6.3\bin\jmeter.bat -n -t "apache-jmeter-5.6.3\bin\jp@gc - Ultimate Thread Group - load.jmx" -l "resultado_load.jtl" -e -o "reports\load"``` to run the load test.
- ```apache-jmeter-5.6.3\bin\jmeter.bat -n -t "apache-jmeter-5.6.3\bin\jp@gc - Ultimate Thread Group - spike.jmx" -l "resultado_spike.jtl" -e -o "reports\spike"``` to run the spike test.

# Support:

- [Stepping Thread Group](https://jmeter-plugins.org/wiki/SteppingThreadGroup/)
- [Apache JMeter Github Action](https://github.com/marketplace/actions/apache-jmeter)

# Tips:

- JMeter, even with the jpgc - Standard Set plugin support, is not able to execute a ramp down procedure. Threads are abruptly removed according to the number of threads to be removed per second.



