---
title: Linux File And Directory (4)
tags: Linux
katex: true
abbrlink: e830469d
date: 2023-03-22 13:49:08
---
## Introduction

- FileSystem 特徵
- file 屬性
- stat() 函數
- stat structure
- 改變owner, permission 函數
- file system 結構 和 symbolic link
- Descending directory hierarchy

## stat

取得path所指檔案信息

```c
stat(pathname, struct stat *struct_stat);
fstat(fd, struct stat *struct_stat);
lstat(pathname, struct stat *struct_stat);
fstatat(fd, pathname,struct stat *struct_stat, flag;
```

成功返回0, 失敗返回-1並設置errno

- stat() : returns a information structure about a given file
- fstat() : returns information about a file that is already open
- lstat() : returns information about a symbolic link (not the file referenced by the link)
- fstatat() : same as stat(), except relative to an open directory (fd). The flag argument controls whether links are followed. (AT_SYMLINK_NOFOLLOW flag)

struct stat{}

```c
struct stat{
  mode_t st_mode;
  ino_t st_ino; //
  dev_t st_dev; //
  dev_t st_rdev;
  nlink_t st_nlink;
  uid_t st_uid;
  struct timespec st_atim; //last access file
  struct timespec st_mtim; //last modificate file
  struct timespec st_ctim; //last file status change
}
```

st_mtim：表示文件或目錄的最後一次修改時間。當文件或目錄的內容被修改時，st_mtim 會被更新。

st_ctim：表示文件或目錄的最後一次更改時間。當文件或目錄的屬性（如權限、所有者等）或元數據（如擁有的子目錄數量等）發生更改時，st_ctim 會被更新。

## File types

1. Regular File
2. Direcyory File
3. Character special File : unbuffered I/O in variable-sized units, used for certain types of devices, such as terminal
4. Block special File : buffered I/O access in fixed-size units, used for disk drives
5. FIFO
6. Socket
7. Symbolic link

## Macro detect file type

1. ``S_ISDEG()``
2. ``S_ISDIR()``
3. ``S_ISCHR()``
4. ``S_ISBLK()``
5. ``S_ISFIFO()``
6. ``S_ISSOCK()``
7. ``S_ISLNK()``

---

Argument: stat
represent IPC objects as files
**No OS from the book does that now**

- POSIX.1 標準允許實現將 IPC (Inter-Process Communication) 對象表示為文件，例如信號量、消息隊列等。這意味著在某些操作系統中，IPC 對象可能會被當作普通的文件對待，並可以使用類似於 stat() 函數來獲取其屬性信息。

其中，S_TYPEISMQ()、S_TYPEISSEM() 和 S_TYPEISSHM() 是用於檢查 stat 結構中 st_mode 成員的宏，用於判斷文件是否為消息隊列、信號量或共享內存對象。這些宏可以用來識別文件是否是某種特定的 IPC 對象，進而針對不同的 IPC 對象進行相應的處理。

需要注意的是，根據書中的描述，目前的操作系統並不一定會將 IPC 對象表示為文件，因此使用這些宏進行文件類型的檢查時需要謹慎，應確保在特定的操作系統和文件系統中進行測試和驗證。

## Set-User-ID & Set-GROUP-ID

Each process has 6 or more IDs:

real user id
real group id
effective user id
effective group id
Supplementary group IDs
saved set-user-id
saved set-group-id

Real user ID: 是與進程關聯的實際用戶的識別符。通常是啟動進程的使用者的 UID。

Real group ID: 是與進程關聯的實際組的識別符。通常是啟動進程的使用者所屬的組的 GID。

Effective user ID: 是進程在運行時使用的有效用戶的識別符。可以通過設置特殊的權限位（setuid）來更改進程的有效用戶，使進程能夠以該用戶的權限運行。

Effective group ID: 是進程在運行時使用的有效組的識別符。可以通過設置特殊的權限位（setgid）來更改進程的有效組，使進程能夠以該組的權限運行。

Supplementary group IDs: 是與進程關聯的附加組的識別符的集合。進程可以屬於多個組，這些組的識別符可以通過設置進程的附加組來指定。

