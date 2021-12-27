# Django

# 一、ORM

## 一、关系字段类型

#### 1.1 ForeignKey
1. ForeignKey 外键（一对多，也可一对一）：
将字段定义在多的一端中

#### 1.2 ManyToManyField
1. ManyToManyField: 多对多，定义在两端中/或只定义在任一端中

#### 1.2.2
1. 自定义第三张表

```python
class AlertRoleDBM(models.Model):
    """告警规则表"""
    id = models.AutoField(primary_key=True)
    resource_type = models.CharField(max_length=32, choices=ResourceType, null=True)
    type = models.CharField(null=True, max_length=64)    # 告警类型
    trigger_value = models.CharField(null=True, max_length=64)  # 触发值   >80%

class AlertGroupDBM(models.Model):
    """告警通知组, 组合:哪些规则需要通知哪些收件人
    """
    id = models.AutoField(primary_key=True)
    name = models.CharField(null=True, max_length=64)  # 名称
    alert_role = models.ManyToManyField(AlertRoleDBM, through='AlertGroupRoleDBM')
class AlertGroupRoleDBM(models.Model):
    """group_role_relate， 第三张表"""
    id = models.AutoField(primary_key=True)
    alert_group = models.ForeignKey(AlertGroupDBM, on_delete=models.CASCADE)
    alert_role = models.ForeignKey(AlertRoleDBM, on_delete=models.CASCADE)
```

2. 查询：

```python
ag = AlertGroupDBM.objects.get(id=1)
role_list = ag.alert_role.all()
```

3. 更新关系

```python
ale_group = AlertGroupDBM.objects.get(id=alert_log_id)
if alert_role_ids:
    ale_group.alert_role.clear()
    for al in alert_role_ids:
        al_obj = AlertRoleDBM.objects.get(id=int(al))
        if al_obj:
            AlertGroupRoleDBM.objects.create(alert_group=ale_group, alert_role=al_obj)
```

#### 1.3 OneToOneField
1. OneToOneField：一对一，将字段定义在任意一端中

## 二、关联查询语法

#### 2.1 一到多的访问语法（对象.模型类小写_set）
1. 对象.模型类小写_set
2. 图书：英雄 ** 1对多
2. bookinfo.heroinfo_set

#### 2.2 一到一查询（对象.模型类小写）
1. heroinfo.bookinfo

#### 2.3 访问关联对象的id语法（对象.属性_id）
1. heroinfo.book_id

## 三、模型的方法、属性及Manager管理器

#### 3.1 模型方法
1. str: 将对象转为字符串时调用
2. save(): 将模型对象保存到数据表中
3. delete(): 将模型对象从数据表中删除

#### 3.2 属性及Manager管理器
1. 属性objects：默认的管理器，是Manager类型的对象，与数据库进行交互
2. 当定义模型类时，Django默认生成一个objects管理器，也可自定义管理器
3. 管理器是Django的模型进行数据库操作的接口，自定义管理器主要用于：
- 修改原始查询集，重写get_queryset()方法
- 向管理器类中添加额外的方法，如创建对象
4. 当创建模型类对象时，django不会对数据库进行读写操作，调用save()方法才与数据库交互，进行insert或update操作，将数据保存到数据库中

## 四、查询集

#### 4.1 基本情况
1. 查询集表示从数据库中获取的对象集合，从Sql的角度，查询集和select语句等价，过滤器像where和limit子句
2. 特性1，惰性执行：创建查询集不会访问数据库，直到调用数据时，才会访问数据库
3. 特性2，缓存：查询集的结果被存下来之后，再次查询时会使用之前缓存的数据

#### 4.1 返回列表的过滤器
1. all()，所有
2. filter(), 过滤
3. **exclude(), 过滤取反**
4. order_by(), 排序

#### 4.2 返回单个的过滤器
1. get(), 单个对象
2. count(), 数量
3. aggregete(), 聚合
4. exists(), 有返回True, 没有返回False

## 五、条件查询

#### 5.1 字段查询
1. **属性名称__比较运算符=值**
2. eg:filter(id__exact=1), exact 判等
3. contains：是否包含
4. startswith、endswith：以指定值开头或结尾
5. filter(btitle__isnull=False)， 不为空
6. in：是否包含在范围内
7. gt、gte、lt、lte：大于、大于等于、小于、小于等于

```python
# 查询用户的创建时间小于现在的时间
UserExtra.objects.filter(create_date__lt=datetime.now())
```

