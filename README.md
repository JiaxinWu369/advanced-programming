# advanced-programming
advanced programming
# MDI记事本项目 - AI交流日志
记录与AI的交流过程、问题解决、代码迭代等内容，用于项目复盘和知识沉淀。

## 基本信息
- 项目名称：MDINotePad（MFC多文档记事本）
- 开始时间：2025-12-13
- 技术栈：MFC、C++、Windows SDK

## 日志正文（按时间倒序/正序记录）

###2025-12-12 主题：项目架构搭建
####问题描述（需求）
需要基于 MFC 创建 MDI 多文档记事本，替换默认CEditView基类为CScrollView，实现自定义文本存储、绘制与基础交互。
####解决方案
1. 定义文档类CMDINotePadDoc，添加m_strText成员变量存储文本内容；
2. 定义视图类CMDINotePadView（继承CScrollView），重写OnDraw实现文本绘制；
3. 通过GetDocument()关联文档与视图，实现数据传递。
####代码
```cpp
// 文档类：存储文本
class CMDINotePadDoc : public CDocument {
public:
    CString m_strText
;
    void SetText(const CString& str) { m_strText = str; }
    CString 
GetText() const { return m_strText; }
};

// 视图类：绘制文本
void CMDINotePadView::OnDraw(CDC* pDC) {
    CMDINotePadDoc
* pDoc = GetDocument();
    CString text 
= pDoc->GetText().IsEmpty() ? "请输入文本" : pDoc->GetText();
    pDC
->TextOut(50, 50, text);
}
```
2025-12-13 问题：编译报错（成员函数未声明）
问题描述
编译触发多个 C2509 错误：OnFilePrintPreview、Dump、OnRButtonUp等成员函数未在CMDINotePadView/CMDINotePadDoc中声明。
解决方案
在MDINotePadView.h中补充打印、右键菜单相关函数声明；
删除CMDINotePadDoc.cpp中未声明的Dump函数实现；
确保函数签名与 MFC 基类（CScrollView/CDocument）一致。
```cpp
// 视图类头文件补充声明
protected:
    afx_msg void OnFilePrintPreview();
    afx_msg void OnRButtonUp(UINT nFlags, CPoint point);
```

### 2025-12-14 问题：无法输入中文 & 调整字体大小
#### 问题描述
1. 多字节字符集下，程序能输入英文但无法输入中文；
2. 需要通过界面选项调整字体大小，匹配记事本的字体设置功能。
#### 解决方案
1. 中文输入：添加IME（输入法编辑器）相关消息处理（OnImeChar、OnImeSetContext等），修复消息签名不匹配问题；
2. 字体大小：
   - 快速修改：调整`CreatePointFont`的参数（数值=字体大小×10）；
   - 动态修改：添加成员变量`m_nFontSize`，通过右键菜单/对话框修改后刷新视图。
#### 关键代码片段
```cpp
// 调整字体大小的核心代码
font.CreatePointFont(m_nFontSize * 10, "微软雅黑"); // m_nFontSize为字体大小（如12、20）
```


