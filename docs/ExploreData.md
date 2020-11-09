The following steps assume that you have authorization to edit reports in **SAS Visual Analytics**.

**Create a Report**

1.  From SAS Drive, click ![pic_a](/images/pic_a.png) in the application bar. Using the side menu, click **Explore and Visualize Data**. SAS Visual Analytics displays the home page or go to http://*host*/SASVisualAnalytics. 
The *host* is the system where SAS Visual Analytics is installed.
2.  Click **Data** in the home page.
3.  Click **Import**, go to **Local File** and browse to folder containing the csv file. Select parking_floodlights_data.csv and click **Open**.
4.  Select table name and target destination, click **Import Item**.
5.  Click **OK** after table is successfully imported. 

 ![pic1](/images/pic1.PNG)

6.  Click **Data** and change properties of sequence_id & day variables from measure to category
7.  Click **Objects** and drag **Line Chart** to the canvas
8.  Select variables light_1 as **Measure** and sequence_id as **Category** under **Data Roles**
9.  Click on **Filters** on right of the canvas.
10. Add a new filter and select values 1 through 16 for day variable. 
11. Go to **Roles** and add light_2-light_6 under measures. 
12. Click **Options** , select **Measure Layout** as ‘Separate Axes’ under **Line**. 
13. Expand **Object** under **Options** tab. Check box for *Override system data limit* and change value to 10000. 
![data1a](/images/data1a.png)
14. Repeat steps 10-13 for last 16 days. Or duplicate the line chart and change filter values to select last 16 days. 
![data2a](/images/data2a.png)