Saved set-user-ID: 是在進程運行時保存的先前的有效用戶ID，以便在後續操作中恢復。

Saved set-group-ID: 是在進程運行時保存的先前的有效組ID，以便在後續操作中恢復。

Set-User-ID
當一個文件的 SUID 標誌被設置時，它表示當這個文件被執行時，執行該文件的進程將會以該文件的擁有者 (owner) 的有效用戶 ID (UID) 來運行，而不是執行該文件的進程的真實用戶 ID (RUID)。換句話說，這個文件在執行時會暫時修改進程的有效用戶 ID，使其變成文件的擁有者，而不是執行者本身

Saved set-user-ID 和 Saved set-group-ID 是在使用 exec() 函數族（例如 execve()、execvp() 等）執行一個新的程序時保存的先前的有效用戶ID和有效組ID。

當一個進程使用 exec() 函數族來執行一個新的程序時，通常會將進程的有效用戶ID和有效組ID重置為新程序的用戶ID和組ID，以確保新程序以其自己的權限運行。然而，為了確保進程在後續操作中可以恢復到先前的狀態，exec() 函數族會保存進程的先前的有效用戶ID和有效組ID到 Saved set-user-ID 和 Saved set-group-ID 中。

這樣，當新程序執行完畢並終止時，進程可以通過恢復 Saved set-user-ID 和 Saved set-group-ID 的值來恢復先前的有效用戶ID和有效組ID，以保持在執行新程序期間的權限和身份狀態不受影響。這種機制通常用於實現特權程序（例如 SetUID 程序）的權限管理和身份切換。

st_mode 中一個 flag

1. Set-user-ID : when file execute, set the effective user ID of the process to be the owner of the file (st_uid)

Set-user-ID 是一個特殊的文件屬性（位於 st_mode 的標誌），當這個文件被執行時，會將進程的有效用戶ID（effective user ID）設置為該文件的擁有者的用戶ID（st_uid）。

Set-user-ID 是一種在執行程序時，**臨時改變進程的有效用戶ID的機制**。這意味著即使當前執行這個程序的用戶的真實用戶ID（real user ID）是不同的，當執行帶有 Set-user-ID 屬性的文件時，進程的有效用戶ID會暫時變為文件的擁有者的用戶ID。

這種機制通常用於實現特權程序，**允許普通用戶在執行該程序時臨時擁有某些權限**，例如執行需要特定權限的系統操作，**而不必永久更改用戶的真實用戶ID**。由於這種機制可能對系統安全性產生影響，因此在使用 Set-user-ID 機制時需要謹慎並遵從相應的安全最佳實踐。

## File Access Permission

rwx------

- User
- Group
- Other

## Ownership of New Files and Directories

## access and faccessat

``` shell
$ ./a.out /etc/shadow
access error for /etc/shadow: Permission denied
open error for /etc/shadow: Permission denied
$ su
Password:
# chown root a.out
# chmod u+s a.out
# ls –l a.out
-rwsrwxr-x   1 root 15945 Nov 30 12:10  a.out
# exit
$ ./a.out /etc/shadow
access error for /etc/shadow: Permission denied
open for reading OK
```

使用者首先運行了一個名為 "a.out" 的執行檔，並且試圖以讀取的方式訪問 "/etc/shadow" 文件，但因權限不足而出現 "Permission denied" 錯誤。

使用者切換到超級用戶（root）帳戶（su），並使用 "chown" 命令將 "a.out" 文件的擁有者更改為 "root" 用戶。

使用 "chmod" 命令將 "a.out" 文件的 set-user-ID（suid）位設置為可執行（"u+s"），這會使該執行檔在執行時以 "root" 用戶的權限運行。

使用 "ls -l" 命令查看 "a.out" 文件的詳細屬性，確認 set-user-ID 位已經設置。

超級用戶退出（exit）。

使用者再次運行 "a.out" 執行檔，並試圖訪問 "/etc/shadow" 文件。這次由於 "a.out" 文件具有 set-user-ID 位，因此在執行時會以 "root" 用戶的權限運行，因此可以成功讀取 "/etc/shadow" 文件並顯示 "open for reading OK" 的訊息，而不再出現 "Permission denied" 的錯誤。

---

