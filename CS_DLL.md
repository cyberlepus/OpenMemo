C++のDLLを呼び出す
----------------------------------------------------------------

### DLL の関数の確認方法
Visual Studio 付属のコマンドプロンプトを使用する。
`>dumpbin /exports <DLLファイル>`で確認できる
例)
```
>dumpbin /exports C:\Windows\System32\kernel32.dll
Microsoft (R) COFF/PE Dumper Version 14.16.27026.1
Copyright (C) Microsoft Corporation.  All rights reserved.


Dump of file C:\Windows\System32\kernel32.dll

File Type: DLL

  Section contains the following exports for KERNEL32.dll

    00000000 characteristics
    38B369C4 time date stamp
        0.00 version
           1 ordinal base
        1633 number of functions
        1633 number of names

    ordinal hint RVA      name

          1    0          AcquireSRWLockExclusive (forwarded to NTDLL.RtlAcquireSRWLockExclusive)
          2    1          AcquireSRWLockShared (forwarded to NTDLL.RtlAcquireSRWLockShared)
          3    2 00020080 ActivateActCtx
          4    3 0001B700 ActivateActCtxWorker
          5    4 0005A140 AddAtomA
          6    5 000128F0 AddAtomW
          7    6 00025640 AddConsoleAliasA
          8    7 00025650 AddConsoleAliasW
				:
				:
```

### DLL が 32bit or 64bit か確認する方法
Visual Studio 付属のコマンドプロンプトを使用する。
`dumpbin /headers <DLLファイル>`で確認できるが、大量のヘッダー情報が出力されるので、`findstr machine` にて絞り込む

```
dumpbin /headers C:\Windows\System32\kernel32.dll | findstr machine
            8664 machine (x64)
```
