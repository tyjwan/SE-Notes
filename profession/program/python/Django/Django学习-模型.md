# Django学习-模型
****
## 何为模型
&ensp;&ensp;&ensp;&ensp;官方文档里对模型是这样描述的：模型是你的数据的唯一的、权威的信息源。它包含你所储存数据的必要字段和行为。通常，每个模型对应数据库中唯一的一张表。
&ensp;&ensp;&ensp;&ensp;从数据库的角度来看：模型就是数据库中的一张表。而在Python中继承了Model类的就具有了相应的数据库中的表的性质了：一个类就是一张表，其中的成员便是列名（模型的字段）。

## 模型定义
- 用自定义的类继承models.Model
- 定义类的成员变量（表中的列名，也是模型的字段），等号右边定义成员的数据库中的变量属性
- 可以定义类的方法，自定义的还有特殊的

### 模型元选项
&ensp;&ensp;&ensp;&ensp;Meta：这个是具有特殊功能的，好像还比较重要，比如排序之类的
- abstract:定义模型是否是抽象基类
- app_label:用于模型定义与默认文件之外时
- db_table：该模型所使用的数据表名称
- db_tablespace：该模型使用的数据库表空间的名字
- default_related_name：这个名字会默认被用于一个关联对象到当前对象的关系，默认为_set(并不知道是啥具体意思）
- get_latest_by：是指定特定字段能够被排序？答案是不是，这个是用于默认字段的，但它有什么用
- managed:是否自动管理创建表
- order_with_respect_to：（有关迁移会有一些问题）按照给定的字段把这个对象标记为“可排序的”。然后模型会提供用于设置和获取关联对象顺序的方法：
    - get_RELATED_order()：获取
    - set_RELATED_order()：设置
- ordering：对象默认的排序，获取一个对象的列表时使用：设置默认排序方式的字段加上不同的前缀具有不同的排序方式（“-”表示倒序，“？”表示随机，没有表示正序），注意排序是需要开销的，请谨慎使用
- permissions：设置创建对象时权限表中额外的权限，但不知详细使用含义
- default_permissions：默认权限，一般为（‘add’，‘change’，‘delete’），定义时删除其中的一些选项便可修改
- proxy：是否设置为代理模型
- select_on_save：是否采用Django1.6以前的save（）算法
- unique_together：用来设置不能重复的字段组合（一维的元组也可以）
- index_together：用来设置带有索引的字段组合（也可以是一维的元组）
- verbose_name：对象的一个易于理解的名称，为单数（小名）
- verbose_name_plural：该对象复数形式的名称

### 模型的字段
#### 字段选项
- null：如果为True，Django 将空值以NULL 存储到数据库中。默认值是 False（这个使用时需要注意一点）
- blank：如果为True，则该字段允许为空白。 默认值是 False。
- choices：它是一个可迭代的结构(比如，列表或是元组)，由可迭代的二元组组成(比如[(A, B), (A, B) ...])，用来给这个字段提供选择项。如果设置了 choices ，默认表格样式就会显示选择框，而不是标准的文本框，而且这个选择框的选项就是 choices 中的元组。（不是很简单易用）
- db_column：数据库中用来表示该字段的名称。如果未指定，那么Django将会使用Field名作为字段名.
- db_index：若值为 True, 则 django-admin sqlindexes 将会为此字段输出 CREATE INDEX 语句。（译注：为此字段创建索引）
- db_tablespace： 如果该字段有索引的话，database tablespace的名称将作为该字段的索引名。 如果DEFAULT_INDEX_TABLESPACE 已经设置，则默认值是由DEFAULT_INDEX_TABLESPACE指定, 如果没有设置则由 db_tablespace 指定，如果后台数据库不支持表空间，或者索引，则该选项被忽略
- default：该字段的默认值. 它可以是一个值或者一个可调用对象. 如果是一个可调用对象，那么在每一次创建新对象的时候，它将会调用一次.
- editable：如果设为False, 这个字段将不会出现在 admin 或者其他 ModelForm. 他们也会跳过 模型验证. 默认是True.
- error_messages：error_messages 参数能够让你重写默认抛出的错误信息。通过指定 key 来确认你要重写的错误信息
- help_text：额外的 ‘help' 文本将被显示在表单控件form中。即便你的字段没有应用到一个form里面，这样的操作对文档化也很有帮助。
- primary_key：若为 True, 则该字段会成为模型的主键字段。主键的这个行为需要特别注意：主键字段是只读的。如果你改变了一个已存在对象上的主键并且保存的话，会创建一个新的对象，而不是覆盖旧的.
- unique：如果为 True, 这个字段在表中必须有唯一值.注意当设置unique 为True 时，你不需要再指定 db_index，因为unique 本身就意味着一个索引的创建。
- unique_for_date：当设置它为DateField 和DateTimeField 字段的名称时，表示要求该字段对于相应的日期字段值是唯一的。
- unique_for_month：类似unique_for_date，只是要求字段对于月份是唯一的。
- unique_for_year：类似unique_for_date 和 unique_for_month。
- verbose_name：一个字段的可读性更高的名称。如果用户没有设定冗余名称字段，Django会自动将该字段属性名中的下划线转换为空格，并用它来创建冗余名称。
- validators：该字段将要运行的一个Validator 的列表

#### 字段类型
- AutoField：自增字段；一个根据实际ID自动增长的IntegerField . 你通常不需要直接使用;如果不指定,一个主键字段将自动添加到你创建的模型中.详细查看 主键字段.
- BigIntegerField：一个64位整数, 类似于一个 IntegerField ,它的值的范围是 -9223372036854775808 到9223372036854775807之间. 这个字段默认的表单组件是一个TextInput.
- BinaryField：这是一个用来存储原始二进制码的Field. 只支持bytes 赋值，注意这个Field只有很有限的功能。例如，不大可能在一个BinaryField 值的数据上进行查询
- BooleanField：true/false 字段；此字段的默认表单挂件是一个CheckboxInput.如果你需要设置null 值，则使用NullBooleanField 来代替BooleanField。如果Field.default没有指定的话， BooleanField 的默认值是 None。
- CharField：一个用来存储从小到很大各种长度的字符串的地方；CharField必须接收一个额外的参数:CharField.max_length字段的最大字符长度.max_length将在数据库层和Django表单验证中起作用, 用来限定字段的长度. 
- CommaSeparatedIntegerField：一个逗号分隔的整数字段。像 CharField一样， 需要一个max_length 参数， 同时数据库移植时也需要注意。
- DateField：这是一个使用Python的datetime.date实例表示的日期. 有几个额外的设置参数:
    - DateField.auto_now：每次保存对象时，自动设置该字段为当前时间。用于"最后一次修改"的时间戳。注意，它总是使用当前日期；和你可以覆盖的那种默认值不一样。
    - DateField.auto_now_add：当对象第一次被创建时自动设置当前时间。用于创建时间的时间戳. 它总是使用当前日期；和你可以覆盖的那种默认值不一样。
- DateTimeField：它是通过Python datetime.datetime实例表示的日期和时间. 携带了跟DateField一样的额外参数.
- DecimalField：用python中 Decimal 的一个实例来表示十进制浮点数. 有两个 必须的参数:
    - DecimalField.max_digits：位数总数，包括小数点后的位数。该值必须大于等于decimal_places.
    - DecimalField.decimal_places：小数点后的数字数量
- DurationField：用作存储一段时间的字段类型 - 类似Python中的timedelta.（数据库不同会有问题）
- EmailField：一个 CharField 用来检查输入的email地址是否合法。它使用 EmailValidator 来验证输入合法性。
- FileField：一个上传文件的字段。有两个可选参数：（有点复杂······）
    - FileField.upload_to：一个本地文件系统的路径，它将附加到MEDIA_ROOT 设置的后面来确定url 属性的值。
    - FileField.storage：一个Storage 对象，用于你的文件的存取。参见管理文件 获取如何提供这个对象的细节。
    - FileField 和FieldFile：当你添加 FileField 到你的模型中时, 你实际上会获得一个 FieldFile的实例来替代将要访问的文件。 除了继承至 django.core.files.File的功能外, 这个类还有其他属性和方法可以用于访问文件:
        - FieldFile.url：通过潜在Storage 类的url()方法可以只读地访问文件的URL。
        - FieldFile.open(mode='rb')[source]：该方法像标准的Python open() 方法,并可通过 mode参数设置打开模式.
        - FieldFile.close()[source]：该方法像标准的Pythonfile.close() 方法,并关闭相关文件.
        - FieldFile.save(name, content, save=True)[source]：这个方法会将文件名以及文件内容传递到字段的storage类中,并将模型字段与保存好的文件关联. 如果想要手动关联文件数据到你的模型中的 FileField实例, 则save() 方法总是用来保存该数据.方法接受两个必选参数: name 文件名, 和 content 文件内容.可选参数save 控制模型实例在关联的文件被修改时是否保存.默认为 True.
        - FieldFile.delete(save=True)[source]：删除与此实例关联的文件，并清除该字段的所有属性。注意︰ 如果它碰巧是开放的调用 delete() 方法 时，此方法将关闭该文件。模型实例save的文件与此字段关联的可选 保存 参数控件已被删除。默认值为 True。注意，model删除的时候，与之关联的文件并不会被删除。如果你要把文件也清理掉，你需要自己处理。
- FilePathField：一个 CharField ，内容只限于文件系统内特定目录下的文件名。有三个参数, 其中第一个是 必需的:
    - FilePathField.path：必填。这个FilePathField 应该得到其选择的目录的绝对文件系统路径。例如: "/home/images".
    - FilePathField.match：可选的.FilePathField 将会作为一个正则表达式来匹配文件名。但请注意正则表达式将将被作用于基本文件名，而不是完整路径。例如: "foo.*.txt$", 将会匹配到一个名叫 foo23.txt 的文件，但不匹配到 bar.txt 或者 foo23.png.
    - FilePathField.recursive：可选的.True 或 False.默认是False.声明是否包含所有子目录的路径
    - FilePathField.allow_files：可选的.True 或 False.默认是True.声明是否包含指定位置的文件。该参数或allow_folders 中必须有一个为 True.
    - FilePathField.allow_folders：是可选的.输入 True 或者 False.默认值为 False.声明是否包含指定位置的文件夹。该参数或 allow_files 中必须有一个为 True.
- FloatField：用Python的一个float 实例来表示一个浮点数.
- ImageField：继承了 FileField的所有属性和方法, 但还对上传的对象进行校验，确保它是个有效的image.ImageField字段需要调用Pillow 库.
- IntegerField：一个整数。在Django所支持的所有数据库中，从 -2147483648 到 2147483647 范围内的值是合法的。默认的表单输入工具是TextInput.
- IPAddressField：IP地址，会自动格式化（例如：“192.0.2.30”）。默认表单控件为 TextInput.
- GenericIPAddressField：一个 IPv4 或 IPv6 地址, 字符串格式 (例如 192.0.2.30 或 2a02:42fe::4). 这个字段的默认表单小部件是一个TextInput.
- NullBooleanField：空的Boolean值
- PositiveIntegerField：正整数字段
- PositiveSmallIntegerField：该模型字段类似 PositiveIntegerField, 但是只允许小于某一特定值（依据数据库类型而定）。从0 到 32767 这个区间，对于Django所支持的所有数据库而言都是安全的。
- SlugField：Slug 是一个新闻术语（通常叫做短标题）。一个slug只能包含字母、数字、下划线或者是连字符，通常用来作为短标签。通常它们是用来放在URL里的。
- SmallIntegerField：与 IntegerField这个字段类型很类似,不同的是SmallInteger类型只能在一个确定的范围内(数据库依赖)。对于django来讲，该字段值在 -32768 至 32767这个范围内对所有可支持的数据库都是安全的。
- TextField：大文本字段。该模型默认的表单组件是Textarea。
- TimeField：时间字段，和Python中 datetime.time 一样。
- URLField:一个CharField 类型的URL
- UUIDField:一个用来存储UUID的字段。使用Python的UUID类。 当使用PostgreSQL数据库时，该字段类型对应的数据库中的数据类型是uuid，使用其他数据库时，数据库对应的是char(32)类型。

#### 关系字段
- ForeignKey：多对一关系。需要一个位置参数：与该模型关联的类。
    - ForeignKey.related_name：这个名称用于让关联的对象反查到源对象。
    - ForeignKey.related_query_name：这个名称用于目标模型的反向过滤。
    - ForeignKey.to_field：关联到的关联对象的字段名称
    - ForeignKey.db_constraint：控制是否在数据库中为这个外键创建约束。默认值为True，而且这几乎一定是你想要的效果；设置成False 对数据的完整性来说是很糟糕的。
    - ForeignKey.on_delete：当一个ForeignKey 引用的对象被删除时，Django 默认模拟SQL 的ON DELETE CASCADE 的约束行为，并且删除包含该ForeignKey的对象。
    - ForeignKey.swappable：控制迁移框架的的重复行为如果该ForeignKey 指向一个可切换的模型。如果它是默认值True，那么如果ForeignKey 指向的模型与settings.AUTH_USER_MODEL 匹配（或其它可切换的模型），则保存在迁移中的关联关系将使用对setting 中引用而不是直接对模型的引用。
    - ForeignKey.allow_unsaved_instance_assignment：Django 阻止未保存的模型实例被分配给一个ForeignKey 字段以防止意味的数据丢失（当保存一个模型实例时，未保存的外键将默默忽略）。
- ManyToManyField：一个多对多关联。要求一个关键字参数：与该模型关联的类，与ForeignKey 的工作方式完全一样，包括递归关系 和惰性关系。
    - ManyToManyField.related_name：与ForeignKey.related_name 相同
    - ManyToManyField.related_query_name：与ForeignKey.related_query_name 相同
    - ManyToManyField.limit_choices_to：与ForeignKey.limit_choices_to 相同
    - ManyToManyField.symmetrical：只用于与自身进行关联的ManyToManyField，用于设置能不能对称关联吧？
    - ManyToManyField.through：Django 会自动创建一个表来管理多对多关系。不过，如果你希望手动指定中介表，可以使用through 选项来指定Django 模型来表示你想要使用的中介表。
    - ManyToManyField.through_fields：只能在指定了自定义中间模型的时候使用。 Django 一般情况会自动决定使用中间模型的哪些字段来建立多对多关联。
    - ManyToManyField.db_table：为存储多对多数据而创建的表的名称。如果没有提供，Django 将基于定义关联关系的模型和字段假设一个默认的名称。
    - ManyToManyField.db_constraint：控制中间表中的外键是否创建约束。默认为True，而且这是几乎就是你想要的；设置为False 对数据完整性将非常糟糕。
    - ManyToManyField.swappable：控制ManyToManyField 指向一个可切换的模型时迁移框架的行为。如果它是默认值True，那么如果ManyToManyField 指向的模型与settings.AUTH_USER_MODEL 匹配（或其它可切换的模型），则保存在迁移中的关联关系将使用对setting 中引用而不是直接对模型的引用。
    - ManyToManyField.allow_unsaved_instance_assignment：与ForeignKey.allow_unsaved_instance_assignment 的工作方式类似。
- OneToOneField：一对一关联关系。概念上讲，这个字段很像是ForeignKey 设置了unique=True，不同的是它会直接返回关系另一边的单个对象。
    - OneToOneField.parent_link：当它为True 并在继承自另一个具体模型 的模型中使用时，表示该字段应该用于反查的父类的链接，而不是在子类化时隐式创建的OneToOneField。

#### Field API参考
##### Field
- description
- get_internal_type()
- db_type(connection)
- get_prep_value(value)
- get_db_prep_value(value, connection, prepared=False)
- get_db_prep_value(value, connection, prepared=False)
- get_db_prep_save(value, connection)
- pre_save(model_instance, add)
- get_prep_lookup(lookup_type, value)
- get_db_prep_lookup(lookup_type, value, connection, prepared=False)
- to_python(value)
- value_to_string(obj)
- formfield(form_class=None, choices_form_class=None, **kwargs)
- deconstruct()

##### Field attribute reference
- Field.auto_created
- Field.concrete
- Field.hidden
- Field.is_relation
- Field.model

##### Attributes for fields with relations
- Field.many_to_many
- Field.many_to_one
- Field.one_to_many
- Field.one_to_one
- Field.related_model


## 模型（表）之间的关系
### 一对一：OneToOneField
&ensp;&ensp;&ensp;&ensp;一对一关系与多对一关系非常相似。如果你在模型中定义一个OneToOneField，该模型的实例将可以通过该模型的一个简单属性访问关联的模型。

### 一对多：ForeignKey
### 前向查询
&ensp;&ensp;&ensp;&ensp;该模型的实例将可以通过属性访问关联的（外部）对象，对外键的修改不会保存到数据库中直至你调用save()

### 反向查询
&ensp;&ensp;&ensp;&ensp;如果模型有一个ForeignKey，那么该ForeignKey 所指的模型实例可以通过一个管理器返回前一个模型的所有实例。默认情况下，这个管理器的名字为foo_set，其中foo 是源模型的小写名称

### 多对多：ManyToManyField
&ensp;&ensp;&ensp;&ensp;多对多关系的两端都会自动获得访问另一端的API。这些API 的工作方式与上面提到的“方向”一对多关系一样。

&ensp;&ensp;&ensp;&ensp;唯一的区别在于属性的名称：定义 ManyToManyField 的模型使用该字段的属性名称，而“反向”模型使用源模型的小写名称加上'_set' （和一对多关系一样）。
```python
e = Entry.objects.get(id=3)
e.authors.all() # Returns all Author objects for this Entry.
e.authors.count()
e.authors.filter(name__contains='John')

a = Author.objects.get(id=5)
a.entry_set.all() # Returns all Entry objects for this Author.
```

## 数据库API的操作
&ensp;&ensp;&ensp;&ensp;pk为主键简称
### 创建对象
- save:保存以创建的对象
- create：创建并保存一个对象

### 保存对象的改动
- 一般基本的改动保存用save
- ForeignKey如同普通字段一样
- ManyToManyField用add方法，可同时传入多个参数

### 对象操作
&ensp;&ensp;&ensp;&ensp;这个好像只能通过模型来获取（既定义的类），完全有数据库中的所以获取操作，可以获取全部、也可以获取部分（加限制条件）
- all：获取全部对象
- get:获取指定条件的对象
- filter：返回一个包含对象的集合，他们满足参数中所给的条件
- exclude：返回一个包含对象的集合，它们不满足参数中所给的条件
- 上述的命令可以组合
- add(obj1, obj2, ...)：添加一指定的模型对象到关联的对象集中
- create(**kwargs)：创建一个新的对象，将它保存并放在关联的对象集中。返回新创建的对象
- remove(obj1, obj2, ...)：从关联的对象集中删除指定的模型对象
- clear()：从关联的对象集中删除所有的对象

### 限制查询集范围
&ensp;&ensp;&ensp;&ensp;使用切片：【范围：范围：这个好像还有，但不太清楚】

### 字段筛选条件（字段的后缀）
- exact：匹配
- iexact：忽略大小写的匹配
- contains：大小写敏感的模糊匹配（注意是模糊匹配）
- startswith：开头匹配
- endswith：结尾匹配
- F：数量匹配（有点迷糊）
- Q:或，非操作查询

### 跨关系匹配
&ensp;&ensp;&ensp;&ensp;当不加isnull后缀时匹配为空不会报错

### 在LIKE语句中转义百分号%和下划线_
&ensp;&ensp;&ensp;&ensp;字段筛选条件相当于LIKE SQL语句（iexact, contains, icontains, startswith, istartswith, endswith, iendswith),它会自动转义为两个特殊符号--百分号%和下划线

### 对象比较
&ensp;&ensp;&ensp;&ensp;比较的是对象的主键（PK）

### 对象删除、更新、拷贝
&ensp;&ensp;&ensp;&ensp;对象的删除是delete，它会自动删除相关联的信息，删除所有对象必须每个对象自己调用删除命令
&ensp;&ensp;&ensp;&ensp;更新是update和删除类似
&ensp;&ensp;&ensp;&ensp;拷贝--修改后进行保存吧，继承之后麻烦一点