```bash
chmod [選項] 權限 文件或目錄
```

其中，"選項" 可以是以下之一：

-c：只顯示更改過的文件或目錄的信息。
-f：不顯示錯誤信息。
-v：顯示詳細的操作信息。
"權限" 是用於設置的權限值，可以使用數字或符號兩種方式指定：

使用數字方式指定權限值：

0：無權限
1：執行權限
2：寫入權限
4：讀取權限

使用符號方式指定權限值：

r：讀取權限
w：寫入權限
x：執行權限
s：set-user-ID (suid) 或 set-group-ID (sgid) 權限
t：sticky 權限

## umask

umask 是在 “666” 基礎上 “減少” 檔案權限; 及在 “777” 基礎上 “減少” 目錄權限

三個8進位，表示9位元
ex: 
002 (files: 666 & !(002), dirs: 777& !(002))

666 = 110 110 110
!002 = 111 111 101
& = 110 110 100 = 6 6 4
rw- rw- r--
user group other

---

```c
#include "apue.h"
#include <fcntl.h>
#define RWRWRW (S_IRUSR|S_IWUSR|S_IRGRP|S_IWGRP|S_IROTH|S_IWOTH)

int main(void) {
 umask(0);
 if (creat("foo", RWRWRW)  <  0 )
 err_sys("creat error for foo");

 umask(S_IRGRP | S_IWGRP | S_IROTH | S_IWOTH);
 if (creat("bar", RWRWRW)  <  0 )
 err_sys("creat error for bar");
 exit(0);
}
```

這段程式碼是一個使用了 POSIX 標準函數的 C 語言程式，其中使用了 <fcntl.h> 標頭檔和自定義的 apue.h 標頭檔。它創建了兩個文件 foo 和 bar，並使用了 umask() 函數來設置文件的文件模式屏蔽字。

以下是程式碼的操作解釋：

umask(0); - 設置文件模式屏蔽字為 0，即不屏蔽任何權限。這意味著後續使用 creat() 函數創建的文件將使用完整的權限位，即 RWRWRW 定義的值。

if (creat("foo", RWRWRW) < 0 ) - 使用 creat() 函數創建一個名為 foo 的文件，並設置該文件的權限為 RWRWRW，即 -rw-rw-rw-。如果創建文件失敗，則輸出錯誤訊息。

umask(S_IRGRP | S_IWGRP | S_IROTH | S_IWOTH); - 設置文件模式屏蔽字，屏蔽了群組和其他使用者的讀寫權限。這意味著後續使用 creat() 函數創建的文件將不會有群組和其他使用者的讀寫權限。

if (creat("bar", RWRWRW) < 0 ) - 使用 creat() 函數創建一個名為 bar 的文件，並設置該文件的權限為 RWRWRW，即 -rw-r--r--。由於先前使用 umask() 函數設置了群組和其他使用者的屏蔽位，因此 bar 文件的群組和其他使用者將無法進行讀寫操作。如果創建文件失敗，則輸出錯誤訊息。

## chmod

chmod() 函數在特定情況下會自動清除兩個位元：

如果嘗試在沒有超級使用者特權的情況下設置sticky bit （S_ISVTX），則 chmod() 函數會自動清除該位元。sticky bit通常應用於目錄，用於限制非目錄擁有者對該目錄中文件的刪除操作。然而，只有擁有超級使用者特權的使用者才能設置目錄的黏著位。

如果嘗試在沒有超級使用者特權的情況下設置設置組ID位（S_ISGID），則 chmod() 函數會自動清除該位元。
設置組ID位通常應用於目錄或可執行文件，用於指定文件在執行時以該文件的擁有者所屬的組身份運行。然而，只有擁有超級使用者特權的使用者才能設置文件或目錄的設置組ID位。

## sticky bit

早期版本的 UNIX 系統中，程式的文本（機器指令）被保存在交換空間（swap space）中，這樣在第二次執行程式時可以更快地加載文本，因為文本已經存在於交換空間中，無需再次加載。

