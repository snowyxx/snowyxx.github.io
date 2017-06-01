---
layout: post
title:  "使用python转换html文件为doc"
date:   2014-11-19 16:14:50 +0800
categories: blog
tags:   [python]
---
有用户手册是html格式的要转换为word格式，并且把多个html文件合并到一个doc中。
之前尝试一个使用[其他工具方法](/blog/2013/05/31/htmls-2-doc.html)，缺点是操作麻烦。

用户python也可以，写成一个脚本，操作比较方便。      
原理是：pywin32来通过com来使用office word来转换合并。所以必须在安装了Word的Windows中执行。           

__pywin32安装__：

1. 选择合适的版本程序直接安装 <https://sourceforge.net/projects/pywin32/>
2. 使用pip https://pypi.python.org/pypi/pypiwin32 `pip install pypiwin32`

Example: [source code](https://github.com/snowyxx/pythonHtmlToDoc){:target="_blank"}

以下我使用中用到几个方法：

+ 把html转为doc

        doc = word.Documents.Add(filePath)
        doc.SaveAs(docFile, FileFormat=0)

+ 合并的方法：

        finalDoc.Application.Selection.Range.InsertFile(docFile)
        finalDoc.Application.Selection.Range.InsertBreak(3) #3=word.WdBreakType.wdSectionBreakContinuous
        finalDoc.Application.Selection.EndKey(6,0)  #6=word.WdUnits.wdStory  0=word.WdMovementType.wdMove 

+ 使用页面视图

        finalDoc.ActiveWindow.View.Type=3  #3=Word.WdViewType.wdPrintView

+ 让表格自动适应页面

        i=0
        while(i<len(finalDoc.Tables)):
            try:
                finalDoc.Tables[i].AutoFitBehavior(2) #2=Word.WdAutoFitBehavior.wdAutoFitWindow
                i=i+1

+ 把链接的图片添加到文档中

        i=0
        s=len(doc.InlineShapes)
        while(i<s):
            if doc.InlineShapes[i].Type==4: #4=word.WdInlineShapeType.wdInlineShapeLinkedPicture
                doc.InlineShapes[i].LinkFormat.Update()
                link=doc.InlineShapes[i].LinkFormat.SourceFullName
                print(r' '*16+'going to handle picture: '+str(i)+"/"+str(s)+' -->'+link)
                doc.InlineShapes[i].LinkFormat.SavePictureWithDocument=True
            i=i+1

+ 关闭word时，提示“此文件正由另一个应用程序或用户使用” 提示保存 normal.dot

    word.NormalTemplate.Saved = 1
    
+ 多线程

```python
    import pythoncom, win32com.client, threading, time

    def start():
        # Initialize
        pythoncom.CoInitialize()   # tofix com_error: (-2147221008, 'CoInitialize has not been called.', None, None)

        # Get instance
        xl = win32com.client.Dispatch('Excel.Application')

        # Create id
        xl_id = pythoncom.CoMarshalInterThreadInterfaceInStream(pythoncom.IID_IDispatch, xl)

        # Pass the id to the new thread
        thread = threading.Thread(target=run_in_thread, kwargs={'xl_id': xl_id})
        thread.start()

        # Wait for child to finish
        thread.join()

    def run_in_thread(xl_id):
        # Initialize
        pythoncom.CoInitialize()

        # Get instance from the id
        xl = win32com.client.Dispatch(
                pythoncom.CoGetInterfaceAndReleaseStream(xl_id, pythoncom.IID_IDispatch)
        )
        time.sleep(5)


    if __name__ == '__main__':
        start()
```

参考资料：               
[https://github.com/zhoucc/easyDatasheet/blob/master/win32com.txt](https://github.com/zhoucc/easyDatasheet/blob/master/win32com.txt){:target="_blank"}      
[http://blog.csdn.net/chenjl1031/article/details/8905354](http://blog.csdn.net/chenjl1031/article/details/8905354){:target="_blank"}        
[http://blog.csdn.net/lzl001/article/details/8435048](http://blog.csdn.net/lzl001/article/details/8435048){:target="_blank"}        
[http://msdn.microsoft.com/en-us/library/office/ff837519(v=office.15).aspx](http://msdn.microsoft.com/en-us/library/office/ff837519(v=office.15).aspx){:target="_blank"}        
[http://www.extendoffice.com/documents/word/635-word-remove-all-hyperlinks.html](http://www.extendoffice.com/documents/word/635-word-remove-all-hyperlinks.html){:target="_blank"}      
[http://www.galalaly.me/index.php/2011/09/use-python-to-parse-microsoft-word-documents-using-pywin32-library/](http://www.galalaly.me/index.php/2011/09/use-python-to-parse-microsoft-word-documents-using-pywin32-library/){:target="_blank"}        
[http://www.cnblogs.com/Ss_Andy/archive/2010/09/25/1834386.html](http://www.cnblogs.com/Ss_Andy/archive/2010/09/25/1834386.html){:target="_blank"}         
[https://stackoverflow.com/questions/26764978/using-win32com-with-multithreading](https://stackoverflow.com/questions/26764978/using-win32com-with-multithreading){:target="_blank"}
