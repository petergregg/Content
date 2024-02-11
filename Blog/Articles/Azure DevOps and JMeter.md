---
title: Azure DevOps and JMeter
author: Peter Gregg
description: Azure DevOps and JMeter
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2023/11/26 16:11:00
categories: 
  - Azure DevOps
  - JMeter
  - Azure Pipelines
  - Load Test
---

# Azure DevOps and JMeter
This article was inspired by the [Run nonfunctional tests in Azure Pipelines Module](https://learn.microsoft.com/en-us/training/modules/run-non-functional-tests-azure-pipelines).

## Prerequisites

The following prerequisites will be required to complete this tutorial:
- Azure DevOps account. If you don't have an Azure DevOps account,  [create one for free](https://azure.microsoft.com/en-us/products/devops/) before you begin.
- Azure DevOps project, with a dotnet core project in a Git repository. If you don't have a repo [fork the articles Git repo](https://twogsdev.visualstudio.com/_git/CoreDeployTest).
- A hosted Website which you own.

# Create a JMeter Load Test

1. In the root of an Azure DevOps Project, select **Repos**.

    ![Azure DevOps Repos Files Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsReposFilesNavigation.png)

2. Whilst in the root of your repo, select **More actions**, then **+ New**, and then **Folder**.

    ![Azure DevOps Repos Files Add New File](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsReposFilesAddNewFile.png)

4. In the Modal Popup, add the folder name  **JMeter** into the **New folder name** text box and the file name **LoadTest.jmx** into the text box.

    ![Azure DevOps Repos Files Add New File Modal Popup](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsReposFilesAddNewFolderFileModalPopup.png)

3. Add the following contents to the **LoadTest.jmx** file.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <jmeterTestPlan version="1.2" properties="5.0" jmeter="5.1.1 r1855137">
      <hashTree>
        <TestPlan guiclass="TestPlanGui" testclass="TestPlan" testname="Test Plan 1" enabled="true">
          <stringProp name="TestPlan.comments">A comment!</stringProp>
          <boolProp name="TestPlan.functional_mode">false</boolProp>
          <boolProp name="TestPlan.tearDown_on_shutdown">true</boolProp>
          <boolProp name="TestPlan.serialize_threadgroups">false</boolProp>
          <elementProp name="TestPlan.user_defined_variables" elementType="Arguments" guiclass="ArgumentsPanel" testclass="Arguments" testname="User Defined Variables" enabled="true">
            <collectionProp name="Arguments.arguments">
              <elementProp name="hostname" elementType="Argument">
                <stringProp name="Argument.name">hostname</stringProp>
                <stringProp name="Argument.value"></stringProp>
                <stringProp name="Argument.metadata">=</stringProp>
              </elementProp>
            </collectionProp>
          </elementProp>
          <stringProp name="TestPlan.user_define_classpath"></stringProp>
        </TestPlan>
        <hashTree>
          <ThreadGroup guiclass="ThreadGroupGui" testclass="ThreadGroup" testname="Thread Group" enabled="true">
            <stringProp name="ThreadGroup.on_sample_error">continue</stringProp>
            <elementProp name="ThreadGroup.main_controller" elementType="LoopController" guiclass="LoopControlPanel" testclass="LoopController" testname="Loop Controller" enabled="true">
              <boolProp name="LoopController.continue_forever">false</boolProp>
              <stringProp name="LoopController.loops">10</stringProp>
            </elementProp>
            <stringProp name="ThreadGroup.num_threads">10</stringProp>
            <stringProp name="ThreadGroup.ramp_time">10</stringProp>
            <boolProp name="ThreadGroup.scheduler">false</boolProp>
            <stringProp name="ThreadGroup.duration"></stringProp>
            <stringProp name="ThreadGroup.delay"></stringProp>
          </ThreadGroup>
          <hashTree>
            <ConfigTestElement guiclass="HttpDefaultsGui" testclass="ConfigTestElement" testname="HTTP Request Defaults" enabled="true">
              <elementProp name="HTTPsampler.Arguments" elementType="Arguments" guiclass="HTTPArgumentsPanel" testclass="Arguments" testname="User Defined Variables" enabled="true">
                <collectionProp name="Arguments.arguments"/>
              </elementProp>
              <stringProp name="HTTPSampler.domain">${__P(hostname)}</stringProp>
              <stringProp name="HTTPSampler.port"></stringProp>
              <stringProp name="HTTPSampler.protocol"></stringProp>
              <stringProp name="HTTPSampler.contentEncoding"></stringProp>
              <stringProp name="HTTPSampler.path"></stringProp>
              <stringProp name="HTTPSampler.concurrentPool">6</stringProp>
              <stringProp name="HTTPSampler.connect_timeout"></stringProp>
              <stringProp name="HTTPSampler.response_timeout"></stringProp>
            </ConfigTestElement>
            <hashTree/>
            <HTTPSamplerProxy guiclass="HttpTestSampleGui" testclass="HTTPSamplerProxy" testname="HTTP Request" enabled="true">
              <elementProp name="HTTPsampler.Arguments" elementType="Arguments" guiclass="HTTPArgumentsPanel" testclass="Arguments" testname="User Defined Variables" enabled="true">
                <collectionProp name="Arguments.arguments"/>
              </elementProp>
              <stringProp name="HTTPSampler.domain"></stringProp>
              <stringProp name="HTTPSampler.port"></stringProp>
              <stringProp name="HTTPSampler.protocol"></stringProp>
              <stringProp name="HTTPSampler.contentEncoding"></stringProp>
              <stringProp name="HTTPSampler.path"></stringProp>
              <stringProp name="HTTPSampler.method">GET</stringProp>
              <boolProp name="HTTPSampler.follow_redirects">true</boolProp>
              <boolProp name="HTTPSampler.auto_redirects">false</boolProp>
              <boolProp name="HTTPSampler.use_keepalive">true</boolProp>
              <boolProp name="HTTPSampler.DO_MULTIPART_POST">false</boolProp>
              <stringProp name="HTTPSampler.embedded_url_re"></stringProp>
              <stringProp name="HTTPSampler.connect_timeout"></stringProp>
              <stringProp name="HTTPSampler.response_timeout"></stringProp>
            </HTTPSamplerProxy>
            <hashTree/>
          </hashTree>
          <ResultCollector guiclass="SummaryReport" testclass="ResultCollector" testname="Summary Report" enabled="true">
            <boolProp name="ResultCollector.error_logging">false</boolProp>
            <objProp>
              <name>saveConfig</name>
              <value class="SampleSaveConfiguration">
                <time>true</time>
                <latency>true</latency>
                <timestamp>true</timestamp>
                <success>true</success>
                <label>true</label>
                <code>true</code>
                <message>true</message>
                <threadName>true</threadName>
                <dataType>true</dataType>
                <encoding>false</encoding>
                <assertions>true</assertions>
                <subresults>true</subresults>
                <responseData>false</responseData>
                <samplerData>false</samplerData>
                <xml>true</xml>
                <fieldNames>true</fieldNames>
                <responseHeaders>false</responseHeaders>
                <requestHeaders>false</requestHeaders>
                <responseDataOnError>false</responseDataOnError>
                <saveAssertionResultsFailureMessage>true</saveAssertionResultsFailureMessage>
                <assertionsResultsToSave>0</assertionsResultsToSave>
                <bytes>true</bytes>
                <sentBytes>true</sentBytes>
                <url>true</url>
                <threadCounts>true</threadCounts>
                <idleTime>true</idleTime>
                <connectTime>true</connectTime>
              </value>
            </objProp>
            <stringProp name="filename">./Results.xml</stringProp>
            <boolProp name="ResultCollector.success_only_logging">true</boolProp>
          </ResultCollector>
          <hashTree/>
        </hashTree>
      </hashTree>
    </jmeterTestPlan>
    ```

  See my [Create a Performance Test with BlazeMeter and JMeter](https://www.pgdevopstips.co.uk/article/create-a-performance-test-with-blazemeter-and-jmeter) article to learn how to create a load test. 

# Create a Template for the JMeter report

1. Navigate to the **JMeter** folder in your repo, select **More actions**, then **+ New**, and then **File**.

2. In the Modal Popup, add the file name  **JMeter2JUnit.xsl** into the **New file name** text box.

3. Add the following contents to the **JMeter2JUnit.xsl** file.

    ```xml
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:math="http://exslt.org/math">
      <xsl:output method="xml" indent="yes" encoding="UTF-8"/>
      <xsl:template match="/testResults">
        <xsl:variable name="times" select="./httpSample/@t" />
        <xsl:variable name="failures" select="./httpSample/assertionResult/failureMessage" />
        <xsl:variable name="threshold" select="1000" />
        <testsuite>
          <xsl:attribute name="tests"><xsl:value-of select="count($times)" /></xsl:attribute>
          <xsl:attribute name="failures"><xsl:value-of select="count($failures)" /></xsl:attribute> 
          <testcase>
              <xsl:variable name="avg-time" select="sum($times) div count($times)" />
              <xsl:attribute name="name">Average Response Time</xsl:attribute>
              <xsl:attribute name="time"><xsl:value-of select="format-number($avg-time div 1000,'#.##')"/></xsl:attribute>
              <xsl:if test="$avg-time > $threshold">
                <failure>Average response time of <xsl:value-of select="format-number($avg-time,'#.##')"/> exceeds <xsl:value-of select="$threshold"/> ms threshold.</failure>
              </xsl:if> 
          </testcase>
          <testcase>
              <xsl:variable name="exceeds-threshold" select="count($times[. > $threshold])" />
              <xsl:attribute name="name">Max Response Time</xsl:attribute>
              <xsl:attribute name="time"><xsl:value-of select="math:max($times) div 1000"/></xsl:attribute>
              <xsl:if test="$exceeds-threshold > count($times) * 0.1">
                <failure><xsl:value-of select="format-number($exceeds-threshold div count($times) * 100,'#.##')"/>% of requests exceed <xsl:value-of select="$threshold"/> ms threshold.</failure>
              </xsl:if>
          </testcase>
        </testsuite>
      </xsl:template>
    </xsl:stylesheet>
    ```

### Create a Variables Group in Azure DevOps

1. In the root of an Azure DevOps Project, under the **Pipelines** section, select **Library**.

    ![Azure DevOps Pipelines Library Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesLibraryNavigation.png)

2. To create a new variable group select **+ Variable group**.

    ![Azure DevOps Pipelines Library Navigation Bar](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesLibraryNavigationBar.png)

4. In the new **Variable group**, enter the following values, replacing {YourWebsiteURL} with your website url. To add new variables in the **Variables** section, select **+ Add**.

    ![Azure DevOps Pipelines Library Navigation Bar](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesLibraryNewVariableGroup.png)

    | Section | Field | Value | Description |
    | --- | --- | --- | --- |
    | Properties | Variable group name | Release | Name of the Variable Group |
    | Variables | Name | STAGING_HOSTNAME | |
    | Variables| Value | {YourWebsiteURL} | Website URL to load test. You don't need to include the http:// or https:// protocol prefix in your value. JMeter provides the protocol when the tests run. |
    |Variables| Name | jmeterVersion | |
    | Variables | Value | 5.4.3 | Version of JMeter. To get the latest version, see Download Apache JMeter. |

7. To save the **Variable group** to the pipeline, select **Save** near the top of the page.

### Create and Run Azure DevOps JMeter Pipeline

1. In the root of an Azure DevOps Project, select **Pipelines**.

    ![Azure DevOps Pipelines Navigation](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesNavigation.png)

2. Select **Create Pipeline**.

    ![Azure DevOps Pipelines Create Your First Pipeline](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesCreateYourFirstPipeline.png)

3. In the **Connect** tab, Select **Azure Repos Git**.
    
    ![Azure DevOps Pipelines Connect Azure Repos Git](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesConnectAzureReposGit.png)

5. In the **Select** tab, select **Starter pipeline** YAML template.

    ![Azure DevOps Pipelines Select ASP.NET Core YAML template](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesStarterPipelineYamlTemplate.png)

6. Replace the Contents of the YAML file with the following YAML.

    ```yaml

    trigger:
    - master

    pool:
      vmImage: ubuntu-latest

    variables:
        - group: Release

    steps:
    - script: |
          wget -c archive.apache.org/dist/jmeter/binaries/apache-jmeter-$(jmeterVersion).tgz
          tar -xzf apache-jmeter-$(jmeterVersion).tgz
      displayName: 'Install Apache JMeter'
    - script: apache-jmeter-$(jmeterVersion)/bin/./jmeter -n -t JMeter/LoadTest.jmx -o Results.xml -Jhostname=$(STAGING_HOSTNAME)
      displayName: 'Run Load tests'
    - script: |
          sudo apt-get update
          sudo apt-get install xsltproc
          xsltproc JMeter/JMeter2JUnit.xsl Results.xml > JUnit.xml
      displayName: 'Transform JMeter output to JUnit'
    - task: PublishTestResults@2
      inputs:
        testResultsFormat: JUnit
        testResultsFiles: JUnit.xml

    ```

7. Rename the pipelines YAML file to **jmeter-az-pipeline.yml** and then select **Save and run**.

    ![Azure DevOps Pipelines Connect Azure Repos Git](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesJMeterLoadTestSaveAndRunNavigation.png)

8. Add a commit message and then select **Save and run**. 

    ![Azure DevOps Pipelines Rename the Pipelines Yaml File, Save and Run](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesJMeterLoadTestCommitSaveAndRun.png)

## Review JMeter Test Report and Fix

1. Navigate to **Pipelines**.

    ![Azure DevOps Pipelines JMeter](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesJMeterLoadTest.png)

3. In the **Runs** tab, select a pipeline that has been run. 

    ![Azure DevOps Pipelines Runs Pipelines Run](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesJMeterLoadTestRunsPipeline.png)

4. Select the **Tests** tab to view the JMeter test report.

    ![Azure DevOps Pipelines JMeter Test Report](https://raw.githubusercontent.com/petergregg/Content/main/Blog/Images/AzureDevOps/AzureDevOpsPipelinesJMeterLoadTestReport.png)

## Resources

- https://learn.microsoft.com/en-us/training/modules/run-non-functional-tests-azure-pipelines
- https://github.com/MicrosoftDocs/mslearn-tailspin-spacegame-web-deploy/tree/jmeter