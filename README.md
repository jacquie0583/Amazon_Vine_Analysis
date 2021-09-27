# Amazon_Vine_Analysis
##  Overview of Project

For the previous project, I partnered with Jennifer, an account manager at BigMarket. We worked within the big data and Hadoop context, used MapReduce to facilitate the process for handling big data as well PySpark assisted in the process and handling of big data.  After having used these technologies, natural language processing (NLP) allowed us to collect and analyze text data in relation to big data.  Cloud services, Amazon Web Services (AWS), allowed for more scalability and performance. 
Since our work on the SellBy project was so successful, we were tasked with another, larger project: analyzing Amazon reviews written by members of the paid Amazon Vine program. The Amazon Vine program is a service that allows manufacturers and publishers to receive reviews for their products. Companies like SellBy pay a small fee to Amazon and provide products to Amazon Vine members, who are then required to publish a review.
In this project, we had access to approximately 50 datasets. Each one contains reviews of a specific product, from clothing apparel to wireless products.  We narrowed our focus to one of these datasets and used PySpark to perform the ETL process to extract the dataset, transform the data, connect to an AWS RDS instance, and load the transformed data into pgAdmin. Next, we used PySpark to determine if there was any bias toward favorable reviews from Vine members in the dataset. The report to summarize the analysis for the SellBy stakeholders follows and consists of two technical analyses, performing ETL on Amazon Product Reviews and a statistical analysis to determine Bias of Vine Reviews as well as a proposal for future statistical study.

##  Resources 

•	Data Source: SQL table schema.csv and Amazon ETL starter code.csv

•	Data Tools: Amazon_Reviews_ETL.ipynb and Vine_Review_Analysis.ipynb.

•	Software: Python 3.9, Visual Studio Code 1.50.0, Anaconda 4.8.5, Jupyter Notebook 6.1.4 and Pandas

•	Cloud Storage with S3 on AWS

###  Database Versus Data Storage
   •	A database is necessary for holding raw data such as CSVs, Excel files, and JavaScript Object Notation (JSON) files. This data doesn't need to be queried and analyzed for business decisions. The files still have structure and can be reviewed, but not nearly as efficiently as a database. This database has been carefully planned and structured so that data can be analyzed efficiently through queries. Doing so comes at a cost of processing data to fit all the rules and structures.
   
   •	Data storage is a place where large amounts of raw data can be kept without any munging or curating. Data storage allows us to keep data of different types or data we might want to parse in the future.
   
   •	The benefit of having dedicated data storage is that nothing limits the intake of data. Data can flow in constantly and be saved without having to worry if it fits the criteria of the database. We have seen this with our extract, transform, and load (ETL) process—the data storage can hold raw files, such as CSV or JSON, for different needs.
   
  •	AWS's Simple Storage Service
  
  •	S3 is Amazon's cloud file storage service that uses key-value pairs. Files are stored on multiple servers and have a high rate of availability of more than 99.9%. To store files, S3 uses buckets, which are similar to folders or directories on your computer. Buckets can contain additional folders and files. Each bucket must have a unique name across all of AWS.
  
  •	One of S3's perks is its fine-grained control over files. Each file or bucket can have different read and write permissions, which helps regulate what can be done with each file.
  
  •	S3 is also very scalable—you are not limited to the memory of one computer. As data flows in, more and more can be stored, as opposed to a local computer that is limited by available memory. Additionally, it offers availability—several team members can access massive amounts of data from one central location.
  
##  Process
### Extract
 Starts with creating a new notebook, installing/ importing Spark, java, Environment Variables, initiating a Spark 
 Session which will allow for the ability to write directly to our Postgres database. Followed by a code to download
 a Postgres driver that will allow Spark to interact with Postgres.  In Extraction, we can connect to data storage, 
 then extract that data into a DataFrame. Next, the file is read in with the read method and combined with the csv() 
 method, which pulls in our CSV stored in SparkFiles and infers the schema. Finally, an action is called to show and 
 confirm our data extraction.  
 
 ### Transform 
 In Transform, the raw data stored in S3 is available in a PySpark DataFrame, we can perform our transformations. 
 First, the two tables joined together. 
 ####  Joining Tables
<p align="center">
   <img width="600" height="200" src="https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/Image%205%20Joined%20taables.jpg">
</p
   
#### Dropping
   
