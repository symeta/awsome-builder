# generate random data in json format
 
## description
in order to simulate the stream data processing, we need to generate real-time streaming data. the code below randomly generate json format records. the sample record is like below:
```json
{
 "rowkey": "601642222859", 
 'name': 'Nwyfr', 
 'age': '41', 
 'sex': 'woman', 
 'goods_no': '230121', 
 'goods_price': '259.55', 
 'store_id': '313019', 
 'goods_type': 'buy', 
 'tel': '13123468175', 
 'email': 'zSiAwZuJCN@163.com', 
 'buy_time': '2019-06-06'
}
```

## code
```python
import json
import random
import string
import sys
import time
import boto3
from datetime import datetime

alphabet_upper_list = string.ascii_uppercase
alphabet_lower_list = string.ascii_lowercase
STREAM_NAME = "ab23-test"

# Randomly generate a string of specified digits
def get_random(instr, length):
    # Obtain segments of a specified length from a specified sequence randomly and form an array, for example: ['a', 't', 'f', 'v', 'y']
    res = random.sample(instr, length)
    # Combine elements in the array into a character string.
    result = ''.join(res)
    return result

# Store the generated rowkey that does not exist.
rowkey_tmp_list = []
# Make a rowkey.
def get_random_rowkey():
    import time
    pre_rowkey = ""
    while True:
        # Obtain a two-digit number from 00 to 99, including 00 and 99.
        num = random.randint(00, 99)
        # Obtain the current 10-digit timestamp.
        timestamp = int(time.time())
        # str(num).zfill(2) If the string does not contain two digits, 0 is automatically added to the string.
        pre_rowkey = str(num).zfill(2) + str(timestamp)
        if pre_rowkey not in rowkey_tmp_list:
            rowkey_tmp_list.append(pre_rowkey)
            break
    return pre_rowkey

# Create a username.
def get_random_name(length):
    name = string.capwords(get_random(alphabet_lower_list, length))
    return name


# Obtain the age.
def get_random_age():
    return str(random.randint(18, 60))


# Obtain the gender.
def get_random_sex():
    return random.choice(["woman", "man"])


# Obtain the goods ID.
def get_random_goods_no():
    goods_no_list = ["220902", "430031", "550012", "650012", "532120","230121","250983", "480071", "580016", "950013", "152121","230121"]
    return random.choice(goods_no_list)

# Obtain the goods price (floating point number).
def get_random_goods_price():
    # Randomly generate the integer digits of the goods price. The value is a three-digit number ranging from 1 to 999, including 1 and 999.
    price_int = random.randint(1, 999)
    # Generate the number of decimal places in the price randomly. The value is a two-digit number ranging from 1 to 99, including 1 and 99.
    price_decimal = random.randint(1, 99)
    goods_price = str(price_int) +"." + str(price_decimal)
    return goods_price


# Obtain the shop ID.
def get_random_store_id():
    store_id_list = ["313012", "313013", "313014", "313015", "313016","313017","313018", "313019", "313020", "313021", "313022","313023"]
    return random.choice(store_id_list)

# Obtain the shopping behavior type.
def get_random_goods_type():
    goods_type_list = ["pv", "buy", "cart", "fav", "scan"]  #click, purchase, add, add to favorites, and browse
    return random.choice(goods_type_list)


# Obtain the phone number.
def get_random_tel():
    pre_list = ["130", "131", "132", "133", "134", "135", "136", "137", "138", "139", "147", "150",
                "151", "152", "153", "155", "156", "157", "158", "159", "186", "187", "188"]
    return random.choice(pre_list) + ''.join(random.sample('0123456789', 8))


# Obtain the email address.
def get_random_email(length):
    alphabet_list = alphabet_lower_list + alphabet_upper_list
    email_list = ["163.com", "126.com", "qq.com", "gmail.com", "huawei.com"]
    return get_random(alphabet_list, length) + "@" + random.choice(email_list)


# Obtain the goods purchase date (data of the last seven days).
def get_random_buy_time():
    buy_time_list = ["2019-06-08", "2019-06-09", "2019-06-10", "2019-06-11", "2019-06-07", "2019-06-12", "2019-06-06"]
    return random.choice(buy_time_list)


# Generate a piece of data.
def get_random_record():
    return {'rowkey': get_random_rowkey(), 
     'name': get_random_name(5), 
     'age': get_random_age(), 
     'sex': get_random_sex(), 
     'goods_no': get_random_goods_no(), 
     'goods_price': get_random_goods_price(), 
     'store_id': get_random_store_id(),
     'goods_type': get_random_goods_type(), 
     'tel': get_random_tel(), 
     'email': get_random_email(10),
     'buy_time': get_random_buy_time()}


# Obtain a random integer for sleep.
def get_random_sleep_time():
    return random.randint(5, 10)


def send_data(stream_name, kinesis_client):
    while True:
        data = get_random_record()
        partition_key = str(data["rowkey"])
        print(data)
        kinesis_client.put_record(
            StreamName=stream_name,
            Data=json.dumps(data),
            PartitionKey=partition_key)
    
if __name__ == '__main__':
    kinesis_client = boto3.client('kinesis')
    send_data(STREAM_NAME, kinesis_client)
```
