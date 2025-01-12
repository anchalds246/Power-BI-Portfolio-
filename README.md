
# A DYNAMIC PERFORMANCE REPORT (SALES | QUANTITY | GROSS PROFIT)


## Problem Statement
This project presents a dynamic performance report using Power BI to analyze sales, gross profit, and quantity sold, while comparing performance over time through prior year values and year-to-date (YTD) metrics. A key focus was on identifying trends, highlighting account profitability, and pinpointing underperforming regions, particularly the bottom 10 countries with significant declines in YTD values compared to prior years (2022–2024). 

The analysis revealed that 2023 was a pivotal year, with an overall decline in YTD sales by £512.26k despite a notable increase in product quantity sold (+17.05k units). 

The most significant monthly decrease occurred in February 2023, with China emerging as the country experiencing the steepest YTD sales decline across all product types, while simultaneously selling more products year-to-date than the prior year. 

This comprehensive analysis helps in identifying areas of improvement and making data-driven decisions to optimize performance.

This project showcases my expertise in Power BI by leveraging data transformation, visualization, and analytics capabilities to provide actionable insights. Below are the steps and methodologies implemented in the project.



### Steps followed 

- Step 1 : Load data into Power BI Desktop, dataset is a csv file.
- Step 2 : Open power query editor & in view tab under Data preview section, check "column distribution", "column quality" & "column profile" options.
- Step 3 : Also since by default, profile will be opened only for 1000 rows so you need to select "column profiling based on entire dataset".
- Step 4 : It was observed that in none of the columns errors & empty values were present. 
- Step 5 : Rename the tables and columns to for better readability and remove the dublicates.  
- Step 6 : Created a seperate table for the date using CALANDER() DAX function. (for having all the dates from starting to end period to avoid inconsistancy)
        
        - Dim_Date = CALENDAR(min(Fact_sales[Date_Time]), max(Fact_sales[Date_Time])
- Step 7 : Created a new value table for sale , quantity and gross profit by using switch mesaure (to make report more dynamic).
        
        - Home tab > Enter data > columns> sales, Gross profit , Quantity > Rename > load

- Step 8 : Created a new table for all measures. Named it as _measure
- Step 9 : Create new measure for sales, gross profit and quantity. 
        
                - Sales = SUM(Fact_sales[Sales_USD])
                - Quantity = SUM(Fact_sales[quantity])
                - Gross Profit = [Sales]-[COGS]
                - COGS = SUM(Fact_sales[COGS_USD])
 
- Step 10 : Calculated Sales, Gross Profit and Quantity for last year using SAMEPERIODLASTYEAR() DAX funcion.
        
        - Saleslastyear = CALCULATE([Sales], SAMEPERIODLASTYEAR(Dim_Date[Date]))
        - Quantitylastyear = CALCULATE([Quantity], SAMEPERIODLASTYEAR(Dim_Date[Date]))
        - Grossprofitlastyear = CALCULATE([Gross Profit], SAMEPERIODLASTYEAR(Dim_Date[Date]))
- Step 11 : Calculated Year to date values for Sales, Gross Profit and Quantity using TOTALYTD() DAX function.
   
        - Totalyeartodatesales = TOTALYTD([Sales], Fact_sales[Date_Time])
        - Tolalquantityyeartodate = TOTALYTD([Quantity], Fact_sales[Date_Time])
        - TotalGPyeartodate = TOTALYTD([Gross Profit], Fact_sales[Date_Time]) 

- Step 12 : Created switch measures between sales, quantity and gross profit by selecting particular values from the slicer table using SELECTVALUES() DAX function  
         
        
        Switch_lastyear = 
        VAR selected_values = SELECTEDVALUE(Slc_Values[Values])
        VAR result = SWITCH(selected_values, 
            "Sales", [Saleslastyear],
            "Quantity", [Quantitylastyear],
            "Gross Profit", [Grossprofitlastyear],
            BLANK())
            Return 
            result
                                          
        Switch_yeartodate = 
        VAR selected_values = SELECTEDVALUE(Slc_Values[Values])
        VAR result = SWITCH(selected_values, 
            "Sales", [Totalyeartodatesales],
            "Quantity", [Tolalquantityyeartodate],
            "Gross Profit", [TotalGPyeartodate],
            BLANK())
            Return 
            result
Step 13 : Created a new measure to Compare the sales between last year and year to date by taking their difference. 
   
          YTD vs Lastyear = [Switch_yeartodate] - [Switch_lastyear]
Step 14 : Calculted Gross profit % as : 
         
         Gross Profit% = DIVIDE([Gross Profit], [Sales])

### Dashboard Designing 
#### Card visualization
- Used 4 card visualization
- First shows the last year values 
- Second card shows the Year to date values
- Third card shows the Comparison between year to date(YTD) values and last year values. 
- Fourth card shows the GP%. 
#### Slicers 
- First slicer on values table which will select what performace we need to see ( Sales, Quantity, Gross Profit)
- Second Slicer is on Year ( 2022,2023, 2024)

#### Tree Map 
- Represent Bottom 10 countries whoes year to date values are less as comapre to the last year values. 
- To do this filter is used.
        
        Filters > Filter on this visual> Advance Filtering > Top N > Select bottom > 10> values > YTD vs Last year 

#### Waterfall Chart 
- Shows the comparison between YTD and last year values monthly. I also enabled drill down to show this comparision for countries and products. 
- Green bars shows the increase in YTD values from last year. 
- Red bars shows the decline in YTD values from last year. 

#### Column and line chart
- Line chart for last year values by month shown by red line 
- Column chart for Year to date values by month for different product types. 

#### Scatter plot 
- This plot shows where the accounts lies in terms of profitability. 
- X axis: shows the YTD values 
- Y axis - shows the GP% 
- Segmented by Accounts by putting accounts under values field. 
- The scatter plot comes out to be very crowded near the axis so zoom sliders are turned on to navigate to perticualr values. 
- Average lines were added for both the axis to know which accounts are generating more than average profit % shown by red colour. 
          
        visualization pane > add further analyses to your visual > Average line > + add line 1 > +add line 2 > Series > Select values > line > change color 

### Dashboard Formatting 
- Adjusted the shapes, sizes and colors of the vislual 
- Apply conditional formatting on the third card visualization that shows the comaprison between the YTD and last year values. 

![Condition](https://github.com/user-attachments/assets/72961fb5-62eb-4231-bc94-12d6ecbaa346)

- Same conditional formatting is applying to the background of the Third card visual. 
#### Dynamic Titles for the visuals
- I have also created dynamic titles for visulas so that each time title changes depending which performace we want to see ( sales, gross profit, quantiy) by using following DAX measures. 
         
         _waterfall title = SELECTEDVALUE(Slc_Values[Values]) & " YTD vs Last Year| Month-Country-Product"
         _scatter title = "Account Profitability Segmentation | GP% and " & SELECTEDVALUE(Slc_Values[Values]) 
         _column chart title = SELECTEDVALUE(Slc_Values[Values]) & " YTD & Last Year| Month"
         _Report TITLE = "Plant Co. " & SELECTEDVALUE(Slc_Values[Values]) & " Performance " & SELECTEDVALUE(Dim_Date[Date].[Year])
        
- The titles has been changed using conditional formatting shown below:
![title ](https://github.com/user-attachments/assets/56358c33-7735-4c7a-83d3-e6b92992b897)

- In same way titles for each visualization has been changed

### Dashboard has been published on POWER BI SERVICE. 