<p align="center">
   <img width="500" height="150" src="https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/Image%206%20drop%20na.jpg">
</p>    
   
####  Cleaning

<p align="center">
  <img width="500" height="150"      src="https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/Image%208%20Claning%20up.jpg">
</p> 
 
 Next, selected columns are used to create three different DataFrames that
 match what is in the AWS RDS database. A DataFrame was designed to match the active_user table.  Once our data has
 been transformed to fit the tables in our database, we're ready to move on to the "Load" step.
     
 Load The final step is to get our transformed raw data into our database. PySpark can easily connect to a database
 to load the DataFrames into the table. First, we configured settings for RDS to allow the connection with AWS server
 and PgAdmin. We'll append to the current table because every time we run this ETL process, we'll want more data added
 to our database without removing any.  The cleaned DataFrames can then be written directly to our database by using
 the .write.jdbc method that takes in the parameters we set.  The connection string stored in jdbc_url is passed to
 the URL argument. The corresponding name of the table we are writing the DataFrame to. The mode we're using, which 
 is "append." The connection configuration we set up passed to the properties. Finally we run queries in pgAdmin on
 our database to confirm a successful load. 
 
###  Products of ETL:

The Amazon_Reviews_ETL.ipynb file does the following:

   o	An Amazon Review dataset is extracted as a DataFrame
     
   o	The extracted dataset is transformed into four DataFrames with the correct columns
     
   o	All four DataFrames are loaded into their respective tables in pgAdmin
     
1.	 Customer Table
The customers_table DataFrame was created by aggregating(counting) the reviews and chaining it to the groupby() function to create a new column, count(customer_id).  Other adjustments was the renaming the count(customer_id) column using the withColumnRenamed() function so it matched the schema for the customers_table in pgAdmin. The result follows:

<p align="center">
   <img width="300" height="500" src="https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/Image%201Cust%20T.png">
</p>

2.	Products Table
The products_table DataFrame was designed using the select() function to select the product_id and product_title, then drop duplicates with the drop_duplicates() function to retrieve only unique product_ids.  The result follows:

<p align="center">
   <img width="700" height="600" src="https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/image%203%20prod.jpg">
</p>

3.	Review id Table
The review_id_table DataFrame was organized using the select() function to select the columns that are in the review_id_table in pgAdmin, and convert the review_date column to a date using the code snippet.The final review_id_table DataFrame follows:

<p align="center">
   <img width="600" height="600" src="https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/image%202%20%20Rev%20T.png">
</p>


4.	Vine Table
The vine_table DataFrame was also created using the select() function to select only the columns that are in the vine_table in pgAdmin.
The final vine_table DataFrame follows:

Image 4<p align="center">
   <img width="600" height="600" src="https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/Image%204%20Vine%20T.jpg">
</p>


##  Determine Bias of Vine Reviews

Helpful Reviews (All) with 5 Star:  For all reviews and "helpful" reviews, around half of the ratings are 5 Star, which indicates that the Vine programs tend to give 5 Stars over any other rating. 

## Total Vine and non-Vine Reviews

From the data, 99.5 % of the reviews are from the non-Vine category leaving only .005 % contribution of the vine as a whole.

<p align="center">
   <img width="400" height="200" src="https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/Chart%201.png">
</p>
                                                                 

## 5-star Total Vine Reviews vs 5-star Total non-Vine Reviews
The 5-star non-Vine participants dominate the review percentage at 99.60 % as appose to the 5-star total Vine participants at 0.04 %


<p align="center">
   <img width="400" height="200" src="https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/Chart%202.jpg">
</p>

##  Percentage of were Vine reviews were 5-star and non-Vines
The results show that 5-star reviews from Vine members is 41.62% and the 5-star reviews from non-Vine members was 53.02%. The Non-vine reviews has a 11 % higher percentage of the 5-star reviews, this does represent significant difference..
https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/Chart%203.jpg
<p align="center">
   <img width="400" height="200" src="https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/Chart%203.jpg">
</p>
                                                                                                                  
                                                                                                                  
##  Summary of Total Star Reviews - Vine and non-Vines

<p align="center">
   <img width="400" height="200" src="https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/Chart%204.jpg">
</p>
                                       
                                       
<p align="center">
   <img width="400" height="200" src="https://github.com/jacquie0583/Amazon_Vine_Analysis/blob/main/Chart%205.jpg">
</p>
#  Proposed Analysis for the future