為了實現這種效果，UNIX 系統引入了一個稱為 "sticky bit"（黏著位）的特殊權限位，用於將程式的文本保存在交換空間中，從而加速程式的執行。這種情況下，程式的文本在第一次執行後會被保存在交換空間中，當再次執行同一個程式時，文本會被直接加載，而不需要重新從文件系統中讀取，從而提高了執行速度。

然而，隨著新一代的 UNIX 系統引入了虛擬記憶和快速檔案系統等技術，這種使用黏著位保存文本的方式變得不再必要，因為現代的文件系統和記憶管理技術已經能夠更高效地處理程式的文本加載和執行，所以在新的系統中不再需要使用黏著位。
virtual memory & fast filesystem, no need of sticky bit

## chown, fchown, fchownat, and lchown Functions



## File Size

st_size

- = 0 empty regular file
- = multiple of 16 or 512 for dir
- = #bytes for links

---

Hole in a File

`ls -l core`
-rw-r--r-- 1 sar 8483248 Nov 18 12:18 core

`du -s core`
272 core

"du" 命令是一個用於計算目錄或文件的磁盤使用空間的實用工具，可以幫助用戶了解文件系統中各個文件和目錄佔用的空間情況。

簡而言之，"ls -l core" 會列出 "core" 文件的**詳細信息**，包括其大小，而 "du -s core" 則僅顯示 "core" 文件的總大小，不顯示詳細信息。

"-s" 選項表示僅顯示摘要信息，即僅顯示總大小，而不顯示每個子目錄或文件的大小。

`wc -c core`
8483248 core

計算名為 "core" 的文件中的字節數。

- 由於 "wc -c core" 只計算文件的字節數，而 "du -s core" 則計算文件佔用的總磁盤空間大小，因此它們的返回值可能會不同。

cat core > core.copy
具體地說，命令中的 "cat" 命令是用於將文件的內容輸出到終端或文件，而 "> core.copy" 則是將輸出的內容重定向到一個新文件 "core.copy" 中，並覆蓋該文件的內容（如果 "core.copy" 文件已經存在的話）。

ls –l core*
-rw-r--r--  1  stevens  8483248  Nov 18  12:18 core
-rw-rw-r-- 1  stevens  8483248  Nov 18  12:27 core.copy

du –s core*
272 core
16592 core.copy
16592 x 512 = 8,495,104 bytes

實際沒用到，並不會在磁盤規劃空間

## File Truncation

```c
truncate(pathname, length)
ftruncate(fd, length)
```

truncate 和 ftruncate 是兩個在常見的系統調用，用於截斷（truncate）或擴展（ftruncate）文件的大小。它們通常在操作文件時用於修改文件的大小。

truncate：將指定文件截斷為指定的大小。如果指定的大小小於原文件大小，則原文件會被截斷，超出指定大小的部分會被刪除。如果指定的大小大於原文件大小，則文件會被擴展，新增部分**會被填充為零字節**。
ftruncate：與 truncate 類似，但是它針對的是已經打開的文件描述符（file descriptor），而不是通過文件名指定的文件。**這意味著您可以在不關閉文件的情況下修改文件的大小。**

## File system

i-node（索引節點）包含了關於檔案的所有資訊，包括檔案的類型、存取權限位、檔案大小、指向檔案資料區塊的指標等等。

在目錄中，i-node 在同一個檔案系統中指向一個 i-node。因此，使用 ln 指令建立的硬連結不能跨越不同的檔案系統。這是因為不同的檔案系統使用不同的 i-node 索引機制，無法直接將一個檔案系統中的 i-node 指向另一個檔案系統中的 i-node。若要在不同的檔案系統間建立連結，可以使用符號連結（Symbolic Link），符號連結是一個特殊類型的檔案，其中包含了指向另一個檔案或目錄的路徑，因此可以跨越不同的檔案系統建立連結。

## link, linkat, unlink, unlinkat, remove

創建硬鏈結（hard link）的系統調用

```c
link(exist_path, new_path)
linkat(fd, exist_path, new_path)
```

create **a new dir** and increment of **link count**

兩個動作必須是原子的，即「連結的創建」和「連結數（link count）的增加」必須同時發生，且不能被其他並發的操作中斷。

```c
unlink(pathname)
unlinkat(fd, pathname, flag)
```

remove dir and decrement link count

