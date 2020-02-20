# Build and Test Streaming model in SAS ESP Studio

The following steps assume that you have access to SAS Event Stream Processing Studio and authorized to start ESP server.

## 1. Required Steps
1. Access SAS Event Stream Processing Studio by opening following URL: https://*host*/SASEventStreamProcessingStudio
The host is the system where SAS Event Stream Processing Studio is installed.
2. Start the Event Stream Processing Server. See the [link](https://go.documentation.sas.com/?cdcId=espcdc&cdcVersion=6.1&docsetId=espstudio&docsetTarget=n02795wpw65f79n1l8kls27e84l1.htm&locale=en#n0zk1grxfc2fron162rgxvg63dip) for details.
3. Open **ESP Servers** page to configure a new ESP server. 
   *  Click on ‘Add ESP Server’
   *  Add name to identify server
   *  Enter hostname
   *  Add HTTP Port
   *  Add description or tag if required.

## 2.a (Option 1) Upload Project

1.  Go to **Projects** page
    *  Click on **More actions**
    *  Select **Upload Projects** 
    *  Navigate to the location that contains the downloaded copy of [parking_lights.xml](project/parking_lights.xml) by clicking on **Upload Project files**
    *  Close and return to Project page
    *  New project named parking_lights is created, double click to open
    *  Review properties of source and calculate windows
    *  Make following changes
        *  Change path to Input Data Connector to reflect accessible location
        
        ![](images/pic5.png) 
        *  Similarly update path of Subscriber connector to reflect accessible location
    * Save the Project
    * Follow **Sections 4-5** as described below. 
    * See **Demo video** for more details. ![](docs/sst_floodlights.mp4) 


## 2.b (Option 2) Create Project
1. Go to **Projects** page
   *  Create new project
   *  Add a unique name for the project
   *  Optional: add description, tag. 
   *  Click OK
2. **SAS Event Stream Processing Studio Modeler** appears
   *  Add Source window from Input Streams to workspace
   *  Add name, description 
   *  Select *‘Stateful (pi_EMPTY)’* under **State and Event Type**. Check box for ‘Accept only “Insert” events’  
   *  Expand **Input Data (Publisher) Connectors**
   *  Add new connector
   *  Add details to receive data from csv file 
      *  Copy paste or type the path to the parking_floodlights_data.csv file under Fsname and select Fstype as csv
      ![](images/pic5.png) 
      *  Click ‘All Properties’ and add details from the table below and click ‘OK’
      
            | Property | Value |
            | :------ | :------: |
            | rate | 10 |
            | header | 1 | 
            | noautogenfield | true | 
            | addcsvopcode | true | 
            | addcsvflags | normal | 
            | repeatcount | 20 | 

   *  Click on Output schema on the right pane and edit rows   
   *  Click on Import Schema and select XML snippet.
   
      <img src="images/pic_b.png" width="400" > 

   *  Copy the xml below and paste it in the window. 
      ``` 
       <fields>
       <field name="day" type="string"/>
       <field name="sequence_id" type="int64" key="true"/>
       <field name="Light_1" type="double"/>
       <field name="Light_2" type="double"/>
       <field name="Light_3" type="double"/>
       <field name="Light_4" type="double"/>
       <field name="Light_5" type="double"/>
       <field name="Light_6" type="double"/>
       </fields>
      ```
   *  Add Calculate window from Analytics to workspace
   *  Connect the calculate window with source data
        ![](images/esp_project.png) 
   *  Select Name and Description 
   *  Expand Settings
      *  Select ‘Online algorithm’ under Calculation
      *  Select 'SST' under Algorithm
      *  Expand Parameters and add following parameters for window based SST. Leave the default values for remaining properties.
      
            | Name | Value |
            | :------ | :------: |
            | windowLength | 240 |
            | maxPrincipal | 2 |  
            | overlap | -1 |
        *  Select Light_1 through Light_6 from dropdown as inputs under Input Map ( *Tip* : **SHIFT** and select Light_1-Light_6 to select)
        *  Type an output name for each role under Output Map. These columns will be added to the schema. Click on Add new Item when prompted. 
        ![](images/pic6.png) 
        *  For principalVecOut type : pc_light_1, pc_light_2, pc_light_3, pc_light_4, pc_light_5, pc_light_6 for Name. Click on Add new Item when prompted. 
         <img src="images/pic7.png" width=600>
   *  Go to **Output Schema**, click on 'Edit rows'
        *  Click on Import Schema and copy fields from Input Schema
        *  Add new fields pc_light_1-pc_light_6 with type as 'double' 
        *  Or you can copy paste the syntax below in XML editor
            ``` 
            <fields>
                <field name="day" type="string"/>
                <field name="sequence_id" type="int64" key="true"/>
                <field name="Light_1" type="double"/>
                <field name="Light_2" type="double"/>
                <field name="Light_3" type="double"/>
                <field name="Light_4" type="double"/>
                <field name="Light_5" type="double"/>
                <field name="Light_6" type="double"/>
                <field name="PCAngleChangeOut" type="double"/>
                <field name="PCAbsoluteAngleOut" type="double"/>
                <field name="projAngleOut" type="double"/>
                <field name="residualRate" type="double"/>
                <field name="numRank" type="int32"/>
                <field name="pc_light_1" type="double"/>
                <field name="pc_light_2" type="double"/>
                <field name="pc_light_3" type="double"/>
                <field name="pc_light_4" type="double"/>
                <field name="pc_light_5" type="double"/>
                <field name="pc_light_6" type="double"/>
            </fields>
             ```
        ![](images/pic8.png)
   *  Save the Project 
   *  Follow **Sections 3-5** as described below.
    
## 3. *Optional*- Add Subscriber Connector to output results
*  Expand **Subscriber Connectors**
    *  Add new connector
    *  Add details to send data to a csv file 
    *  Check box for ‘Snapshot’
    *  Copy paste or type the path to the csv file under Fsname and select Fstype as csv`
    *  Click on ‘All Properties’ and select ‘full’ for header
*  Save the Project  

## 4. Test the Project
   *  Click on **Enter Test Mode**
   *  Click on **Run Test**
   *  The image below shows the streaming data coming in and results from SST. 
     ![](images/pic9.png)

## 5. Visualize Event Streams with Streamviwer
   * Access **SAS Event Stream Processing Streamviewer** by opening following URL: https://*Streamviewer-host-name*/SASEventStreamProcessingStreamviewer
   * If Streamviewer is not connected to an ESP server, you are prompted to enter a New ESP Server. Follow steps described in Section 1: Required Steps #3 above.
   * Create new dashboard
   * Click on Show model. This opens ESP Model Viewer. (If you don’t see any projects here then go to Enter Test Mode in ESP Studio and click on Run Test, this starts streaming data and can be viewed in ESP Streamviewer)
   * Highlight the SST-calculate window, it displays information regarding the window, fields, schema, etc. 
   * Add updating subscriber or streaming subscriber. 
   * A table is added to the dashboard. 
   * Click on ![](images/a6.png)  and select New Chart
   * Select Scatter plot and set values for X as Event and Y as Light_1, click OK
   * A chart is added to the dashboard
   * Repeat the steps to add charts for Light_2-Light_6 or other parameters from SST model
    ![](images/pic10.png)

Check [sample project template](https://gitlab.sas.com/IOT/accelerators/anomaly-detection-in-floodlights-for-smart-campus/blob/master/project/Parking_lights.xml) to compare project properties.

For more details see the [link.](https://go.documentation.sas.com/?cdcId=espcdc&cdcVersion=6.1&docsetId=espvisualize&docsetTarget=titlepage.htm&locale=en)






