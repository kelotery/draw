# 📘 AI Development Log: MFC Advanced Drawing System
# AI 协作开发日志：MFC 高级绘图系统 v2.0

> **Date:** 2023-12-17  
> **Author:** AI Assistant & Developer (17663)  
> **Tech Stack:** C++ / MFC / Google Test  
> **Project Goal:** Build a feature-rich, object-oriented drawing application with unit testing support.

---

## 📅 Phase 1: Architecture Design (架构设计阶段)

### 1.1 Requirement Analysis (需求分析)
The goal was to create a Windows desktop drawing board supporting basic shapes, freehand drawing, and utility functions like Undo and Save.
**Key Decision:** Use **MFC Dialog-based Application** for simplicity and **GDI (Graphics Device Interface)** for rendering.

### 1.2 System Architecture (系统架构)
To ensure extensibility and support the "Undo" feature, we rejected bitmap manipulation in favor of a **Vector-based approach**.

*   **Model (Data Layer)**: 
    *   Defined `struct MyShape` to store graphic attributes (Type, Start/End Points, Color, Width).
    *   Used `std::vector<MyShape> m_shapes` as the central data repository.
*   **View (Presentation Layer)**:
    *   `OnPaint()` acts as the rendering engine, iterating through `m_shapes` to redraw the canvas.
*   **Controller (Logic Layer)**:
    *   `OnLButtonDown/Up` handles user input and invokes the **ShapeFactory**.

---

## 💻 Phase 2: Implementation Details (核心实现细节)

### 2.1 Feature Implementation (功能实现)
Based on the `Resource.h` analysis, we mapped UI controls to C++ logic:

| Control ID | Functionality | Implementation Details |
| :--- | :--- | :--- |
| `IDC_COMBO2` | Drawing Mode | Line, Rect, Ellipse, Free Draw handled via Enum `Mode`. |
| `IDC_BUTTON6` | Save Image | Captured `CClientDC` to `CImage`, saved with timestamped filename. |
| `IDC_BUTTON8` | Undo | Implemented via `m_shapes.pop_back()` + `Invalidate()`. |
| `IDC_COMBO3` | Multi-language | Dynamic string replacement via `UpdateAllUIStrings()`. |

### 2.2 Logic Decoupling (逻辑解耦)
To support Unit Testing, we extracted pure logic from the UI class `CDrawingSystemDlg` into `DrawingLogic.h`:

*   **`ShapeFactory`**: Encapsulates the complexity of creating `MyShape` objects.
*   **`ShapeSerializer`**: Converts shape data to string format for persistence testing.
*   **`PathGenerator`**: Handles file path logic, isolating file system dependencies.

---

## 🐛 Phase 3: Debugging & Optimization (调试与优化)

### 3.1 The "Dashed Line" Bug (GDI 虚线失效问题)
*   **Symptom**: Selecting "Dash" style resulted in a solid line.
*   **Diagnosis**: The default pen width was set to **5px**. Windows GDI only supports dashed lines (`PS_DASH`) when width is **1px**.
*   **Fix**: Modified `OnPaint` to enforce width constraint.
    ```cpp
    // Code snippet from OnPaint fix
    int displayWidth = s.isDash ? 1 : s.width; 
    CPen pen(s.isDash ? PS_DASH : PS_SOLID, displayWidth, s.lineColor);
    ```

### 3.2 Unit Test Configuration Issues (单元测试环境配置)
*   **Error**: `LNK2019 Unresolved External Symbol` & `Undefined identifier CTime`.
*   **Fix**:
    1.  Changed Test Project Property **"Use of MFC"** to **"Use MFC in a Shared DLL"**.
    2.  Included `<afxwin.h>` in the test logic header.

---

## 🧪 Phase 4: Unit Testing with Google Test (单元测试)

We integrated **Google Test** to verify the stability of the core logic modules.

### 4.1 Test Suites (测试套件)

#### A. ShapeFactory Test
Verifies that graphical objects are created with the correct properties.
*   *Case 1*: **CreateRectangle** - Checks if Type, Coordinates, and Fill status are correct.
*   *Case 2*: **CreateDashLine** - Checks if the `isDash` flag is correctly set.

#### B. Serializer Test
Verifies the data persistence logic.
*   *Case 1*: **SaveSingleShape** - Input a shape, assert the output string format is `Type,X1,Y1,X2,Y2|`.
*   *Case 2*: **LoadIntegration** - Simulate loading a string and assert the restored object matches original data.

### 4.2 Test Results (测试结果)
All tests passed successfully (Green), confirming that the logic layer functions correctly independent of the UI.

---

## 📝 Phase 5: Documentation (文档产出)

### 5.1 Project README
Created a professional `README.md` including:
*   Project Badges (Language, Framework, Status).
*   Feature List (Tools, Styling, Utilities).
*   Build Instructions (Visual Studio setup).
*   Known Limitations (GDI Dash width).

---

## 🔚 Summary (总结)

This project demonstrates a complete software development lifecycle:
1.  **Architecture**: Separated Logic from UI.
2.  **Implementation**: Used modern C++ features (`auto`, `shared_ptr`, `vector`).
3.  **Refactoring**: Solved GDI limitations.
4.  **Testing**: Ensured quality with Google Test.

**End of Log.**
