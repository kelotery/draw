为了方便你整理和查阅，我将之前的 MFC 绘图板项目（基于对话框）的所有关键代码和设置步骤打包整理成了这份 Markdown 文档。

MFC 绘图板项目开发文档 (基于对话框)
本篇文档旨在指导如何从零开始创建一个具有左侧颜色区、中间绘图区、右侧工具区布局的 MFC 应用程序。

一、 项目初始化设置
创建项目：在 Visual Studio 中新建 “MFC 应用”。

应用程序类型：务必选择 “基于对话框” (Dialog based)。

资源设计：在资源视图中打开对话框，添加以下控件并修改 ID：

Picture Control (中间): ID 设为 IDC_DRAWING_AREA。

Button (左侧): ID 设为 IDC_BTN_COLOR_RED。

Button (右侧): ID 设为 IDC_BTN_TOOL_PEN。

Button (底部): ID 设为 IDC_BTN_DONE。

二、 关键代码实现
1. resource.h (资源标识符定义)
注意：确保文件末尾的 #endif 逻辑完整，且指令之间有换行。

C++

// resource.h
#define IDD_DRAWINGBOARDDIALOG_DIALOG   102
#define IDR_MAINFRAME                   128

// 自定义控件 ID
#define IDC_DRAWING_AREA        1000  // 中间绘图区域
#define IDC_BTN_COLOR_RED       1001  // 红色按钮
#define IDC_BTN_TOOL_PEN        1002  // 画笔工具
#define IDC_BTN_DONE            1003  // 完成按钮

#ifdef APSTUDIO_INVOKED
#ifndef APSTUDIO_READONLY_SYMBOLS
#define _APS_NEXT_RESOURCE_VALUE        130
#define _APS_NEXT_COMMAND_VALUE         32771
#define _APS_NEXT_CONTROL_VALUE         1004
#define _APS_NEXT_SYMED_VALUE           101
#endif
#endif
2. DrawingBoardDialogDlg.h (头文件)
主要负责声明控件变量和消息处理函数。

C++

#pragma once

class CDrawingBoardDialogDlg : public CDialogEx
{
public:
    CDrawingBoardDialogDlg(CWnd* pParent = nullptr);

#ifdef AFX_DESIGN_TIME
    enum { IDD = IDD_DRAWINGBOARDDIALOG_DIALOG };
#endif

protected:
    virtual void DoDataExchange(CDataExchange* pDX);
    HICON m_hIcon;

    virtual BOOL OnInitDialog();
    afx_msg void OnPaint();
    DECLARE_MESSAGE_MAP()

public:
    // 控件变量
    CStatic m_DrawingArea; 
    CButton m_btnColorRed; 
    CButton m_btnToolPen;
    CButton m_btnDone; 

    // 消息处理函数声明
    afx_msg void OnBnClickedBtnDone();
    afx_msg void OnBnClickedColorRed();
};
3. DrawingBoardDialogDlg.cpp (源文件)
主要负责控件绑定、事件映射及功能实现。

C++

#include "pch.h"
#include "DrawingBoardDialog.h"
#include "DrawingBoardDialogDlg.h"
#include "afxdialogex.h"
#include "resource.h"

// 变量关联
void CDrawingBoardDialogDlg::DoDataExchange(CDataExchange* pDX)
{
    CDialogEx::DoDataExchange(pDX);
    DDX_Control(pDX, IDC_DRAWING_AREA, m_DrawingArea);
    DDX_Control(pDX, IDC_BTN_COLOR_RED, m_btnColorRed);
    DDX_Control(pDX, IDC_BTN_TOOL_PEN, m_btnToolPen);
    DDX_Control(pDX, IDC_BTN_DONE, m_btnDone);
}

// 消息映射
BEGIN_MESSAGE_MAP(CDrawingBoardDialogDlg, CDialogEx)
    ON_WM_PAINT()
    ON_BN_CLICKED(IDC_BTN_DONE, &CDrawingBoardDialogDlg::OnBnClickedBtnDone)
    ON_BN_CLICKED(IDC_BTN_COLOR_RED, &CDrawingBoardDialogDlg::OnBnClickedColorRed)
END_MESSAGE_MAP()

BOOL CDrawingBoardDialogDlg::OnInitDialog()
{
    CDialogEx::OnInitDialog();
    return TRUE;
}

// “完成”按钮实现
void CDrawingBoardDialogDlg::OnBnClickedBtnDone()
{
    AfxMessageBox(_T("完成按钮被点击！"));
}

// “红色”按钮实现
void CDrawingBoardDialogDlg::OnBnClickedColorRed()
{
    AfxMessageBox(_T("已选择红色！"));
}
三、 常见错误排查：Unexpected end of file found (C1004)
如果你在编译 resource.h 时遇到此错误，通常是因为编译器找不到预处理指令的结束标志。

修复方案：

检查换行：确保 resource.h 末尾的 #endif 各占一行，不要挤在一起。

补充缺失：检查文件最末尾是否包含以下两行：

C++

#endif
#endif
编码问题：如果代码看起来没问题，尝试点击 文件 -> 高级保存选项，将编码改为 UTF-8 带签名 或 简体中文 (GB2312)，并确保行尾符号为 Windows (CR LF)。

清理缓存：执行 生成 -> 清理解决方案，然后再重新生成。
