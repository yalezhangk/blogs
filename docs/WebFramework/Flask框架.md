# 一、框架知识

### 1 程序实例(app)
1.  所有的Flask程序都必须创建一个程序实例(app)

```python
from flask import Flask
app = Flask(__name__)
```

### 2 路由和视图函数、url_for()函数
1. 路由和视图函数形成一个映射关系

```python
# 一个url对应一个视图函数
@app.route('/')
def index():
    return '<h1>Hello World!<h1>'
# 动态路由，由尖括号<>传递
@app.route('/user/<int:user_id>')
def user(user_id):
    return '<h2>Hello, %s'
```

2. **url_for()函数，使视图函数名映射URL链接**
3. url_for()函数映射视图函数名，最简单的的用法是url_for('index'),输出结果为http://127.0.0.1:5000/index,这是没有蓝本的情况下
2. 如果有蓝本

```python
url_for('.generate_uuid', uuid=uuid, _external=True)
输出结果为：
http://127.0.0.1:5000/dingUI/generateUrl?uuid=uuid
```

5. 此url_for会寻找函数generate_uuid()对应的url > generateUrl 并将蓝本dingUI拼接上,
_external=True是将对应的ip及端口拼接上，补全完整的url

### 3 程序和请求上下文
1. Flask使用上下文临时将某些对象变为全局变量可访问，请求上下文request就是
2. 程序上下文：current_app, g
3. **current_app:表示当前运行程序的程序实例**
4. **g:处理请求时，用于临时存储的对象，每次请求都会重设这个变量。** 比如可以获取一些临时请求的用户信息。
3. 请求上下问：request,session
4. Flask在分发请求之前激活（或推送）程序和请求上下文，请求处理完之后再将其删除。程序上下文被推送后，就可以在线程中使用current_app和g变量；请求上下问被推送后，可以使用request和session
5. 例如异步发送邮件时，在另一个线程中发送邮件时，需要用到程序上下文，**因此需要激活程序上下文with current_app.app_context():**

```python
# 1 views.py
# 调用发送邮件函数
from flask import current_app
send_email(name, current_app._get_current_object())

# 2. send_email.py文件
send_email(name, capp):
    with capp.app_context():
        编写发送邮件的代码
        pass
```

### 4 请求钩子
1. 类似于Django中的中间件，有4种
- before_first_request: 处理第一个请求之前
- before_request:每次请求之前运行
- after_request:如果没有异常抛出，每次请求之后运行
- teardown_request:即使有异常抛出，也在每次请求之后运行

### 5 响应
1. Response 响应对象
2. redirect(url)  重定向
3. abort(404)  抛出异常，此异常是交给web服务器处理的
4. jsonify(dict)  返回json格式的数据

# 二、读写Excel

### 一 数据写入Excel中：生成带文件名的Excel
**xlsxwrite是将数据写入(不能修改)Excel的Python模块**
1. pip install xlsxwrite
2. workbook = xlsxwriter.Workbook('test1.xlsx') 生成一个Excel薄
3. sheet = workbook.add_worksheet('sheet1') 生成一个sheet表， 一个Excel薄可以有多个sheet表

```python
# 生成一个Excel薄
workbook = xlsxwriter.Workbook(filename)
# 生成一个sheet表，在sheet里写入数据
# 一个Excel薄可以有多个sheet表
sheet = workbook.add_worksheet()
# 格式(是否居中、加粗等)
merge_format = workbook.add_format({
    'bold': True,
    'align': 'center',
    'valign': 'vcenter',
    'color': '#000000',
})
# 写入数据（行号、列号、数据、格式（可有可无)）
sheet.write(0, 1, 'aa', merge_format)
# 合并单元格
# (初始行、初始列、结束行、结束列、数据、格式（可有可无）)
sheet.merge_range(0, 0, 0, 3, 'bbb', merge_format)
# 写完数据关闭Excel薄
workbook.close()
```

4. **flask 可以发送文件send_file()**

