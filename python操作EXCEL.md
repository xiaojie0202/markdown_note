# openpyxl
    openpyxl 用来跟EXCEL交互
### 操作EXCEL文件（WorkBook）
    openpyxl.Workbook()
>- 打开EXCEL文件
```
workbook = openpyxl.load_workbook(filename, read_only=False)
# filename: EXCEL表格文件命名
# read_only: True表示只读模式， False表示可编辑
# return ： 返回一个WorkBook对象
```
>- 新建EXCEL文件
```
workbook = openpyxl.Workbook()
```
>- 修改完或者新建后保存EXCEL文件
```
workbook.save(filename)
# filename: 要保存的文件名
```
>- 关闭EXCEL文件
```
workbook.close()
```
### 操作EXCEL工作簿(WorkBook)中的工作表(WorkSheet)
    openpyxl.worksheet.worksheet.Worksheet()
>- 打开工作表
```
worksheet_name_list = workbook.sheetnames # 获取所有工作表的名字返回一个list
worksheet = workbook.active # 返回当前激活的工作表对象
worksheet = workbook['工作表名'] # 打开指定名称的工作表，返回一个工作表对象
worksheet_list = workbook.worksheets  # 返回WorkBook中所有的工作表对象
```
>- 创建工作表
```
worksheet = workbook.create_sheet(title='新建工作表', index=0)  # 新建一个工作表，返回一个工作表对象
# title : 新建工作表的名称
# index ： 新建工作表插入再工作簿（WorkBook）的位置
```
>- 删除工作表
```
workbook.remove(worksheet)
# worksheet : 需要删除的工作表对象
```
>- 工作表属性
```
myworksheet.title # 获取打开的表名字
myworksheet.max_row      #获取工作表的最大行数
myworksheet.max_column  #获取工作表的最大列数
worksheet.parent # 返回当前工作表所属的Workbook
worksheet.merged_cells # 获取合并的单元格
```
>- 工作表数据操作
```
worksheet.values # 返回工作表中每行数据组成的元祖的可迭代对象
worksheet.append(['This is A1', 'This is B1', 'This is C1']) # 追加一行数据
```
### 操作EXCEL工作簿(WorkBook)中的工作表(WorkSheet)中的单元格(Cell)
    openpyxl.cell.cell.Cell()
>- 获取单元格对象(Cell)
```
worksheet.rows  # 返回每行单元格对象组成的元组的可迭代对象
worksheet.columns  # 返回每列单元格对象组成的元组的可迭代对象

cell = worksheet.cell(row, column, value=None)
# row : int类型， 第几行
# column ： int类型 第几列
# value ： 默认为None，代表获取单元格的默认对象， 如果指定value值则代表给单元格设置值

```
>- 单元格对象操作
```
cell.value # 获取单元格对象的值
cell.value = 值 # 设置单元格的值
cell.row # 获取当前单元格所在第几行
cell.column # 获取当前单元格的列对应的字母
```
### 合并查分单元格
```
worksheet.merge_cells(start_row=2,start_column=1,end_row=2,end_column=4)
worksheet.unmerge_cells(start_row=2,start_column=1,end_row=2,end_column=4)
# start_row : 开始行 
# start_column :  开始列
# end_row : 结束行
# end_column : 结束列
```

读取流程

wb = openpyxl.load_workbook(filename=filename）

****************获取sheet***************************
sheet_list= wb.get_sheet_names() # 获取所有的sheet
sheet= wb.get_sheet_by_name('Sheet1') # 根据sheet名字获得sheet
sheet= wb.get_active_sheet()
sheet= wb.active

***************获取最大行，最大列*****************
sheet.max_row   # 获取最大行
sheet.max_column  # 获取最大列

sheet.rows   # 生成器, 里面是每一行的数据，每一行又由一个tuple包裹。 (<Cell 'Sheet1'.A1>, <Cell 'Sheet1'.B1>, <Cell 'Sheet1'.C1>, <Cell 'Sheet1'.D1>, <Cell 'Sheet1'.E1>, <Cell 'Sheet1'.F1>)
sheet.columns # 生成器, 里面是每一列的数据，每一列又由一个tuple包裹。(<Cell 'Sheet1'.A1>, <Cell 'Sheet1'.A2>, <Cell 'Sheet1'.A3>, <Cell 'Sheet1'.A4>, <Cell 'Sheet1'.A5>, <Cell 'Sheet1'.A6>)


sheet.merged_cells # 获取合并的单元格 [<CellRange J26:J28>, <CellRange K26:K28>, <CellRange L26:L28>, <CellRange M26:M28>, <CellRange M23:M25>, <CellRange A26:A28>, <CellRange B26:B28>)






*******************合并拆分单元格******************

#方式1
sheet.merge_cells('A1:B1')
sheet.unmerge_cells('A1:B1')
#方式2







********************获取单元格数据*****************
sheet["A3"]
sheet["A1":"C3"]
sheet["A":"C"]
sheet["1":"5"]
sheet.cell(row=1, column=3)
sheet.cell(row=1, column=3, value=10)

for row in ws.iter_rows(min_row=1, max_col=3, max_row=2):
    for cell in row:
        print cell


首先用
from openpyxl import Workbook
wb = Workbook()
创建一个工作表，然后
sheet = wb.active
找到活动的sheet页。空的excel表默认的sheet页就叫Sheet，如果想改名字，可以直接给title属性赋值。
sheet.title = "New Shit"
这个属性是可读可写的。当然，这个只针对当前活动页，别的页的话，可以用create_sheet和remove_sheet进行添加和删除。
往sheet页里面写内容就比较简单了，跟上面读一样，
sheet['C3'] = 'Hello world!'
for i in range(10):
  sheet["A%d" % (i+1)].value = i + 1
我们还可以进行花式操作，比如写写公式：
sheet["E1"].value = "=SUM(A:A)"
最后记得保存
wb.save('保存一个新的excel.xlsx')