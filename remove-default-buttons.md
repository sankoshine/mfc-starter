---
description: 建立 MFC 应用程序后的第一件事，同时接触消息和虚函数。
---

# 移除默认的确定和取消按钮

## MFC 的默认控件

当建立完成一个 MFC 应用程序后，可以看到窗体中默认有 3 个控件，分别是：

* 内容为「TODO」的文本，Static Text 控件，ID 为 ID\_STATIC
* 显示为「确定」的按钮，Button 控件，ID 为 IDOK
* 显示为「取消」的按钮，Button 控件，ID 为 IDOK

![](.gitbook/assets/image%20%2811%29.png)

同时，运行该程序后，当按下回车键和 ESC 键时，对话框程序都会退出。

## 移除「TODO」文本

这个文本没有任何附加内容，选中后按下键盘上的 Delete 键直接删除即可。

## 移除「确定」按钮

### 打开类向导

类向导用于管理一个类（一个窗口或说一个对话框）中各项方法、变量以及消息、事件等内容，呼出方式为在对话框编辑器中按下 `Ctrl+Shift+X`。

### 创建虚函数的重写

对于类向导中的每个条目，我们几乎都可以使用「双击」的形式为其创建对应的方法或函数。对应虚函数中的内容，当我们双击一个条目时，将自动创建该虚函数的重写。这个虚函数会在左侧窗口中变粗，并在右侧窗口中出现。

![](.gitbook/assets/image%20%2810%29.png)

在搜索栏中输入 `ok` 来搜索 `OnOK` 并双击，随后按「确定」确认操作。

### 重写虚函数的内容

在源文件 `HiSanKoDlg.cpp` 中，自动生成了 `OnOK` 的虚函数重写，内容如下：

```cpp
void CHiSanKoDlg::OnOK()
{
	// TODO: 在此添加专用代码和/或调用基类

	CDialog::OnOK();
}
```

同时在头文件 `HiSanKoDlg.h` 中，自动生成了下述内容：

```cpp
virtual void OnOK();
```

我们将该函数体内唯一一行语句注释掉：

```cpp
void CHiSanKoDlg::OnOK()
{
	// CDialog::OnOK();
}
```

这样就避免我们在按下回车键或「确定」按钮时自动关闭对话框了。

### 删除按钮

最后，选中「确定」按钮并点击键盘上的 Delete 键删除即可。

## 移除「取消」按钮

### 重写 OnCancel 虚函数

在虚函数中为 `OnCancel` 创建重写，并将函数体内唯一一句语句注释掉：

```cpp
void CHiSanKoDlg::OnCancel()
{
	// CDialog::OnCancel();
}
```

此时不要尝试运行，你会发现你无法关闭窗口了，无论是点击 ESC，还是「取消」按钮，甚至是右上角的叉叉。

{% hint style="warning" %}
未完成下述操作前不要尝试运行程序。
{% endhint %}

### 添加 WM\_CLOSE 处理程序

在类向导中选择「消息」选项卡，搜索 `WM_CLOSE` 并双击。

![](.gitbook/assets/image%20%289%29.png)

可以看到源文件中生成了一个与虚函数类似的函数体：

```cpp
void CHiSanKoDlg::OnClose()
{
	// TODO: 在此添加消息处理程序代码和/或调用默认值

	CDialog::OnClose();
}
```

在头文件中则生成了下述内容：

```cpp
afx_msg void OnClose();
```

在函数体中添加 `OnCancel` 中注释掉的内容，使其能够在 `WM_CLOSE` 消息触发时得以执行，保证窗口能够正确关闭。

修改后的函数体如下：

```cpp
void CHiSanKoDlg::OnClose()
{
	CDialog::OnClose();
	CDialog::OnCancel();
}
```

### 另一种方式

事实上，通过打断点调试的方式也可以确认 `OnClose()` 和 `OnCancel()` 之间调用的先后顺序。

或采用下述消息框的提示，可以区分出，当点击右上角叉叉时，会依次调用`OnClose()` 和`OnCancel()`，而当按下 ESC 时，只调用了`OnCancel()`。

```cpp
int isClosedByX = 0;

void CHiSanKoDlg::OnCancel() // ESC
{
	AfxMessageBox("Cancel");
	CDialog::OnCancel();
}


void CHiSanKoDlg::OnClose() // X
{
	AfxMessageBox("Close");
	CDialog::OnClose();
}
```

因此也可以通过一个变量来检查是否来自于右上角叉叉的关闭指令，若不是，不予处理。

```cpp
int isClosedByX = 0;

void CHiSanKoDlg::OnCancel()
{
	if (!isClosedByX)
	{
		return;
	}
	CDialog::OnCancel();
}

void CHiSanKoDlg::OnClose()
{
	isClosedByX = 1;
	CDialog::OnClose();
}
```

### 删除按钮

此时，删除「取消」按钮即可。启动程序，检查 ESC 和回车键都无法关闭程序，并且右上角的叉叉可以正常关闭程序。

## 参考资料：对话框的生命周期

* 微软文档 [Closing the Dialog Box](https://docs.microsoft.com/en-us/cpp/mfc/closing-the-dialog-box) 以及[ Destroying the Dialog Box](https://docs.microsoft.com/en-us/cpp/mfc/destroying-the-dialog-box)
* 网友分享 [MFC对话框退出程序所调用的函数](https://blog.csdn.net/ccfxue/article/details/51698868)





