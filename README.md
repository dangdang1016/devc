🚀 Windows DLL 動態載入範例 (Dev-C++)

一個使用 Dev-C++ 環境編寫的簡單 Windows DLL 專案，展示了如何使用 動態連結 (Run-time Dynamic Linking) 的方式，在執行時載入並調用 DLL 中的函式。
🌟 專案概覽

本專案包含三個核心檔案，用於演示 DLL 的創建、匯出、和主程式的載入調用：

    dll.h: DLL 介面定義，使用 __declspec(dllexport) 和 __declspec(dllimport) 處理匯出/匯入。

    dll.c: DLL 實作文件，包含 HelloWorld() 函式和 DllMain 進入點。

    main.c: 主程式應用，使用 LoadLibrary 和 GetProcAddress 動態調用 HelloWorld。

⚙️ 核心程式碼
1. dll.h (DLL 介面定義)

控制函式是匯出 (dllexport) 還是匯入 (dllimport)。
C

#ifndef _DLL_H_
#define _DLL_H_

#if BUILDING_DLL
#define DLLIMPORT __declspec(dllexport)
#else
#define DLLIMPORT __declspec(dllimport)
#endif

// 定義要匯出的函式原型
DLLIMPORT void HelloWorld();

#endif

2. dll.c (DLL 實作與 DllMain)

實現了會彈出 "Hello World" 訊息框的 HelloWorld 函式。
C

#include "dll.h"
#include <windows.h>

// 匯出函式的實作
DLLIMPORT void HelloWorld()
{
    MessageBox(0,"Hello World from DLL!\n","Hi",MB_ICONINFORMATION);
}

// DLL 進入點，處理 DLL 載入/卸載事件
BOOL WINAPI DllMain(HINSTANCE hinstDLL,DWORD fdwReason,LPVOID lpvReserved)
{
    // ... (處理 DLL_PROCESS_ATTACH, DLL_PROCESS_DETACH 等事件)
    return TRUE;
}

3. main.c (主程式動態載入器)

這是主應用程式，它使用 Windows API 函式 動態 載入 Project1.dll。
C

#include <stdio.h>
#include <windows.h> 

// 匹配 DLL 函式的型別
typedef void (*HelloWorld_func)(void);

int main()
{
    HMODULE hDLL;
    HelloWorld_func helloWorld;
    
    // 1. 載入 DLL
    hDLL = LoadLibrary("Project1.dll"); // 假設 DLL 名稱是 Project1.dll

    if (hDLL != NULL)
    {
        // 2. 獲取函式指標
        helloWorld = (HelloWorld_func)GetProcAddress(hDLL, "HelloWorld");

        if (helloWorld != NULL)
        {
            // 3. 呼叫函式 (將彈出 MessageBox)
            helloWorld(); 
        }

        // 4. 釋放 DLL
        FreeLibrary(hDLL);
    }
    else
    {
        printf("Error: Could not load DLL.\n");
    }
    
    // ...
    return 0;
}

🛠️ 編譯與執行
步驟 1: 編譯 DLL

    在 Dev-C++ 中，建立一個新的 Windows DLL 專案。

    將 dll.h 和 dll.c 加入專案。

    編譯專案，這將生成 Project1.dll (或您專案設定的名稱)。

步驟 2: 編譯主程式

    在 Dev-C++ 中，建立一個新的 Console Application 專案。

    將 main.c 加入專案。

    編譯專案，這將生成 main.exe。

步驟 3: 運行

    確保編譯好的 Project1.dll 檔案被複製到 main.exe 所在的目錄下。

    執行 main.exe。

預期結果: 主控台將印出載入成功的訊息，隨後彈出一個標題為 "Hi" 的 Windows 訊息框，最後印出 DLL 卸載成功的訊息。
💡 動態載入機制

主程式 (main.c) 使用以下 API 函式實現了動態連結，這是比靜態連結更靈活的方式：
API 函式	目的
LoadLibrary("...")	載入 DLL 模組到記憶體中。
GetProcAddress(hDLL, "...")	透過函式名稱（字串），在載入的 DLL 中查找並獲取該函式的位址。
FreeLibrary(hDLL)	釋放 DLL 佔用的記憶體和其他資源。
📜 授權

此專案採用 [請填寫您的授權名稱，例如：MIT License]。
