## dataset

​	2014年初以来按Carat使用的总持续时间排名的前1000名用户的记录（JSON）：	

- uuid
- timestamp
- batteryLevel
- batteryStatus
- timeZone
- mobileCountyCode
- apps (appName+appCategory)

### 1.json=>csv

```python
# json转csv
def trans(path):
    # 注意前十个和后面的文件名不一样
    jsonData = codecs.open('data/00-49/0049_json/part-000'+path, 'r', 'gbk')
    csvfile = open('data/00-49/0049_csv/part-000'+path+'.csv', 'w', newline='')  # python3下
    writer = csv.writer(csvfile, delimiter=',')
    flag = True
    for line in jsonData:
        dic = json.loads(line[0:-1])  # key-value
        if flag:
            # 获取属性列表
            # print(dic)
            keys = list(dic.keys())
            # print (keys)
            writer.writerow(keys) # 将属性列表写入csv中
            writer.writerow(list(dic.values()))
            flag = False
        else:
            # 读取json数据的每一行，将values数据一次一行的写入csv中
            writer.writerow(list(dic.values()))
    jsonData.close()
    csvfile.close()
```

### 2.根据用户归类

```python
# 按用户分类
def csv_to_uuid(n):
    with open('data/00-49/0049_csv/part-000'+n+'.csv', 'r') as f:
        reader = csv.reader(f)
        for row in reader:
            # print(row)
            # 按uuid进行合并
            if row[0] not in uuid:
                uuid.append(row[0])
                uuid_csv_file = open('data/00-49/00-49_csv/' + row[0] + '.csv', 'w', newline='')  # 'write' 创建表格
                writer = csv.writer(uuid_csv_file, delimiter=',')
                writer.writerow(row)
            else:
                uuid_csv_file = open('data/00-49/00-49_csv/' + row[0] + '.csv', 'a', newline='')  # 'add' 内容追加模式
                writer = csv.writer(uuid_csv_file, delimiter=',')
                writer.writerow(row)
            uuid_csv_file.close()
        uuid_csv=open('data/00-49_uuid_csv.csv','w',newline='')
        writer=csv.writer(uuid_csv,delimiter=',')
        writer.writerow(uuid)  # 会覆盖
        uuid_csv.close()
```

### 3.将时间序列转为日期，并按日期排序

```python
def ts_to_date(filename):
    col = ['uuid', 'timestamp', 'batteryLevel', 'batteryStatus', 'timeZone', 'mobileCountryCode', 'apps']
    # 读取csv文件并添加列名
    df = pd.read_csv('data/00-49/user/' + filename + '.csv', names=col)

    # 升序
    dataframe = df.sort_values(by='timestamp')
    # 通过to_date()将时间序列修改为日期
    dataframe['timestamp'] = dataframe['timestamp'].apply(lambda x: to_date(x))
    # 写入文件
    dataframe.to_csv('data/00-49/user_sort/' + filename + '.csv', mode='w', index=None)
```

### 4.去重

```python
def deduplicate(filename):
    with open('data/00-49/user_sort/'+filename+'.csv', 'r') as f:
        dedup=open('data/00-49/user_deduplication/'+filename+'.csv',
                  'w',newline='')
        writer=csv.writer(dedup,delimiter=',')
        reader = csv.reader(f)
        timestamp=[]
        for row in reader:
            if row[1] not in timestamp:
                timestamp.append(row[1])
                writer.writerow(row)
        dedup.close()
        f.close()
```

### 5.应用分类和统计

这里数据量比较大，跑得很慢

![image-20201212134852195](C:\Users\okey_upppp\AppData\Roaming\Typora\typora-user-images\image-20201212134852195.png)

```python
def get_categories(filename):
    with open('data/00-49/timestamp_and_apps/'+filename+'.csv','r') as f:
        reader=csv.reader(f)
        categories = []
        for row in reader:
            row.pop(0)
            for app in row:
                json_app=json.loads(app)
                category = get_category_of_app(json_app['processName'])
                if category not in categories:
                    categories.append(category)
            print(categories)
        categories_file = open('data/00-49/user_with_categories/' + filename + '.csv', 'w', newline='')
        writer = csv.writer(categories_file, delimiter=',')
        writer.writerow(categories)
        categories_file.close()
        f.close()
```