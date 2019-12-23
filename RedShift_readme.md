# RedShift Utils

  Amazon Redshift is a fully managed data warehouse service in the cloud. Its datasets range from 100s of gigabytes to a petabyte. The initial process to create a data warehouse is to launch a set of compute resources called nodes, which are organized into groups called cluster. After that you can process your queries.

# RedShift Documenations :
  https://docs.aws.amazon.com/redshift/latest/gsg/getting-started.html

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