**file deleted** only when link count = 0, open count = 0 (fd,確保沒有正在使用)

---

- delete file 只是將 link 拿掉而已，實際上檔案依然存在於硬碟，只是沒有 link 指向他
- 如果要真正刪除一個文件，通常需要使用一些特殊的工具或者技術。以下是一些可能的方法：

1. 使用安全刪除工具：有些安全刪除工具可以通過覆寫文件的內容多次來確保文件在硬碟上被真正刪除，例如使用 "shred" 命令或者其他的安全刪除工具。

2. 使用硬碟擦除工具：硬碟擦除工具可以通過對整個硬碟或者分區進行覆寫來確保文件在硬碟上被完全擦除，例如使用 "dd" 命令或者其他的硬碟擦除工具。

---

所有的 directory 都至少有兩條 link 一條指向自己另一條指向 parent 或 child

- 例如，在一個目錄中創建一個新的子目錄時，該子目錄的 Parent link 將會指向您創建它的父目錄，而父目錄的 Child link 則會指向新創建的子目錄。

- 每個目錄至少有兩條連結，一條指向自己，另一條指向其父目錄或子目錄，這樣才能建立目錄層級的關係，讓文件系統能夠正確遍歷和管理目錄結構。這是文件系統設計的一個重要原則。

---

|Hard link|Symbolic link|
|---|---|
|super user 才可建立|any one|
|不可跨file system|可跨file system|

硬連結是通過將一個文件名與一個文件數據區塊關聯起來，使得多個文件名可以引用相同的文件數據。硬連結只是在文件系統中創建了多個指向相同文件數據的硬連結條目，它們在文件系統層面上是完全相等的，沒有主從之分。硬連結只能在同一文件系統中創建，即它們必須位於同一個硬碟分區上，因此無法跨越不同的文件系統。

而符號連結則是一種特殊類型的文件，其中包含了對另一個文件或目錄的引用。符號連結類似於 Windows 系統中的快捷方式，它只是包含了指向實際文件或目錄的路徑信息。因此，符號連結可以引用位於不同文件系統中的文件或目錄，即可以跨越不同的文件系統。

---

當連結數（link count）和開啟數（open count）都等於0時，檔案才會被刪除。這樣的屬性適用於臨時檔案，當程式執行結束或崩潰時，臨時檔案不會被留下。

若使用 open() 或 creat() 函式建立的臨時檔案，可以立即使用 unlink() 函式將其刪除，因為檔案在創建後還未被開啟，所以連結數和開啟數都為0，檔案會被刪除。

如果檔案仍然被開啟，那麼即使呼叫 unlink() 函式，檔案不會被刪除，而是在該檔案的開啟數變為0或程式結束時才會被刪除。

這些屬性可用於處理臨時檔案，確保在程式執行結束或異常終止時，臨時檔案能夠被正確刪除，不會留下垃圾檔案。

## futimens, utimensat, and utimes

這些函式是用於在UNIX或類UNIX系統中設置檔案的存取和修改時間的函式。以下是它們的功能說明：

1. futimens(): 該函式用於設置**指定檔案**的**存取時間和修改時間**。它接受一個檔案描述符（file descriptor）和一個包含兩個時間值的 timespec 數組作為參數，分別表示存取時間和修改時間。這兩個時間值可以是絕對時間（absolute time）或相對時間（relative time）。

2. utimensat(): 該函式用於設置**指定檔案或目錄**的**存取時間和修改時間**。它接受一個目錄描述符（directory descriptor）和一個路徑名（path name）作為參數，以及一個包含兩個時間值的 timespec 數組，表示存取時間和修改時間。這兩個時間值可以是絕對時間或相對時間。此外，utimensat() 函式還可以通過一個參數指定選項，例如是否遞迴設置時間，以及如何處理符號連結。

utimes(): 該函式用於設置指定檔案的存取時間和修改時間。它接受一個路徑名作為參數，以及一個包含兩個時間值的 timeval 數組，表示存取時間和修改時間。這兩個時間值可以是絕對時間或相對時間。

這些函式可用於在程式中動態地設置檔案的存取和修改時間，例如在處理檔案的時候需要修改或紀錄檔案的時間戳。