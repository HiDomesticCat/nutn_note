public:: true

- ## 前言
	- ### 資料來源
- # 目錄
	- ((66b75a21-4759-41c2-b839-a124efe86c5c))
	- ((66baca52-3f65-43bb-aab7-82fc466210ad))
- # Sqlite
  id:: 66b75a21-4759-41c2-b839-a124efe86c5c
  collapsed:: true
	- ## 資料來源
		- https://www.tutorialspoint.com/sqlite/index.htm
	- ## Command
		- .help
			- 顯示幫助文檔
		- .exit
		- .databases
		- .open FILENAME
			- 開啟儲存的持久性資料庫
		- .load
	- ## PRAGMA
		- https://www.sqlite.org/pragma.html
	- ## 延時
		- randomblob(100000000)
			- 在sqlite沒有sleep()相關的函數，所以要使用randomblob()代替，但延時的時間並不固定
- # SQL Injection
  id:: 66baca52-3f65-43bb-aab7-82fc466210ad
  collapsed:: true
	- ## 漏洞成因
		- 未對 SQL 查詢命令的輸入做檢測