#### 5.2 关联查询
1. 类似于SQL中的join连接查询
2. **关联模型类名小写__属性名__运算符=值**
3. 查询图书，要求图书中英雄的描述包含‘八’
3. list = BookInfo.books.filter(heroinfo__hcontent__contains='八')

#### 5.3 F对象，两个属性之间比较
1. **两个属性比较，F(属性名)**
2. 查询阅读量大于等于评论量的图书
2. list = BookInfo.books.filter(bread__gte=F('bcommet'))

#### 5.4 Q对象，多个过滤器
1. 多个过滤器逐个调用表示逻辑与关系，同sql语句中where部分的and关键字
2. Q对象可以使用&、|连接，&表示逻辑与，|表示逻辑或
3. 查询阅读量大于20，或编号小于3的图书，只能使用Q对象实现
4. list = BookInfo.books.filter(Q(bread__gt=20) | Q(pk__lt=3))
5. BookInfo.books.filter(~Q(pk=3)) 不等于3的

#### 5.5 aaggregate()过滤器调用,聚合函数
1. 聚合函数包括：Avg，Count，Max，Min，Sum，被定义在django.db.models中
2. list = BookInfo.books.aggregate(Sum('bread'))  > 查询图书的总阅读量

## 六、优化查询

#### 6.1 select_related 外键，多找一
1. 在数据库有外键的时候，使用select_related()和prefetch_related()可以很好的减少数据库请求次数，从而调高性能。
2. 对于一对一和多对一（外键ForeignKey），可以使用select_related()来对QuerySet进行优化
3. select_related()**接受外键参数或外键的外键参数**，即多找一

#### 6.2 prefetch_related() 一找多
1. prefetch_related主要针一对多和多对多关系进行优化。

```python
disks = DisksDBM.objects.select_related('node')
for 
1. nodes = NodesDBM.objects.prefetch_related('disksdbm_set').all()
2. DisksDBM.objects.select_related('node').filter(node__role_storage=True, node__deleted=False, sys_disk=False,osdsdbm=None,status='AVAILABLE',role='DATA').all()
```

## 六、portal_stor项目
#### 1、Rack：Node -> 1:N
#### 2、Node: Osd -> 1:N

#### 3、Pool: Osd -> N:N， pool中有字段osds
- 一找多，返回osd
- node_obj.osdsdbm_set.filter(delted=1)
- 多找一,filter中过滤
- Osd.objects.filter(poolsdbm__id=None,node__rack_id=None)

## 七、批量操作

#### 7.1 批量更新
1. Product.objects.filter(name__contains='name').**update**(name='new name')
2. 类似于update...where... SQL语句

#### 7.2 批量删除
1. Product.objects.filter(name__contains='name query').delete()
2. 类似于delete from...where...的SQL语句

#### 7.3 批量新增
1. bulk_create(对象list)：传入对象列表即可
2. **Product.objects.bulk_create（new_obj_list）**

```python
product_list_to_insert = list()
for x in range(10):
    product_list_to_insert.append(Product(name='product name ' + str(x), price=x))
Product.objects.bulk_create(product_list_to_insert)
```

## 八、django中使用原生SQL

### 1. 使用extra
1. 查询人民邮电出版社出版并且价格大于50元的书籍

```python
Book.objects.filter(publisher__name='人民邮电出版社').extra(where=['price>50'])
```
### 2. 使用raw

```python
books=Book.objects.raw('select * from hello_book')
for book in books:
    print book
```
### 3. 自定义sql

```python
from django.db import connection
 
cursor = connection.cursor()
cursor.execute("insert into hello_author(name) VALUES ('郭敬明')")
cursor.execute("update hello_author set name='韩寒' WHERE name='郭敬明'")
cursor.execute("delete from hello_author where name='韩寒'")
cursor.execute("select * from hello_author")
cursor.fetchone()
cursor.fetchall()
```

# 二、Django工具

## 1. 获取请求的client_ip
1. **获得用户的IP地址**。但是有些网站服务器会使用ngix等代理http，或者是该网站做了负载均衡
2. 导致使用remote_addr抓取到的是1270.0.1，这时使用HTTP_X_FORWARDED_FOR才获得是用户的真实IP

```python
if request.META.has_key('HTTP_X_FORWARDED_FOR'):
    ip =  request.META['HTTP_X_FORWARDED_FOR']
else:
    ip = request.META['REMOTE_ADDR']
```

