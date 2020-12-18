### 1.热力图

对160个用户的使用app进行类别统计，并计算每个类别的比例

```python
cates=[]
_2017=[]
xlabelx=["2017"]
for i in categories:
    categories[i]=categories[i]/160
    cates.append(i)
    _2017.append([categories[i]])
```

创建热图进行分析

```
sns.heatmap(_2017, cmap='Reds',xticklabels=xlabelx,yticklabels=cates)
plt.show()
```

![heat map.png](https://github.com/1397201241/img/blob/main/heat%20map.png?raw=true)

### 2.CDF图

统计每个用户使用app的类别数

```python
def user_app_cate_num(filename):
    with open('../data/00-49/user_with_categories/' + filename + '.csv', 'r') as f:
        reader = csv.reader(f)
        for row in reader:
            categories.append(len(row) - 1)
```

![categories.png](https://github.com/1397201241/img/blob/main/categories.png?raw=true)

传入数据绘制CDF图

```python
# =============绘制cdf图===============
ecdf = sm.distributions.ECDF(categories)
print(ecdf)
#等差数列，用于绘制X轴数据
x = np.linspace(min(categories), max(categories))
# x轴数据上值对应的累计密度概率
y = ecdf(x)
# 绘制阶梯图
plt.plot(x, y)
plt.title('CDF of Categories (some users in 2017)')
plt.xlabel('number of categories')
plt.ylabel('CDF')
plt.show()
```

如图![cdf.png](https://github.com/1397201241/img/blob/main/cdf.png?raw=true)



### 3.绘制类别条形图

```python
#===============绘制条形图=============
fig,ax0 = plt.subplots(nrows=1,figsize=(6,6))
#第二个参数是柱子宽一些还是窄一些，越大越窄越密
ax0.hist(categories,10,density=1,histtype='bar',facecolor='yellowgreen',alpha=0.75)
plt.show()
```

![Bar graph.png](https://github.com/1397201241/img/blob/main/Bar%20graph.png?raw=true)

