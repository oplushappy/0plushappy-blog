---
title: Linux File I/O (3)
date: 2023-04-11 18:44:08
tags: Linux
---
## Introduction

kernel mode : unbuffered I/O
each read and write invoke system call

FILE I/O

- open
- read
- write
- lseak
- close

FILE Sharing and atomic operation : 檔案分享中，原子操作通常用於處理多個使用者對同一個檔案的併發訪問。例如，當多個使用者同時對一個檔案進行讀寫操作時，可能會產生競爭條件，導致檔案的內容不一致或出現錯誤。使用原子操作可以確保對檔案進行的讀寫操作是同步進行的，並且不會出現競爭條件的問題，保證檔案的一致性和正確性。

- dup
- fcntl
- sync
- fsync
- ioctl

## File Descriptor

Represent a open file
type : integer(非負)

- **open** and **create** : 返回值
- **read** and **write** : 參數

ex :

- 標準輸入 : STDIN_FILENO = 0
- 標準輸出 : STDOUT_FILENO = 1
- 標準錯誤 : STDERR_FILENO = 2

## open vs openat

```c
open(path, oflag, mode)
```

```c
openat(fd, path, oflag, mode)
```

## create

```c
create(path, mode)
=
open(path, O_WRONLY | O_CREAT | O_TRUNC, mode)
```

## File sharing

Process Table（進程表）：內核使用進程表來管理所有正在運行的進程。進程表通常是一個數組或散列表，其中每個項目都包含了有關進程的信息，例如進程ID（PID）、父進程ID、用戶ID、狀態、記憶體佔用等。內核可以通過進程表來查詢和操作進程，例如創建、終止、調度和切換進程。

File Table（文件表）：內核使用文件表來管理系統中打開的文件。文件表通常是一個數組或散列表，其中每個項目都包含了有關文件的信息，例如文件描述符（file descriptor）、文件狀態標誌、文件位置指針等。內核可以通過文件表來查詢和操作文件，例如打開、關閉、讀寫和定位文件。

V-node Table（v節點表）：在某些UNIX系統中，內核使用V節點表來管理文件系統中的文件。V節點（v-node）是一個數據結構，包含了文件的元數據（metadata）信息，例如文件類型、所有者、許可權、創建時間等。V節點表通常是一個數組或散列表，其中每個項目都對應著一個打開的文件，並包含了相關的V節點信息。內核可以通過V節點表來查詢和操作文件的元數據。值得注意的是，不是所有的UNIX系統都使用V節點表，而是使用類似的數據結構來管理文件系統中的文件。


lseek only modify current file offset

- For write of same file by multiple processes, unexpected results can occur

檔案位移（File Offset）是指檔案中目前讀取或寫入操作將發生的位置。

```c
lseek(fd, offset, whence)
```

在 lseek 函數中，whence 參數用於指**定偏移量 offset 的參考點或起始位置**。

whence 可以使用以下三個常數之一：

- SEEK_SET：從檔案開頭開始計算偏移量，偏移量值為 offset。
- SEEK_CUR：從目前的檔案位置開始計算偏移量，偏移量值為目前檔案位置加上 offset。
- SEEK_END：從檔案結尾開始計算偏移量，偏移量值為檔案大小加上 offset。

## sync, fsync, fdatasync

sync函數只是將所有修改過的塊緩衝區排入寫隊列，然後就返回，它並不等待實際寫磁盤操作結束。

通常稱為update的系統守護進程會周期性地（一般每隔30秒）調用sync函數。這就保證了定期沖洗內核的塊緩衝區。命令sync(1)也調用sync函數。

fsync函數只對由fd指定的單一文件起作用，並且等待寫磁盤操作結束，然後返回。
fsync可用於數據庫這樣的應用程序，這種應用程序需要確保將修改過的塊立即寫到磁盤上。

fdatasync函數類似於fsync，但它只影響文件的數據部分。而除數據外，fsync還會同步更新文件的屬性。

除了同步文件的修改內容（臟頁），fsync還會同步文件的描述信息（metadata，包括size、訪問時間st_atime & st_mtime等等）

## ioctl

```c
ioctl(fd, request)
```

在磁帶上使用 ioctl 函數可以執行以下操作：

寫入文件結束標記（End-Of-File Marks，EOF Marks）：磁帶是一種線性存儲媒體，ioctl 函數可以用於寫入文件結束標記，標記文件的結束。這對於在磁帶上保存多個文件時，可以標記不同文件之間的邊界，以便在讀取時能夠識別出文件的結束。

倒帶磁帶（Rewind Tape）：ioctl 函數可以用於控制磁帶驅動器將磁帶倒帶到起始位置。這在需要重新訪問磁帶上的數據或者在磁帶操作之前需要進行初始化時可能會使用。

在磁帶上進行前向定位（Space Forward over Files or Records）：ioctl 函數可以用於控制磁帶驅動器在磁帶上前向定位，跳過一定數量的文件或者記錄。這對於在磁帶上查找特定文件或者記錄，或者進行快速定位和跳過一部分數據時可能會使用。

No other function (read, write, lseek) 

## dev/fd

```c
open("/dev/fd/n", mode) = dup(n)
```

位於 /dev/fd 目錄下的文件可以通過使用fd的整數值作為文件名來訪問。
例如，/dev/fd/0 表示標準輸入（stdin），/dev/fd/1 表示標準輸出（stdout），/dev/fd/2 表示標準錯誤（stderr），以此類推。

### |

```shell
command1 | command2
```

將 command1 的輸出作為 command2 的輸入進行處理

當使用管道運算符 | 將命令串聯在一起時，前一個命令的輸出會成為後一個命令的輸入。
這樣，前一個命令的輸出結果可以直接傳遞給後一個命令進行處理，而不需要中間文件或臨時存儲。

```shell
filter file2 | cat file1 – file3 | lpr
```

這個命令由三個部分組成，使用了管道 | 符號將它們串聯在一起：

filter file2：這是第一個命令，它執行一個名為 filter 的程序，並將 file2 文件的內容作為輸入。它的輸出將成為下一個命令的輸入。

cat file1 - file3：這是第二個命令，它使用 cat 命令，通過 - 表示標準輸入，將 file1 文件的內容、第一個命令的輸出以及 file3 文件的內容合併在一起。這裡的 - 表示標準輸入，也可以寫成 /dev/stdin。合併後的內容將成為下一個命令的輸入。

lpr：這是第三個命令，它是一個列印命令，將第二個命令的輸出作為打印作業輸入，將其列印出來。

總的來說，這個命令的作用是將三個文件（file1、file2、file3）的內容合併在一起，並通過管道 | 將它們傳遞給 lpr 命令進行列印。其中，file2 的內容通過 filter 程序進行處理，並與 file1、file3 的內容合併後一起傳遞給 lpr 命令