```python
from flask import send_file
# 发送文件
result = send_file(文件名)
# 将文件内容返回，页面里可以下载
return result
```

### 二 不生成Excel文件名，而直接将Excel内容发送过去
1. 默认情况下，**XlsxWrite是将写入的单元格数据保存在内存中的，当指定一个文件**，**打开，写入，并关闭时，将会写入磁盘中**
2. **可以使用BytesIO将文件写入内存中的字符串，这样就不会在项目中生成文件了，直接发送内存中的文件对象**

```python
from io import BytesIO
# 一个字节型内存型文件对象
output = BytesIO()
# 使用in_memory，避免将整个文件保存在内存中
workbook = xlsxwriter.Workbook(output, {'in_memory': True})
sheet = workbook.add_worksheet()
worksheet.write('A1', 'Hello')
# 在数据流之前关闭工作薄
workbook.close()
# 文件指针回到开头
output.seek(0)
# 作为附件发送文件内容
file_content = send_file(output,attachment_filename='文件名',as_attachment=True
return file_content
```

### 三、读取Excel内容：openpyxl
1、 **openpyxl模块是用来处理Excel的，既可以读，又可写入**。以下代码示例，是获取前端上传的文件，读取Excel中的内容，存入库中

```python
from openpyxl import load_workbook
# 导入Excel数据
@blue_pro.route('/importExcel', methods=['POST'])
def importExcel():
    if request.files.has_key('file'):
        file = request.files['file']
        allow_file = '.' in file.filename and file.filename.rsplit('.', 1)[1] in ['xlsx']
        if file and allow_file:
            # 解析文件内容
            workbook = load_workbook(file)
            sheet = workbook.active
            max_row = sheet.max_row
            # 验证合同号是否重复
            contId_list = db.session.query(ProjectUnique.contractId).all()
            clo_id_list = []
            for col in sheet.iter_cols(min_row=2, max_row=max_row, min_col=4, max_col=4):
                for cell in col:
                    clo_id_list.append((cell.value,))
            # 求交集,已存在的合同号
            exist_list = list(set(clo_id_list).intersection(set(contId_list)))
            if exist_list:
                # 合同号存在，不处理
                existIds_list = map(lambda x: x[0], exist_list)
                return resp_json(402, u'导入失败，合同号已存在', existIds_list, None, 1)
            else:
                # 处理数据
                for rows in range(max_row - 1):
                    row_info_list = []
                    for row in list(sheet.rows)[1 + rows]:
                        row_info_list.append(row.value)
                    del row_info_list[0]
                    # 日期处理
                    if row_info_list[3]:
                        try:
                            row_info_list[3] = datetime.strptime(row_info_list[3], '%Y-%m-%d').date()
                        except:
                            return resp_json(402, u'日期格式不符', None, None, 1)
                    if row_info_list[5]:
                        try:
                            row_info_list[5] = row_info_list[5] * 10000
                        except:
                            return resp_json(402, u'预算金额不符', None, None, 1)
                    if row_info_list[6]:
                        try:
                            row_info_list[6] = row_info_list[6] * 10000
                        except:
                            return resp_json(402, u'采购金额不符', None, None, 1)
                    # 项目信息表添加
                    pinfo_obj = ProjectInfo()
                    pinfo_obj.read_excel(row_info_list)
                    db.session.add(pinfo_obj)
                    try:
                        db.session.commit()
                    except:
                        db.session.rollback()
                        return resp_json(501, u'信息处理失败', None, None, 1)
                    # 2.项目唯一表
                    punique_obj = ProjectUnique(contractId=row_info_list[2], proinfoId=pinfo_obj.id)
                    db.session.add(punique_obj)
                    try:
                        db.session.commit()
                    except:
                        db.session.rollback()
                        return resp_json(501, u'信息添加失败', None, None, 1)
                return resp_json(200, u'数据处理成功，已入库', None, None, 0)
        else:
            return resp_json(401, u'上传失败,文件名后缀不符', None, None, 1)
    else:
        return resp_json(400, u'上传失败', None, None, 1)
```
