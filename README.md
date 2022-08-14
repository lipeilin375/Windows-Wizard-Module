### C++ 窗体程序学习记录

---

官方入门文档：[Create a traditional Windows Desktop application (C++)](https://docs.microsoft.com/en-us/cpp/windows/walkthrough-creating-windows-desktop-applications-cpp#:~:text=From the main menu%2C choose,Desktop Wizard then choose Next.)

---

#### IDE的选择

1. 窗体程序的开发我会推荐大家使用微软旗下的[Microsoft Visual Studio](https://visualstudio.microsoft.com/)
2. 打开网页后下拉到最下方，选择对应版本就可以直接下载
    - ![C](https://f.pz.al/pzal/2022/08/14/2faeac26253ef.gif)
3. 下载后双击运行，选择如下图的所需配置项（没有python需求的可以不勾选python，对窗体程序开发并没有影响）
    - ![image-20220814103323820](https://f.pz.al/pzal/2022/08/14/86b0ff38862e6.png)
4. 然后点击下载并安装
    - ![image-20220814111759427](https://f.pz.al/pzal/2022/08/14/47f3ae2391c61.png)
5. 接着就可以使用Visual Studio了

---

#### 创建项目

1. 点击`Create a new project`
    - ![image-20220814112211717](https://f.pz.al/pzal/2022/08/14/661f8f3c91109.png)
2. 选择语言、终端、应用层，之后选择`桌面窗口引导程序`
    - ![image-20220814112606805](https://f.pz.al/pzal/2022/08/14/8059401279784.png)
3. 接着点击下一步，填写项目名称以及选择项目保存路径
    - ![image-20220814112925700](https://f.pz.al/pzal/2022/08/14/f0a6d9f1f427d.png)
4. 点击`Create`即可创建成功

---

#### 开发记录

---

##### 程序主入口（`WinMain()`）

1. 首先，窗体程序和C++程序都会有一个==程序入口==，在C++程序中主入口如==*code1-1*==，在窗体程序中主入口如==*code1-2*==
   
    1. ```cpp
        int main()
        {
            // Your Own Code
        }
        ```
    
    2. ```cpp
        int WinMain()
        {
            // Your Own Code
        }
        ```
    
2. 其次，在窗体程序中同样需要引入==头文件==，但与C++程序不同！！==**并且不需要定义命名空间！**==，C++程序头部如==*code2-1*==，窗体程序头部如=='code2-2'==

    1. ```cpp
        #include <iostream>
        using namespace std;
        ```

    2. ```cpp
        #include <windows.h>
        #include <tchar.h> // 有时候需要，有时候可以不用
        ```

        - 关于是否需要使用`<tchar.h>`模块，具体请看[官方文档](https://docs.microsoft.com/en-us/cpp/windows/walkthrough-creating-windows-desktop-applications-cpp?view=msvc-170#:~:text=Windows%20desktop%20programs%20require%20%3Cwindows.h%3E.%20%3Ctchar.h%3E%20defines%20the%20TCHAR%20macro%2C%20which%20resolves%20ultimately%20to%20wchar_t%20if%20the%20UNICODE%20symbol%20is%20defined%20in%20your%20project%2C%20otherwise%20it%20resolves%20to%20char.%20If%20you%20always%20build%20with%20UNICODE%20enabled%2C%20you%20don%27t%20need%20TCHAR%20and%20can%20just%20use%20wchar_t%20directly.)

3. 在窗体程序中==`WinMain()`==也是一种**函数封装**

    - 官方给出的封装原型如下：

    - ```cpp
        int WINAPI WinMain(
           _In_ HINSTANCE hInstance,
           _In_opt_ HINSTANCE hPrevInstance,
           _In_ LPSTR     lpCmdLine,
           _In_ int       nCmdShow
        );
        ```

    - 那么在源文件中可以直接写成如下形式：

    - ```cpp
        int WINAPI WinMain(HINSTANCE hInstance,
                           HINSTANCE hPrevInstance,
                           LPSTR lpCmdLine,
                           int nCmdShow) {
            // 各种配置项写在这里
        }
        ```

4. 对于==`WinMain()`==的配置项如下：

    - 写法：

    - ```cpp
        WNDCLASSEX wcex;
        
        wcex.cbSize         = sizeof(WNDCLASSEX);
        wcex.style          = CS_HREDRAW | CS_VREDRAW;
        wcex.lpfnWndProc    = WndProc;
        wcex.cbClsExtra     = 0;
        wcex.cbWndExtra     = 0;
        wcex.hInstance      = hInstance;
        wcex.hIcon          = LoadIcon(wcex.hInstance, IDI_APPLICATION);
        wcex.hCursor        = LoadCursor(NULL, IDC_ARROW);
        wcex.hbrBackground  = (HBRUSH)(COLOR_WINDOW+1);
        wcex.lpszMenuName   = NULL;
        wcex.lpszClassName  = L"test";
        wcex.hIconSm        = LoadIcon(wcex.hInstance, IDI_APPLICATION);
        ```

    - 其中有些需要特别注意：

    - ```cpp
        wcex.lpfnWndProc    = WndProc;
        ```
        
    - 向wcex.lpfnWndProc赋的值须与LRESULT CALLBACK定义的函数名相同

    - ```cpp
        wcex.cbClsExtra     = 0;
        wcex.cbWndExtra     = 0;
        ```
        
    - 句柄和窗体的额外内存，一般都默认为0

    - ```cpp
        wcex.hIcon          = LoadIcon(wcex.hInstance, IDI_APPLICATION);
        wcex.hIconSm        = LoadIcon(wcex.hInstance, IDI_APPLICATION);
        ```
        
    - 窗口的Icon图标，如果没有，可以将IDI_APPLICATION替换为NULL，同时也可以使用已有的 .ico文件，其他系统图标参见[官方文档](https://docs.microsoft.com/zh-cn/windows/win32/api/winuser/nf-winuser-loadicona?redirectedfrom=MSDN&f1url=%3FappId%3DDev16IDEF1&l=ZH-CN&k=k(WINUSER%252FLoadIcon)%3Bk(LoadIcon)%3Bk(DevLang-C%252B%252B)%3Bk(TargetOS-Windows)&rd=true)

    - ```cpp        
        wcex.hCursor        = LoadCursor(NULL, IDC_ARROW);
        ```
        
    - 当光标进入程序窗口后鼠标的样式可参照[官方文档](https://docs.microsoft.com/zh-cn/windows/win32/api/winuser/nf-winuser-loadcursora?redirectedfrom=MSDN&f1url=%3FappId%3DDev16IDEF1&l=ZH-CN&k=k(WINUSER%252FLoadCursor)%3Bk(LoadCursor)%3Bk(DevLang-C%252B%252B)%3Bk(TargetOS-Windows)&rd=true)

    - ```cpp
        wcex.hbrBackground  = (HBRUSH)(COLOR_WINDOW+1);
        ```

    - 背景样式及颜色也可以更改，参考[官方文档](https://docs.microsoft.com/en-us/windows/win32/api/winuser/ns-winuser-wndclassexw#:~:text=the%20application%27s%20window.-,hbrBackground,fErase%20member%20of%20the%20PAINTSTRUCT%20structure%20filled%20by%20the%20BeginPaint%20function.,-lpszMenuName)

    - ```cpp
        wcex.lpszClassName  = _T("test");
        ```

    - 这个是窗体的类名，格式即为`_T"类名"`，并且此类名会在创建窗体的时候用到

5. 在这之后呢我们还需要进行判断，判断窗体程序是否注册窗体成功

    - 代码如下：

    - ```cpp
        if (!RegisterClassEx(&wcex))
            {
                MessageBox(NULL,
                    _T("Call to RegisterClassEx failed!"),
                    _T("Hello World!"),
                    NULL);
        
                return 1;
            }
        ```

    - **这一段一定要加上，否则窗体无法显示！**

6. 接下来我们还需要把窗体句柄写入全局变量中

    - 代码如下：

    - ```cpp
        hInst = hInstance;
        ```
        
    - 但是一定要在全局变量中定义`hInst`这个变量名！

7. 接下来我们需要创建窗体

    1. 可以使用一个函数对窗体句柄进行创建

        - ```cpp
            HWND hWnd = CreateWindow(_T("test"),
                                     _T("Hello Wold!"),
                                     WS_OVERLAPPEDWINDOW,
                                     CW_USEDEFAULT,
                                     CW_USEDEFAULT,
                                     500,
                                     500,
                                     NULL,
                                     NULL,
                                     hInstance,
                                     NULL);
            ```

        - 具体的功能可以参考[官方文档](https://docs.microsoft.com/en-us/cpp/windows/walkthrough-creating-windows-desktop-applications-cpp?view=msvc-170#:~:text=Now%20you%20can%20create%20a%20window.%20Use%20the%20CreateWindowEx%20function.)

        - 使用==Visual Studio==开发的小伙伴其实可以直接在IDE里面就能够知道每个位置对应什么功能

    2. 窗体的配置已经OK了，但同样还需要进行判断是否创建成功

        - 代码如下：

        - ```cpp
            if (!hWnd)
                {
                    MessageBox(NULL,
                        _T("Call to CreateWindow failed!"),
                        _T("Windows Desktop Guided Tour"),
                        NULL);
            
                    return 1;
                }
            ```

        - 这一段其实可以不写，但为了后期维护方便检查是否为窗体创建失败还是挺有用的

    3. 窗体现在是创建好了，但是需要展示出来，所以就有了以下的代码

        - ```cpp
            ShowWindow(hWnd, nCmdShow);
            UpdateWindow(hWnd);
            ```

        - 这一段就这样写就行，详情移步至[官方文档](https://docs.microsoft.com/en-us/cpp/windows/walkthrough-creating-windows-desktop-applications-cpp?view=msvc-170#:~:text=At%20this%20point%2C%20the%20window%20has%20been%20created%2C%20but%20we%20still%20need%20to%20tell%20Windows%20to%20make%20it%20visible.%20That%27s%20what%20this%20code%20does%3A)

    4. 而后呢，我们需要接受、翻译、执行窗体发送回来的Message

        - 代码如下：

        - ```cpp
            MSG msg;
            while (GetMessage(&msg, NULL, 0, 0))
            {
               TranslateMessage(&msg);
               DispatchMessage(&msg);
            }
            
            return (int) msg.wParam;
            ```

        - 了解更多有关Message的知识，就看看[官方文档](https://docs.microsoft.com/en-us/cpp/windows/walkthrough-creating-windows-desktop-applications-cpp?view=msvc-170#:~:text=For%20more%20information%20about%20the%20structures%20and%20functions%20in%20the%20message%20loop%2C%20see%20MSG%2C%20GetMessage%2C%20TranslateMessage%2C%20and%20DispatchMessage.)过过眼就行

##### 窗体创建过渡（`WinProc()`）

1. 在窗体构建完成之前也会有一个过程，这是毋庸置疑的，官方也对此给出了一个**函数封装**，使用方法与==``WinMain()`==类似，

    - 封装原型：

    - ```cpp
        LRESULT CALLBACK WndProc(
           _In_ HWND   hWnd,
           _In_ UINT   message,
           _In_ WPARAM wParam,
           _In_ LPARAM lParam
        );
        ```

    - 使用写法：

    - ```cpp
        LRESULT CALLBACK WndProc(HWND hWnd,
                                 UINT message,
                                 WPARAM wParam,
                                 LPARAM lParam) {
            // 窗体内容及功能
        }
        ```

2. 接下来我们就来实现一个简单的功能

    - 我们希望在窗体左上角显示`Hello, Windows desktop!`，那么接下来我们就需要在`WinProc`函数中调用`WM_PAINT`指令

    - 并将上面那段话显示出来

    - 代码如下：

    - ```cpp
        PAINTSTRUCT ps;
            HDC hdc;
            TCHAR greeting[] = _T("Hello, Windows desktop!");
        
            switch (message)
            {
            case WM_PAINT:
                hdc = BeginPaint(hWnd, &ps);
        
                // Here your application is laid out.
                // For this introduction, we just print out "Hello, Windows desktop!"
                // in the top left corner.
                TextOut(hdc,
                    5, 100,
                    greeting, _tcslen(greeting));
                // End application-specific layout section.
        
                EndPaint(hWnd, &ps);
                break;
            case WM_DESTROY:
                PostQuitMessage(0);
                break;
            default:
                return DefWindowProc(hWnd, message, wParam, lParam);
                break;
            }
        
            return 0;
        ```

3. `WinProc`函数写好之后我们还得在`WinMain`函数前进行调用

    - 代码如下：

    - ```cpp
        LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);
        ```

##### 代码展示

1. 代码到目前就写完了

    1. 所有代码：
        - ```cpp
            #include <windows.h>
            #include <stdlib.h>
            #include <string.h>
            #include <tchar.h>
            
            static TCHAR szWindowClass[] = _T("test");
            static TCHAR szTitle[] = _T("Hello World!");
            HINSTANCE hInst;
            LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);
            
            int WINAPI WinMain(_In_ HINSTANCE hInstance, _In_opt_ HINSTANCE hPrevInstance, _In_ LPSTR lpCmdLine, _In_ int nCmdShow)
            {
                WNDCLASSEX wcex;
            
                wcex.cbSize = sizeof(WNDCLASSEX);
                wcex.style = CS_HREDRAW | CS_VREDRAW;
                wcex.lpfnWndProc = WndProc;
                wcex.cbClsExtra = 0;
                wcex.cbWndExtra = 0;
                wcex.hInstance = hInstance;
                wcex.hIcon = LoadIcon(wcex.hInstance, IDI_APPLICATION);
                wcex.hCursor = LoadCursor(NULL, IDC_ARROW);
                wcex.hbrBackground = (HBRUSH)(COLOR_WINDOW + 1);
                wcex.lpszMenuName = NULL;
                wcex.lpszClassName = _T("test");
                wcex.hIconSm = LoadIcon(wcex.hInstance, IDI_APPLICATION);
                
                if (!RegisterClassEx(&wcex))
                {
                    MessageBox(NULL,
                        _T("Call to RegisterClassEx failed!"),
                        _T("Windows Desktop Guided Tour"),
                        NULL);
            
                    return 1;
                }
            
                // Store instance handle in our global variable
                hInst = hInstance;
            
                // The parameters to CreateWindowEx explained:
                // WS_EX_OVERLAPPEDWINDOW : An optional extended window style.
                // szWindowClass: the name of the application
                // szTitle: the text that appears in the title bar
                // WS_OVERLAPPEDWINDOW: the type of window to create
                // CW_USEDEFAULT, CW_USEDEFAULT: initial position (x, y)
                // 500, 100: initial size (width, length)
                // NULL: the parent of this window
                // NULL: this application does not have a menu bar
                // hInstance: the first parameter from WinMain
                // NULL: not used in this application
                HWND hWnd = CreateWindowEx(
                    WS_EX_OVERLAPPEDWINDOW,
                    _T("test"),
                    _T("Hello World!"),
                    WS_OVERLAPPEDWINDOW,
                    CW_USEDEFAULT,
                    CW_USEDEFAULT,
                    500, 500,
                    NULL, NULL,
                    hInstance, NULL
                );
                
                if (!hWnd)
                {
                    MessageBox(NULL,
                        _T("Call to CreateWindow failed!"),
                        _T("Windows Desktop Guided Tour"),
                        NULL);
            
                    return 1;
                }
            
                // The parameters to ShowWindow explained:
                // hWnd: the value returned from CreateWindow
                // nCmdShow: the fourth parameter from WinMain
                ShowWindow(hWnd, nCmdShow);
                UpdateWindow(hWnd);
            
                // Main message loop:
                MSG msg;
                while (GetMessage(&msg, NULL, 0, 0))
                {
                    TranslateMessage(&msg);
                    DispatchMessage(&msg);
                }
            
                return (int)msg.wParam;
            }
            
            //  FUNCTION: WndProc(HWND, UINT, WPARAM, LPARAM)
            //
            //  PURPOSE:  Processes messages for the main window.
            //
            //  WM_PAINT    - Paint the main window
            //  WM_DESTROY  - post a quit message and return
            LRESULT CALLBACK WndProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
            {
                PAINTSTRUCT ps;
                HDC hdc;
                TCHAR greeting[] = _T("Hello, Windows desktop!");
            
                switch (message)
                {
                case WM_PAINT:
                    hdc = BeginPaint(hWnd, &ps);
            
                    // Here your application is laid out.
                    // For this introduction, we just print out "Hello, Windows desktop!"
                    // in the top left corner.
                    TextOut(hdc,
                        5, 100,
                        greeting, _tcslen(greeting));
                    // End application-specific layout section.
            
                    EndPaint(hWnd, &ps);
                    break;
                case WM_DESTROY:
                    PostQuitMessage(0);
                    break;
                default:
                    return DefWindowProc(hWnd, message, wParam, lParam);
                    break;
                }
            
                return 0;
            }
            ```
        
    2. 代码截图：
       
       
        - ![image-20220814113503252](https://f.pz.al/pzal/2022/08/14/22f23be857c3c.png)

---

#### 调试记录

---

- 调试方法其实非常简单
    1. 将`cpp`文件进行构建（可使用快捷键`Ctrl+B`）
        - ![A](https://f.pz.al/pzal/2022/08/13/02d4eca856987.gif)
        - ![B](https://f.pz.al/pzal/2022/08/13/4760ed039db8a.gif)
    2. 按`F5`运行即可
        - ![image-20220814113730009](https://f.pz.al/pzal/2022/08/14/b892b4f105b1f.png)