## 2. datetime时间字段
1. **auto_now**
每次保存对象时，自动将该字段设置为现在。对于“最后修改”的时间戳很有用。请注意，当前日期 总是 被使用，而不仅仅是一个你可以覆盖的默认值。
只有在调用 Model.save() 时，该字段才会自动更新。当以其他方式对其他字段进行更新时，如 QuerySet.update()，该字段不会被更新，尽管你可以在这样的更新中为该字段指定一个自定义值。
2. **auto_now_add**
当第一次创建对象时，自动将该字段设置为现在。对创建时间戳很有用。请注意，当前日期是 始终 使用的；它不仅仅是一个你可以覆盖的默认值。因此，即使你在创建对象时为该字段设置了一个值，它也会被忽略。如果你想修改这个字段，可以设置以下内容来代替 auto_now_add=True ：

```python
DateField
class DateField(auto_now=False, auto_now_add=False, **options)¶
一个日期，在 Python 中用一个 datetime.date 实例表示。有一些额外的、可选的参数。
auto_now_add、auto_now 和 default 选项是相互排斥的。这些选项的任何组合都会导致错误。
目前，将 auto_now 或 auto_now_add 设置为 True，将导致该字段设置为 editable=False 和 blank=True。

auto_now 和 auto_now_add 选项将始终使用创建或更新时 默认时区 的日期。如果你需要一些不同的东西，你可能需要考虑使用你自己的可调用的默认值，或者覆盖 save() 而不是使用 auto_now 或 auto_now_add ；或者使用 DateTimeField 而不是 DateField，并决定如何在显示时间处理从日期时间到日期的转换。
```

## 3、whoosh+HayStack+Jieba

#### 2.1 介绍
1. **haystack**是Django的开源**搜索框架**，该框架支持的搜索引擎：Solr, Elasticsearch, Whoosh, **Xapian
2. **搜索引擎：Whoosh**,它是由纯Python实现的全文搜索引擎，没有二进制文件，小巧、配置简洁，性能略低
3. **中文分词Jieba**,Whoosh自带英文分词，不支持中文分词，用Jieba替代

#### 2.2 具体使用
1. Models商品表正常编写
2. 安装3个模块：pip install whoosh django-haystack jieba
3. **修改whoosh的分词器为jieba中文分词器**
4. 修改settings文件，**添加 Haystack 到Django的 INSTALLED_APPS**
下

```python
HAYSTACK_CONNECTIONS = {
    'default': {
        'ENGINE': 'Core.whoosh_backend.WhooshEngine',
        'PATH': os.path.join(BASE_DIR, 'Resources', 'index', 'whoosh'),
    },
}
```

5. **建立索引文件**，在当前app目录下创建search_indexes.py

```python
from haystack import indexes
from goods.models import GoodsSKU


class GoodsSKUIndex(indexes.SearchIndex, indexes.Indexable):
    """建立索引时被使用的类"""
    text = indexes.CharField(document=True, use_template=True)

    def get_model(self):
        return GoodsSKU

    def index_queryset(self, using=None):
        return self.get_model().objects.all()
```

6. 每个索引里面必须有且只能有**一个字段为document=True**，这代表haystack 和搜索引擎将使用此字段的内容作为索引进行检索(primary field)
7. haystack提供了use_template=True在text字段，这样就允许我们**使用数据模板去建立搜索引擎索引的文件**，数据模板的路径为app/templates/search/indexes/goods/post_text.txt,文件名必须为要索引的类名_text.txt,其内容为：

```python
# 指明建立索引的字段
{{ object.name }}
{{ object.title }}
```

7. **配置搜索URL**

```python
path('search/', include('haystack.urls')),
```

8. **编写search.html**，在templates/search/ 文件夹下添加 search.html 文件：
9. settings.py 里加入以下配置，**实现自动刷新索引**（每当添加、修改、删除数据时，自动生成索引）
HAYSTACK_SIGNAL_PROCESSOR = "haystack.signals.RealtimeSignalProcessor"

## 4、FastDFS 分布式文件存储系统

#### 3.1 Django二次开发Fast DFS
1. Django admin站点默认把上传的文件存储到settings配置的MEDIA_ROOT目录下，存储的图片数量受限于Django服务器的磁盘大小，为了支持海量的图片存储，易于拓展存储空间，使用FastDFS分布式存储系统，若上传的文件重复（文件指纹一样），系统只存储一份。
2. 改变Django默认的文件存储方式，自定义文件存储类，继承Storage类。
3. 使用nginx做代理转发，支持大并发。nginx对接FastDFS。
