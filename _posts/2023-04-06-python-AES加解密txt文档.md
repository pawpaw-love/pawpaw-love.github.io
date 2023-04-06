---
layout: post
title: python AES加解密txt文档
date: 2023-04-06 16:31:22 +0800
color: blue
author: pawpaw-love
tags: [python, gui, encrypt]
---  

```python
import base64
from Crypto.Cipher import AES
from tkinter import Frame, Label, Button, X, Y, BOTH, HORIZONTAL, TOP, LEFT, RIGHT, CENTER, GROOVE, Entry
from tkinter.tix import CheckList
from tkinter import filedialog
from tkinter import messagebox
from tkinter.ttk import Progressbar
from TkinterDnD2 import *
import sys
from binascii import b2a_hex
from magic import from_buffer
from datetime import datetime
from pathlib import Path
from os.path import isfile


'''
采用AES对称加密算法
'''


# str不是16的倍数那就补足为16的倍数
def add_to_16(value):
    while len(value) % 16 != 0:
        value += '\0'
    return str.encode(value)  # 返回bytes


# 加密方法
def encryptor(keystr: str = None, abs_path: str = None):
    key = keystr
    with open(abs_path, 'r', encoding='utf-8') as f:
        # print(text) 测试打印读取的数据
        # 待加密文本
        mystr = f.read()
    text = base64.b64encode(mystr.encode('utf-8')).decode('ascii')
    # 初始化加密器
    aes = AES.new(add_to_16(key), AES.MODE_ECB)
    # 先进行aes加密
    encrypt_aes = aes.encrypt(add_to_16(text))
    # 用base64转成字符串形式
    encrypted_text = str(base64.encodebytes(encrypt_aes), encoding='utf-8')  # 执行加密并转码返回bytes
    # print(encrypted_text) 测试打印加密数据
    # 写入加密数据到文件
    with open(abs_path + "-encrypted", "w") as f_data:
        f_data.write(encrypted_text)


# 解密方法
def dencryptor(keystr: str = None, abs_path: str = None):
    key = keystr
    with open(abs_path, 'r', encoding='utf-8') as f:
        # print(text) 测试打印读取的加密数据
        # 待解密文本
        text = f.read()
    # 初始化加密器
    aes = AES.new(add_to_16(key), AES.MODE_ECB)
    # 优先逆向解密base64成bytes
    base64_decrypted = base64.decodebytes(text.encode(encoding='utf-8'))
    # bytes解密
    decrypted_text = str(aes.decrypt(base64_decrypted), encoding='utf-8')  # 执行解密密并转码返回str
    decrypted_text = base64.b64decode(decrypted_text.encode('utf-8')).decode('utf-8')
    with open(abs_path + "-decrypted", "w") as f_data:
        f_data.write(decrypted_text)

def get_a_file_type(abs_path):
    """判断文件类型"""
    with open(abs_path, 'rb') as f:
        '''
        magic 模块注意事项：
        实际上，Windows上的大多数跨平台软件都无法处理文件名中的非ASCII字符。
        这是因为C标准库对所有文件名都使用字节字符串，而Windows使用Unicode字符串（从技术上讲，是UTF-16代码单元字符串，但是区别在这里并不重要）。
        当使用C标准库的软件通过基于字节的字符串打开文件时，MS C运行时会使用依赖于Windows区域设置的编码（易名的“ ANSI”代码页）将其自动转换为Unicode字符串。安装。
        您的ANSI代码页可能是1252，它无法编码韩文字符，因此无法使用该文件名。
        不幸的是，ANSI代码页从来没有像UTF-8那样明智，因此它永远不能包含所有可能的Unicode字符。
        Python的特殊之处在于它对Windows Unicode文件名提供了额外的支持，它绕过了C标准库并直接为Unicode文件名调用了底层Win32 API。
        因此，您可以使用eg传递unicode字符串open()，它将适用于所有文件名。
        但是python-magic，的from_file调用不会从Python打开文件。而是将文件名传递给使用libmagic纯C语言编写的库。
        libmagic由于没有用于Unicode的特殊Windows文件名代码路径，因此失败。
        我建议您自己从Python打开文件并magic.from_buffer改为使用。
        '''
        # 官方建议最少读取2048byte少了可能会出错，这里为了提高效率只取了32byte，如果有错还需更改
        file_type = from_buffer(f.read(1024))  # .read方法读取文件只读取开头一部分即可，过多会出现内存不足
        return file_type


def head_checker(abs_path: str = None):
    """获取并输出 16 进制文件头"""
    with open(abs_path, 'r+b') as f:
        print(str(b2a_hex(f.read(4))).strip("b").strip("'"))


def get_res_path(filename):
    """外部资源路径转换"""
    if getattr(sys, 'frozen', False) and hasattr(sys, '_MEIPASS'):
        bundle_dir = Path(sys._MEIPASS)
    else:
        bundle_dir = Path(__file__).parent
    file_path = Path.cwd()/bundle_dir/filename
    return file_path


# ---------------------功能区与 ui 区 分隔----------------------------


class View(object):
    def __init__(self, master):
        self.root = master
        # 内部交换数据
        self.filenum = 0  # 用于按序号生成 CL0.ItemN
        self.select_all_change_flag = False  # 用于记录全选按钮的状态
        self.event_duplicate_flag = True  # 用于消除鼠标按下与弹起造成的重复响应
        self.file_list = []  # 用于防止重复添加文件的文件列表
        self.selected_files = 0  # 用于显示进度条进度的选定文件数量
        self.process_num = 0  # 用于进度条显示当前进度的已完成数量

        # 运行时窗口上部提示区
        self.frame_top = Frame(self.root, borderwidth=0)
        self.frame_top.pack(side=TOP, fill=X)

        self.frame_top_l = Frame(self.frame_top, borderwidth=1)
        self.frame_top_l.pack(side=LEFT, fill=X)
        tip1_text = " 密码 "
        self.label_tip1 = Label(self.frame_top_l, anchor='center', text=tip1_text, justify='center',
                                font=('', 9, 'bold'), fg='black')
        self.label_tip1.grid(column=0, row=0, sticky='nsew')
        self.passwrd = Entry(self.frame_top_l)
        self.passwrd.grid(column=0, row=1, sticky='nsew')

        self.frame_top_r = Frame(self.frame_top, borderwidth=1)
        self.frame_top_r.pack(side=RIGHT, anchor='se', fill=X)
        self.button_top_openfile = Button(self.frame_top_r, text='选择文件', anchor=CENTER, relief=GROOVE,
                                          command=self.sel_files)
        self.button_top_openfile.pack(side=RIGHT, fill=Y)

        # 运行时窗口中间部分 checklist 区
        self.frame_mid = Frame(self.root, borderwidth=0)
        self.frame_mid.pack(side=TOP, expand='yes', fill=BOTH)
        self.check_l = CheckList(self.frame_mid, browsecmd=self.select_all)
        self.check_l.pack(side=TOP, fill=BOTH, expand='yes')
        self.check_l.drop_target_register(DND_FILES)
        self.check_l.dnd_bind('<<Drop>>', self.add_path)

        # 运行时窗口底部加解密按钮区
        self.frame_bottom = Frame(self.root, borderwidth=1)
        self.frame_bottom.pack(side=TOP, fill=X)
        self.decrypt_bottom = Button(self.frame_bottom, text='   解 密   ', relief=GROOVE, command=self.decrypt)
        self.decrypt_bottom.pack(side=LEFT, fill=X)
        self.encrypt_bottom = Button(self.frame_bottom, text='   加 密   ', relief=GROOVE, command=self.encrypt)
        self.encrypt_bottom.pack(side=RIGHT, fill=X)
        self.frame_process = Frame(self.root, borderwidth=1)
        self.frame_process.pack(side=TOP, fill=X)
        self.process = Progressbar(self.frame_process, orient=HORIZONTAL)
        self.process.pack(fill=X)

    def sel_files(self):
        """选择文件添加"""
        filename = filedialog.askopenfilenames()
        self.create_selectall()
        for file in filename:
            if file not in self.file_list:  # 防止重复添加文件
                self.filenum += 1
                CL_name = f"CL0.Item{self.filenum}"
                self.check_l.hlist.add(CL_name, text=file)
                self.check_l.setstatus(CL_name, "off")
                self.file_list.append(file)

    def add_path(self, event):
        """用于拖入文件添加"""
        self.create_selectall()
        files = self.check_l.tk.splitlist(event.data)  # 将拖入复数个文件的路径分割为元组
        for file in files:
            if isfile(file):
                if file not in self.file_list:
                    self.filenum += 1
                    CL_name = f"CL0.Item{self.filenum}"
                    self.check_l.hlist.add(CL_name, text=file)
                    self.check_l.setstatus(CL_name, "off")
                    self.file_list.append(file)

    def create_selectall(self):
        """判断有没有用于全选的根条目，没有则创建一个"""
        try:
            self.check_l.getstatus('CL0')
        except:
            print('没有找到 CL0, 进行创建')
            self.check_l.hlist.add('CL0', text='全选')
            self.check_l.setstatus('CL0', 'off')

    def select_all(self, event):
        """
        鼠标按下和抬起会分别触发一次 event
        if self.event_duplicate_flag:
            ···
        self.event_duplicate_flag = not self.event_duplicate_flag
        以上结构用于忽略一次 event
        """
        if self.event_duplicate_flag:
            if event == 'CL0':
                self.select_all_change_flag = not self.select_all_change_flag
                if self.select_all_change_flag:
                    sel_list = self.check_l.getselection('off')
                    for i in range(len(sel_list)):
                        self.check_l.setstatus(sel_list[i], "on")
                else:
                    sel_list = self.check_l.getselection('on')
                    for i in range(len(sel_list)):
                        self.check_l.setstatus(sel_list[i], "off")
        self.event_duplicate_flag = not self.event_duplicate_flag

    def encrypt(self):
        sel_list = list(self.check_l.getselection("on"))  # 获取选中的 item
        for item in sel_list:
            if item == 'CL0':
                sel_list.remove('CL0')  # 去除最外层用于全选的 CL0
        self.process_num = 0  # 进度条进度清零
        self.selected_files = len(sel_list)
        self.process['maximum'] = self.selected_files
        print('任务数： ', self.selected_files)
        succssed_l = []  # 成功列表
        unsuccss_l = []  # 失败列表
        for i in range(self.selected_files):
            path = self.check_l.hlist.item_cget(sel_list[i], 0, '-text')  # 获取 item 的 text 值，即文件路径
            print(self.passwrd.get())
            print(str(self.passwrd.get()))
            try:
                encryptor(str(self.passwrd.get()), path)
                succssed_l.append(path)
            except Exception as e:
                unsuccss_l.append(path)
            self.process_num += 1
            print('当前任务序号： ', self.process_num)
            self.process['value'] = self.process_num
            self.process.update()
            print(datetime.now())
        if not unsuccss_l:  # 如果失败列表为空的提示
            succssed_message = '\n'.join(succssed_l)
            messagebox.showinfo(title='提示', message=f"操作成功：\n{succssed_message}")
        else:
            unsuccss_message = '\n'.join(unsuccss_l)
            succssed_message = '\n'.join(succssed_l)
            messagebox.showinfo(title='提示', message=f"失败 ->\n{unsuccss_message}\n将跳过以上文件"
                                                    f"\n--------------\n"
                                                    f"以下文件操作成功：\n{succssed_message}")

    def decrypt(self):
        sel_list = list(self.check_l.getselection("on"))
        for item in sel_list:
            if item == 'CL0':
                sel_list.remove('CL0')  # 去除最外层用于全选的 CL0
        self.process_num = 0  # 进度条进度清零
        self.selected_files = len(sel_list)
        self.process['maximum'] = self.selected_files
        print('任务数： ', self.selected_files)
        succssed_l = []  # 成功列表
        unsuccss_l = []  # 失败列表
        for i in range(self.selected_files):
            path = self.check_l.hlist.item_cget(sel_list[i], 0, '-text')  # 获取 item 的 text 值，即文件路径
            try:
                dencryptor(str(self.passwrd), path)
                succssed_l.append(path)
            except Exception as e:
                unsuccss_l.append(path)
            self.process_num += 1
            print('当前任务序号： ', self.process_num)
            self.process['value'] = self.process_num
            self.process.update()
            print(datetime.now())
        if not unsuccss_l:  # 如果失败列表为空的提示
            succssed_message = '\n'.join(succssed_l)
            messagebox.showinfo(title='提示', message=f"操作成功：\n{succssed_message}")
        else:
            unsuccss_message = '\n'.join(unsuccss_l)
            succssed_message = '\n'.join(succssed_l)
            messagebox.showinfo(title='提示', message=f"失败 ->\n{unsuccss_message}\n将跳过以上文件"
                                                    f"\n--------------\n"
                                                    f"以下文件操作成功：\n{succssed_message}")


root = TkinterDnD.TixTk()
# 窗体信息和循环
root.title('txt加密解密小工具 | 疯狂的阳台 fkdyt0201@163.com')
root.wm_attributes('-alpha', 0.9)
root.minsize(width=640, height=400)
ico_file_path = get_res_path('./res/32x32.ico')
root.iconbitmap(ico_file_path)
view = View(root)
root.update()
root.mainloop()

```

```bash
pyinstaller --onefile --noconsole --noupx --clean --paths=D:\software\Python\Python-3.8.2\Lib\site-packages\pywin32_system32 --paths=C:\python27-x64\Lib\lib-tk --hidden-import=Tix --hidden-import=pkg_resources.py2_warn --add-data=D:\software\Python\Python-3.8.2\tcl\tix8.4.3;tix8.4.3 --add-data=D:\software\PyProjects\strencrypt\res;res strencrypt.py
```
