# complicated raw json struct transformation process
 
## description
the targeted json is an embedded struct json. the objective of the transformation process is to flatten the struct, make it ready for the downstreaming realatinal dabase sql query.
the struct of the json is shown as below:
```
{cm: 
 {
	ln: string,
	sv: string,
	os: string,
	g: string,
	mid: string,
	nw: string,
	l: string,
	vc: string,
	hw: string
 },	
 ap: string,
 et:
 	[
 		ett: string,
 		en: string,
 		kv:
 		{
 			goodsid: string,
 			action: string,
 			extend1: string,
 			place: string,
 			category: string,
 			entry: string
 		}
 	]
}
```json
## solution
apply relationalize function of spark dataframe to achieve the data flattening. the sample code is as below:
```
import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.job import Job
#from awsglue.transforms import Relationalize

# Begin variables to customize with your information
glue_source_database = "ab23-db"
glue_source_table = "event_topic"
glue_temp_storage = "s3://event-example/temp"
glue_relationalize_output_s3_path1= "s3://ab23-batch-raw/event_flatten1/"
glue_relationalize_output_s3_path2= "s3://ab23-batch-raw/event_flatten2/"

glueContext = GlueContext(SparkContext.getOrCreate())
raw_df = glueContext.create_dynamic_frame.from_catalog(
	database=glue_source_database, 
	table_name=glue_source_table)
#raw_df.printSchema()

dfc = raw_df.relationalize('root',glue_temp_storage)
dfc.keys()

df1= dfc.select('root')
blogdataoutput1 = glueContext.write_dynamic_frame.from_options(
	frame=df1, 
	connection_type = "s3", 
	connection_options = {"path": glue_relationalize_output_s3_path1}, 
	format = "json", transformation_ctx = "event-main")

df2=dfc.select('root_et')
blogdataoutput2 = glueContext.write_dynamic_frame.from_options(frame=df2, 
	connection_type = "s3", 
	connection_options = {"path": glue_relationalize_output_s3_path2}, 
	format = "json", transformation_ctx = "event-detail")
```python
*the code file named json-flatten.py can be achieved by downloading from the blog.*
## solution diagram
the overall solution has glue crawler and glue catalog involved. the solution diagram is show as below:
![image](https://user-images.githubusercontent.com/97269758/151690380-6616c81c-a19a-4da5-953f-04f27033a754.png)





