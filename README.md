🚀 devc-simple-msgbox-dll-example

一個使用 Dev-C++ 創建的簡單 Windows DLL 及其動態載入（Run-time Dynamic Linking）的完整教學範例。
✨ 專案簡介 (Project Overview)

這個專案提供了一個功能極簡的 DLL，其唯一作用是彈出一個 "Hello World" 的訊息框（MessageBox）。它作為一個基礎模板，展示了 Windows DLL 開發中的三個核心組件：

    DLL 介面 (dll.h): 定義了匯入和匯出函式的巨集。

    DLL 實作 (dll.c): 包含實際的函式邏輯（HelloWorld）和 DLL 的生命週期管理（DllMain）。

    主程式 (main.c): 示範如何使用 Windows API 中的 LoadLibrary、GetProcAddress 和 FreeLibrary 函式，動態載入並呼叫 DLL 裡的功能。

⚙️ 程式碼與結構 (Code Structure)
1. 介面定義：dll.h

定義了 DLLIMPORT 巨集，用來處理編譯 DLL 本身時的 dllexport 和編譯主程式時的 dllimport。
C

#ifndef _DLL_H_
#define _DLL_H_

#if BUILDING_DLL
#define DLLIMPORT __declspec(dllexport) // 編譯 DLL 時：匯出
#else
#define DLLIMPORT __declspec(dllimport) // 使用 DLL 時：匯入
#endif

// 定義要匯出的函式原型
DLLIMPORT void HelloWorld();

#endif

2. DLL 實作：dll.c

包含 HelloWorld 函式實作（彈出 MessageBox）和 DLL 必要的進入點 DllMain。
C

/* Replace "dll.h" with the name of your header */
#include "dll.h"
#include <windows.h>

// 匯出函式的實作：彈出一個訊息框
DLLIMPORT void HelloWorld()
{
    MessageBox(0,"Hello World from DLL!\n","Hi",MB_ICONINFORMATION);
}

// DLL 進入點：處理 DLL 載入、卸載等系統事件
BOOL WINAPI DllMain(HINSTANCE hinstDLL,DWORD fdwReason,LPVOID lpvReserved)
{
    switch(fdwReason)
    {
        case DLL_PROCESS_ATTACH:
        case DLL_PROCESS_DETACH:
        case DLL_THREAD_ATTACH:
        case DLL_THREAD_DETACH:
            break;
    }
    
    /* Return TRUE on success, FALSE on failure */
    return TRUE;
}

3. 主程式：main.c

展示了如何使用 Windows API 進行動態載入：
C

#include <stdio.h>
#include <windows.h> 

/* 定義 DLL 函式的型別，必須與 DLL 中的簽名匹配 */
typedef void (*HelloWorld_func)(void);

int main()
{
    HMODULE hDLL;
    HelloWorld_func helloWorld;
    
    /* 1. 載入 DLL (DLL 檔名為 "Project1.dll") */
    hDLL = LoadLibrary("Project1.dll"); 

    if (hDLL != NULL)
    {
        printf("DLL loaded successfully.\n");

        /* 2. 獲取函式指標 (必須使用 DLL 中匯出的名稱 "HelloWorld") */
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
        // 建議使用 GetLastError() 來獲取更詳細的錯誤資訊
    }

    printf("Press Enter to exit.\n");
    getchar();
    return 0;
}

🛠️ 編譯與運行步驟 (Compilation & Running)
前置條件

    Dev-C++ 或任何支援 MinGW/GCC 的 Windows 開發環境。

步驟

    編譯 DLL:

        在 Dev-C++ 中，創建一個 DLL 專案。

        將 dll.c 和 dll.h 加入專案中，並編譯產生 Project1.dll (此名稱假設來自 Dev-C++ 預設專案設定)。

    編譯主程式:

        在 Dev-C++ 中，創建一個 Console Application 專案。

        將 main.c 加入專案中，並編譯產生 main.exe。

    運行準備:

        將編譯好的 Project1.dll 檔案複製到 main.exe 所在的目錄下。

    執行:

        運行 main.exe。您將會在控制台看到載入成功訊息，然後彈出一個訊息框，最後控制台顯示 DLL 卸載訊息。

💡 核心概念對照
函式/巨集	程式碼中的位置	核心用途
__declspec(dllexport)	dll.h (藉由 BUILDING_DLL 定義)	告訴編譯器將 HelloWorld 函式公開在 DLL 外部。
LoadLibrary("Project1.dll")	main.c	執行時載入 DLL 檔案到記憶體。
GetProcAddress(hDLL, "HelloWorld")	main.c	通過函式名稱（字串），獲取 DLL 中該函式的執行位址。
helloWorld()	main.c	透過函式指標間接地呼叫 DLL 中的程式碼。
FreeLibrary(hDLL)	main.c	釋放 DLL 佔用的記憶體和其他系統資源。
🤝 貢獻 (Contributing)

歡迎對此專案提出意見或改進！如果您發現任何錯誤或有任何建議，請隨時提交一個 Issue 或發送 Pull Request。
📄 授權 (License)

此專案採用 [請填寫您的授權名稱，例如：MIT License]。詳情請參閱 LICENSE 檔案。
