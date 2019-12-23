# RedShift Utils

  Amazon Redshift is a fully managed data warehouse service in the cloud. Its datasets range from 100s of gigabytes to a petabyte. The initial process to create a data warehouse is to launch a set of compute resources called nodes, which are organized into groups called cluster. After that you can process your queries.

# RedShift Documenations :
  https://docs.aws.amazon.com/redshift/latest/gsg/getting-started.html
  https://www.sqlbot.co/blog/aws-redshift-what-is-it-15-minute-tutorial-explanation
  
  
  

# Reference Data (S3 Load)
  https://docs.aws.amazon.com/redshift/latest/gsg/samples/tickitdb.zip
  
# How to connect Redshift Database :
  	i) .Connect directly using Using the Query Editor in Amazon GUI Console 
	ii) .To Install SQL Workbench/
		Driver: com.amazon.redshift.jdbc41.Driver
		URL: jdbc:redshift://exampleCluster.anc.us-west-2.redshift.amazonws.com:5439/dev
		PORT: 5439
		DB: dev
		Jar file Name: RedShiftJDBC541.jar 
		
#  Creating an IAM Role :
```
For any operation that accesses data on another AWS resource, your cluster needs permission to access the resource and the data on the resource on your behalf. An example is using a COPY command to load data from Amazon S3. You provide those permissions by using AWS Identity and Access Management (IAM). You do so either through an IAM role that is attached to your cluster or by providing the AWS access key for an IAM user that has the necessary permissions.

To best protect your sensitive data and safeguard your AWS access credentials, we recommend creating an IAM role and attaching it to your cluster. For more information about providing access permissions, see Permissions to Access Other AWS Resources.

In this step, you create a new IAM role that enables Amazon Redshift to load data from Amazon S3 buckets. In the next step, you attach the role to your cluster.


#To Create an IAM Role for Amazon Redshift
i. Sign in to the AWS Management Console and open the IAM console at https://console.aws.amazon.com/iam/.
ii. In the navigation pane, choose Roles.
iii.Choose Create role.
iv.In the AWS Service group, choose Redshift.
v.Under Select your use case, choose Redshift - Customizable then choose Next: Permissions.
vi.On the Attach permissions policies page, choose AmazonS3ReadOnlyAccess. You can leave the default setting for Set permissions boundary. Then choose Next: Tags.
vii. The Add tags page appears. You can optionally add tags. Choose Next: Review.
viii.For Role name, enter a name for your role. For this tutorial, enter myRedshiftRole.
Review the information, and then choose Create Role.

Choose the role name of the role you just created.

ix) Copy the Role ARN to your clipboard—this value is the Amazon Resource Name (ARN) for the role that you just created. You use that value when you use the COPY command to load data in Step 6: Load Sample Data from Amazon S3.

Now that you have created the new role, your next step is to attach it to your cluster. You can attach the role when you launch a new cluster or you can attach it to an existing cluster. In the next step, you attach the role to a new cluster. 		

```
# DataModel under dev database 

   	Cluster: Choose examplecluster
	Database name: Enter dev
	Database user: Enter awsuser
	Database password: Enter password that you specified when you created the cluster

Then choose Connect to database.
  ```
  
  create table users(
	userid integer not null distkey sortkey,
	username char(8),
	firstname varchar(30),
	lastname varchar(30),
	city varchar(30),
	state char(2),
	email varchar(100),
	phone char(14),
	likesports boolean,
	liketheatre boolean,
	likeconcerts boolean,
	likejazz boolean,
	likeclassical boolean,
	likeopera boolean,
	likerock boolean,
	likevegas boolean,
	likebroadway boolean,
	likemusicals boolean);

create table venue(
	venueid smallint not null distkey sortkey,
	venuename varchar(100),
	venuecity varchar(30),
	venuestate char(2),
	venueseats integer);

create table category(
	catid smallint not null distkey sortkey,
	catgroup varchar(10),
	catname varchar(10),
	catdesc varchar(50));

create table date(
	dateid smallint not null distkey sortkey,
	caldate date not null,
	day character(3) not null,
	week smallint not null,
	month character(5) not null,
	qtr character(5) not null,
	year smallint not null,
	holiday boolean default('N'));

create table event(
	eventid integer not null distkey,
	venueid smallint not null,
	catid smallint not null,
	dateid smallint not null sortkey,
	eventname varchar(200),
	starttime timestamp);

create table listing(
	listid integer not null distkey,
	sellerid integer not null,
	eventid integer not null,
	dateid smallint not null  sortkey,
	numtickets smallint not null,
	priceperticket decimal(8,2),
	totalprice decimal(8,2),
	listtime timestamp);

create table sales(
	salesid integer not null,
	listid integer not null distkey,
	sellerid integer not null,
	buyerid integer not null,
	eventid integer not null,
	dateid smallint not null sortkey,
	qtysold smallint not null,
	pricepaid decimal(8,2),
	commission decimal(8,2),
	saletime timestamp);
  
  
  ```
  
# Load Sample Data from Amazon S3

  ```
  
  copy users from 's3://<myBucket>/tickit/allusers_pipe.txt' 
credentials 'aws_iam_role=<iam-role-arn>' 
delimiter '|' region '<aws-region>';

copy venue from 's3://<myBucket>/tickit/venue_pipe.txt' 
credentials 'aws_iam_role=<iam-role-arn>' 
delimiter '|' region '<aws-region>';

copy category from 's3://<myBucket>/tickit/category_pipe.txt' 
credentials 'aws_iam_role=<iam-role-arn>' 
delimiter '|' region '<aws-region>';

copy date from 's3://<myBucket>/tickit/date2008_pipe.txt' 
credentials 'aws_iam_role=<iam-role-arn>' 
delimiter '|' region '<aws-region>';

copy event from 's3://<myBucket>/tickit/allevents_pipe.txt' 
credentials 'aws_iam_role=<iam-role-arn>' 
delimiter '|' timeformat 'YYYY-MM-DD HH:MI:SS' region '<aws-region>';

copy listing from 's3://<myBucket>/tickit/listings_pipe.txt' 
credentials 'aws_iam_role=<iam-role-arn>' 
delimiter '|' region '<aws-region>';

copy sales from 's3://<myBucket>/tickit/sales_tab.txt'
credentials 'aws_iam_role=<iam-role-arn>'
delimiter '\t' timeformat 'MM/DD/YYYY HH:MI:SS' region '<aws-region>';

  
  ```
# Select the data from Amazon Redshift Database :
  ```
  
  -- Get definition for the sales table.
SELECT *    
FROM pg_table_def    
WHERE tablename = 'sales';    

-- Find total sales on a given calendar date.
SELECT sum(qtysold) 
FROM   sales, date 
WHERE  sales.dateid = date.dateid 
AND    caldate = '2008-01-05';

-- Find top 10 buyers by quantity.
SELECT firstname, lastname, total_quantity 
FROM   (SELECT buyerid, sum(qtysold) total_quantity
        FROM  sales
        GROUP BY buyerid
        ORDER BY total_quantity desc limit 10) Q, users
WHERE Q.buyerid = userid
ORDER BY Q.total_quantity desc;

-- Find events in the 99.9 percentile in terms of all time gross sales.
SELECT eventname, total_price 
FROM  (SELECT eventid, total_price, ntile(1000) over(order by total_price desc) as percentile 
       FROM (SELECT eventid, sum(pricepaid) total_price
             FROM   sales
             GROUP BY eventid)) Q, event E
       WHERE Q.eventid = E.eventid
       AND percentile = 1
ORDER BY total_price desc;

  ```
  
