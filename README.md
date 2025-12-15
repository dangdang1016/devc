📚 devc-windows-dll-example

這個專案提供了一個使用 Dev-C++ (MinGW/GCC) 編譯的簡單 Windows DLL 及其在主程式中動態載入與調用的完整範例。
✨ 專案簡介 (Project Overview)

本專案旨在展示 Windows DLL 開發的兩個核心環節：

    DLL 建立與匯出 (Export)：如何使用 __declspec(dllexport) 將 HelloWorld() 函式封裝在 Project1.dll 中。

    主程式動態載入 (Dynamic Loading)：主程式如何使用 Windows API 函式 (LoadLibrary、GetProcAddress、FreeLibrary) 在執行時調用 DLL 內的功能。

⚙️ 檔案與程式碼 (Files and Code)
1. 介面定義：dll.h

定義了函式的匯入/匯出機制，確保在編譯 DLL 本身時使用 dllexport，而在編譯主程式時使用 dllimport。
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

2. DLL 實作：dll.c

包含 DLL 的主要功能 (HelloWorld)，它會彈出一個 Windows 訊息框 (MessageBox)。
C

#include "dll.h"
#include <windows.h>

DLLIMPORT void HelloWorld()
{
    // 呼叫 Windows API 彈出訊息框
    MessageBox(0,"Hello World from DLL!\n","Hi",MB_ICONINFORMATION);
}

// DllMain 是 DLL 的進入點，處理載入/卸載事件
BOOL WINAPI DllMain(HINSTANCE hinstDLL,DWORD fdwReason,LPVOID lpvReserved)
{
    // ... 處理各種 DLL 事件（省略細節）
    return TRUE;
}

3. 主程式：main.c

展示了主程式如何動態載入 Project1.dll 並透過指標調用 HelloWorld 函式。
C

#include <stdio.h>
#include <windows.h> 

/* 定義 DLL 函式的型別，必須與 DLL 中的簽名匹配 */
typedef void (*HelloWorld_func)(void);

int main()
{
    HMODULE hDLL;
    HelloWorld_func helloWorld;
    
    /* 1. 載入 DLL */
    hDLL = LoadLibrary("Project1.dll"); 

    if (hDLL != NULL)
    {
        printf("DLL loaded successfully.\n");

        /* 2. 獲取函式指標 */
        helloWorld = (HelloWorld_func)GetProcAddress(hDLL, "HelloWorld");

        if (helloWorld != NULL)
        {
            printf("HelloWorld function found. Calling it...\n");
            
            /* 3. 呼叫函式 */
            helloWorld(); 
        }
        else
        {
            printf("Error: Could not find HelloWorld function.\n");
        }

        /* 4. 釋放 DLL */
        FreeLibrary(hDLL);
        printf("DLL unloaded.\n");
    }
    else
    {
        printf("Error: Could not load DLL (Project1.dll).\n");
    }

    printf("Press Enter to exit.\n");
    getchar();
    return 0;
}

🛠️ 編譯與運行步驟 (How to Build & Run)
預備工作

    環境: Dev-C++ (MinGW/GCC)

    您需要將 dll.c 和 dll.h 編譯為一個 DLL 專案，然後將 main.c 編譯為一個 Console Application 專案。

步驟 1: 編譯 DLL

    在 Dev-C++ 中建立一個新的 Windows DLL 專案。

    將 dll.c 和 dll.h 加入專案。

    編譯專案，這將生成 Project1.dll (或其他您指定的名稱)。

步驟 2: 編譯主程式

    建立一個新的 Console Application 專案。

    將 main.c 加入專案。

    編譯主程式，生成 main.exe。

步驟 3: 運行測試

    將 Project1.dll 檔案複製到 main.exe 所在的目錄下（這是動態載入成功的關鍵步驟）。

    運行 main.exe。

    預期結果：

        主控台輸出 "DLL loaded successfully."

        彈出一個標題為 "Hi"、內容為 "Hello World from DLL!" 的訊息框。

        關閉訊息框後，主控台輸出 "DLL unloaded."

🤝 核心 API 說明 (Key API Functions)
函式	說明
LoadLibrary	載入指定的 DLL，並將其映射到調用行程的記憶體空間。
GetProcAddress	通過函式名稱字串，獲取已載入 DLL 中函式的起始記憶體位址。
FreeLibrary	卸載 DLL 模組，釋放相關資源。
MessageBox	彈出一個訊息框供使用者互動。
📄 授權 (License)

此專案採用 [請在這裡填寫您的授權名稱，例如：MIT License]。
