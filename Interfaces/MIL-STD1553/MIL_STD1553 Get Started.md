
Основной драйвер ТМК1553В является `1553.bwdm.sys` (для TA1-USB-драйвер `ezusb.sys` и библиотеки функций `USB_TA_DRV.dll` и `USB_TA_VC2.dll`). Файл драйвера должен находиться в дириктории `$(WINDIR)/system32/drivers`. Установка в соответсвии с файлом `readme.txt`.

**Драйвер поддерживает работу с несколькими физическими устройствами ТМК одновреенно.**  **Количество поддерживаемых устройств - 8**


### Подключение драйвера в прогрмме С/С++

```cpp
#include "WDMTMKv2.cpp"
```


Для подключения **TA1-USB** (одновременно с устройствами ISA/PCI) файл `WDMTMKv2.cpp` должен браться из комплекта драйвера Windows TA1-USB. Дополнитлно для **TA1-USB** необходимо библиотеки функции `USB_TA_DRV.dll` и `USB_TA_VC2.dll` разместить в папке рабочего проекта.

### Компиляция dll для .NET

```c
//WDMTMKv2.h
#ifdef __cplusplus
extern "C" {
#endif

  

#ifdef BUILD_MY_DLL
#define SHARED_LIB __declspec(dllexport)
#else
#define SHARED_LIB __declspec(dllimport)
#endif

SHARED_LIB DWORD TmkOpen();
SHARED_LIB void StrcutInitUnion(TTmkEventData *conf);
SHARED_LIB void TmkClose();

```

```cpp
#include <windows.h>
#include <stdio.h>
#include "wdmtmkv2.h"

  

#pragma warning (disable: 4244)
#pragma warning (disable: 4700)
#pragma warning (disable: 4715)
#pragma warning (disable: 4996)

int tmkError;
HANDLE _ahVTMK4Event[MAX_TMK_NUMBER+1];
HANDLE _hVTMK4VxD[MAX_TMK_NUMBER+1];
int _VTMK4tmkMaxNum = 0;
int _VTMK4mrtMaxNum = 0;
int _VTMK4tmkCurNum = 0;
DWORD TmkOpen()
```
В заголовочном файле определить все прототипы функций как `dllimport`.

скомпилировать библиотеку
```shell
g++ -c -DBUILD_MY_DLL WDMTMKv2.cpp
g++ -shared -o WDMTMKv2.dll WDMTMKv2.o -Wl,--out-implib,WDMTMKv2.a
```



Документация от ЭЛКУС:
[Справчное описание](tmkref.pdf) , [Рукводство программиста](tmkguide.pdf)


