# os
    os模块是与操作系统交互的一个接口
>- os.getcwd() 获取当前工作目录，即当前python脚本工作的目录路径
>- os.chdir("dirname")  改变当前脚本工作目录；相当于shell下cd
>- os.curdir  返回当前目录: ('.')
>- os.pardir  获取当前目录的父目录字符串名：('..')
>- os.makedirs('dirname1/dirname2')    可生成多层递归目录
>- os.removedirs('dirname1')    若目录为空，则删除，并递归到上一级目录，如若也为空，则删除，依此类推
>- os.mkdir('dirname')    生成单级目录；相当于shell中mkdir dirname
>- os.rmdir('dirname')    删除单级空目录，若目录不为空则无法删除，报错；相当于shell中rmdir dirname
>- os.listdir('dirname')    列出指定目录下的所有文件和子目录，包括隐藏文件，并以列表方式打印
>- os.remove()  删除一个文件
>- os.rename("oldname","newname")  重命名文件/目录
>- os.stat('path/filename')  获取文件/目录信息
>- os.sep    输出操作系统特定的路径分隔符，win下为"\\",Linux下为"/"
>- os.linesep    输出当前平台使用的行终止符，win下为"\t\n",Linux下为"\n"
>- os.pathsep    输出用于分割文件路径的字符串 win下为;,Linux下为:
>- os.name    输出字符串指示当前使用平台。win->'nt'; Linux->'posix'
>- os.system("bash command")  运行shell命令，直接显示
>- os.environ  获取系统环境变量
>- os.path.abspath(path)  返回path规范化的绝对路径
>- os.path.split(path)  将path分割成目录和文件名二元组返回
>- os.path.dirname(path)  返回path的目录。其实就是os.path.split(path)的第一个元素
>- os.path.basename(path)  返回path最后的文件名。如何path以／或\结尾，那么就会返回空值。即os.path.split(path)的第二个元素
>- os.path.exists(path)  如果path存在，返回True；如果path不存在，返回False
>- os.path.isabs(path)  如果path是绝对路径，返回True
>- os.path.isfile(path)  如果path是一个存在的文件，返回True。否则返回False
>- os.path.isdir(path)  如果path是一个存在的目录，则返回True。否则返回False
>- os.path.join(path1[, path2[, ...]])  将多个路径组合后返回，第一个绝对路径之前的参数将被忽略
>- os.path.getatime(path)  返回path所指向的文件或者目录的最后存取时间
>- os.path.getmtime(path)  返回path所指向的文件或者目录的最后修改时间
# sys

>- sys.argv           命令行参数List，第一个元素是程序本身路径
>- sys.exit(n)        退出程序，正常退出时exit(0)
>- sys.version        获取Python解释程序的版本信息
>- sys.maxint         最大的Int值
>- sys.path           返回模块的搜索路径，初始化时使用PYTHONPATH环境变量的值
>- sys.platform       返回操作系统平台名称

# shutil
    shutil模块用来拷贝\移动文件或目录
>- copy()
>>- 功能：复制文件
>>- 格式：shutil.copy('来源文件','目标地址')
>>- 返回值：复制之后的路径

>- copy2()
>>- 功能：复制文件，保留元数据
>>- 格式：shutil.copy2('来源文件','目标地址')
>>- 返回值：复制之后的路径

>- copyfileobj()
>>- 将一个文件的内容拷贝的另外一个文件当中
>>- 格式：shutil.copyfileobj(open(来源文件,'r'),open（'目标文件','w'）)
>>- 返回值：无

>- copyfile()
>>- 功能：将一个文件的内容拷贝的另外一个文件当中
>>- 格式:shutil.copyfile(来源文件,目标文件)
>>- 返回值：目标文件的路径

>- copytree()
>>- 功能：复制整个文件目录
>>- 格式:shutil.copytree(来源目录,目标目录)
>>- 返回值：目标目录的路径
>>- 注意：无论文件夹是否为空，均可以复制，而且会复制文件夹中的所有内容

>- copymode()
>>- 功能：拷贝权限

>- copystat()
>>- 功能：拷贝元数据（状态）

>- rmtree()
>>- 功能：移除整个目录，无论是否空
>>- 格式：shutil.rmtree(目录路径)
>>- 返回值：无

>- move()
>>- 功能：移动文件或者文件夹
>>- 格式：shutil.move(来源地址,目标地址)
>>- 返回值：目标地址

>- which()
>>- 功能：检测命令对应的文件路径
>>- 格式：shutil.which(‘命令字符串’)
>>- 返回值：命令文件所在位置
>>- 注意：window和linux不太一样。 window的命令都是.exe结尾，linux则不是

>- disk_usage()
>>- 功能：检测磁盘使用信息
>>- 格式：disk_usage(‘盘符’)
>>- 返回值：元组

- 归档和解包操作
>- 归档：将多个文件合并到一个文件当中，这种操作方式就是归档。
>- 解包：将归档的文件进行释放。
>- 压缩：压缩时将多个文件进行有损或者无损的合并到一个文件当中。
>- 解压缩：就是压缩的反向操作，将压缩文件中的多个文件，释放出来。
>- 注意：压缩属于归档！

>- make_archive()
>>- 功能：归档函数，归档操作
>>- 格式：shutil.make_archive('目标文件路径','归档文件后缀','需要归档的目录')
>>- 返回值：归档文件的最终路径

>- unpack_archive()
>>- 功能：解包操作
>>- 格式：shutil.unpack_archive('归档文件路径','解包目标文件夹')
>>- 返回值:None
>>- 注意：文件夹不存在会新建文件夹

>- get_archive_formats()
>>- 功能：获取当前系统已注册的归档文件格式（后缀）
>>- 格式：shutil.get_archive_formats()
>>- 返回值：列表   [(后缀,解释),(后缀,解释),(后缀,解释)...]

>- get_unpack_formats()
>>- 功能：获取当前系统已经注册的解包文件格式(后缀)
>>- 格式:shutil.get_unpack_formats()
>>- 返回值：列表   [(后缀,解释),(后缀,解释),(后缀,解释)...]