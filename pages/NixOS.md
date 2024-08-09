public:: true

- ## 前言
	- ### 資料來源
		- https://nixos-and-flakes.thiscute.world/
- # 目錄
	- ((655d9c5e-e67e-44bf-8bea-16200b43904f))
	-
- # Nix Lang
  id:: 655d9c5e-e67e-44bf-8bea-16200b43904f
  collapsed:: true
	- ### 基礎資料型態
		- ```Nix
		  {
		  	# comments are supported
		  	string = "hello";
		  	integer = 15;
		  	float = 15.21;
		  	bool = true;
		  	null = null;
		  	list = [15 "test" false];
		  	attribute-set = {
		      	a = "hello";
		          b = 2;
		          c = 3.14;
		          d = false;
		      };
		  }
		  ```
	- ### 基礎運算符
		- ```nix
		  # list 連接
		  [1 2 3] ++ [4 5 6] # [1 2 3 4 5 6]
		  
		  # // 後面attribute-set的資料更新至前面的attribute-set
		  {a = 1; b = 2} // {b = 3; c = 4} # {a = 1; b = 3; c = 4}
		  
		  # logic implies, 等同於 !b1 || b2
		  bool -> bool
		  ```
	- ### let 表達式
		- ### 用於創建區域變數
		- ```nix
		  let
		  	a = 1;
		  in
		  	a + a 
		  # 結果為2
		  ```
	- ### if 判斷式
		- ### nix 的 if 判斷式可以返回接果
		- ```nix
		  if 3 > 4 then "yes" else "no" # 返回 "no"
		  ```
		- ### 可搭配 let 表達式使用
		- ```nix
		  let
		  	x = 3;
		  in
		  	if x > 4 then "yes" else "no" # 結果返回 "no"
		  ```
	- ### attribute set
		- ### 利用 {} 創建 attribute set ，也就是類似 map 的 key-value 集合
		- ### attribute set 默認不支持遞迴引用，需要使用 rev 關鍵字，但遞迴引用會按照聲明順序來求值，所以被引用的變數一定要在引用前聲明
			- ```nix
			  # error: undefined variable 'a'
			  {
			  	a = 1;
			      b = a + 1;
			  }
			  
			  # OK
			  rec {
			  	a = 1;
			  	b = a + 1;
			  }
			  ```
		- ### 可以利用 . 符號來訪問 attribute set 的成員
			- ```nix
			  let
			  	a = {
			  		b = {
			              c = 1;
			          };
			      };
			  in
			  	a.b.c # 結果為 1
			  ```
		- ### 也可以利用 . 符號進行賦值
			- ```nix
			  { a.b.c = 15; }
			  ```
		- ### has attribute 操作符
			- ```nix
			  let
			  	a = {
			  		b = {
			  			c = 1;
			  		};
			  	};
			  in
			  	a ? b # 結果回傳 true，因為 a.b 這個 attribute 存在
			  ```
	- ### with 語句
		- ```nix
		  with <attribute-set> ; <expression>
		  ```
		- ### 例子
			- ```nix
			  let
			    a = {
			      x = 1;
			      y = 2;
			      z = 3;
			    };
			  in
			  with a; [ x y z ] # 回傳 [1 2 3]，等價 [a.x a.y a.z]
			  ```
	- ### inherit 繼承
		- ### 用於從 attribute set 中繼承成員
			- ```nix
			  let
			  	x = 1;
			      y = 2;
			  in
			  {
			  	inherit x y;
			  } # 回傳 { x = 1; y = 2; }
			  ```
		- ### 也可用於從某個 attribute set 中繼承成員
			- ```nix
			  inherit (<attribute-set>) <member-name>;
			  ```
			- ```nix
			  let
			  	a = {
			      	x = 1;
			          y = 2;
			          z = 3;
			      };
			  in
			  {
			  	inherit (a) x y;
			  } # 回傳 { x = 1; y = 2; }
			  ```
	- ### ${...} 字串插值
		- ```nix
		  let
		  	a = "Hi Cat";
		  in
		  	"my name is ${a}" # 回傳 "my name is Hi Cat"
		  ```
	- ### 文件系統路徑
		- ### nix 中不帶引號的字串會被解析成文件系統路徑，路徑語法與 UNIX 系統相同
	- ### 路徑搜尋 <不應使用，會有不可預期的行為>
		- ### 當 nix 執行到 <nixpkgs> 這類角括號時，會在 NIX_PATH 環境變數中指定的路徑搜尋該路徑
		- ### 但由於 NIX_PATH 是可變的值，所以此功能是不純的，會造成不可預期之行為
	- ### 多行字串
		- ```nix
		  ''
		    this is a
		    multi-line
		    string
		  ''
		  ```
	- ### 字串轉義
		- ### 單行字串可以直接使用 \ 進行轉義
			- ```nix
			  "this is a \"string\" \\" # 結果為 this is a "string" \
			  ```
		- ### 多行字串則需要多加兩個單引號 '' 轉義
			- ```nix
			  let
			    a = "1";
			  in
			  ''the value of a is:
			    ''${a}
			  ''  # 結果為 "the value of a is ''${a}"
			  ```
			- ```nix
			  ''
			    this is a
			    multi-line
			    string
			    ''\n
			  '' # 結尾會換行
			  ```
			- ```nix
			  let
			    a = "1";
			  in
			  ''the value of a is:
			    '''${a}'''
			  ''  # 結果為 "the value of a is ''1''"
			  ```
	- ### 函數
		- ```nix
		  <arg1>: <body>
		  ```
		- ### 常見範例
			- ```nix
			  # 單參數
			  a: a + a
			  
			  # 嵌套函數
			  a: b: a + b
			  
			  # 雙參數
			  {a, b}: a + b
			  
			  # 帶有默認值得雙參數函數
			  {a ? 1, b ? 2}: a + b
			  
			  # 帶有命名 attribute set 作為參數的函數，
			  # 並使用 ... 收集其他可選參數，此二者通常會一起使用
			  args@{a, b, ...}: a + b + args.c
			  等價於
			  {a, b, ...}@args: a + b + args.c
			  ```
		- ### 由於命名參數緊綁定輸入的 attribute set，所以默認參數將不在其中
			- ```nix
			  let
			    f = { a ? 1, b ? 2, ... }@args: args;
			  in
			    f {} # 結果為 {}，可看出默認參數不在 args 中
			  ```
		- ### 調用方式就是把參數放在後方
			- ```nix
			  a: a + a 2 # 輸出將為 4 
			  ```
		- ### 要給函數命名，必須使用 let 表達式
			- ```nix
			  let
			    f = a: a + a;
			  in
			    f 2 # 結果為 4
			  ```
	- ### 內置函數
		- #### 補充內置函數: https://nixos.org/manual/nix/stable/language/builtins.html
		- ### 利用以下方式，可以呼叫 nix 內置之函數
			- ```nix
			  builtins.<function-name>
			  
			  # 範例
			  builtins.add 1 2  # 結果為 3
			  ```
	- ### import 表達式
		- ### 呼叫其他 nix 文件的路徑作為參數，並返回 nix 文件執行之結果，如果參數為文件夾路徑，則會執行文件夾下的 default.nix 文件
		- ### 創建 nix 文件
			- ```shell
			  $ echo "x: x + 1" > file.nix
			  ```
		- ### 執行
			- ```nix
			  import ./file.nix 1  # 結果為 2
			  ```
		- ### pkgs.lib 函數包
			- ### nix 的 nixpkgs 倉庫還提供了名為 lib 的 attribute set ，其中包含一些常用函數，通常用以下形式呼叫
				- ```nix
				  let
				    pkgs = import <nixpkgs> {};
				  in
				  pkgs.lib.strings.toUpper "search paths considered harmful" # 結果為 "SEARCH PATHS CONSIDERED HARMFUL"
				  ```
	- ### impurities [不純]
		- ### 由於 nix lang 本身是純函數語言，所以同樣的輸入將永遠得到同樣的輸出，出了一個例外，就是利用文件系統路徑或從其他輸入源讀取文件作為構建任務的輸入時，文件內容或輸入源返回之內容可能會變化，造成相同輸入，卻得到不一樣的輸出，也就是函數變得不純了
	- ### Fetchers
		- ### 構建輸入除了來自文件系統路徑之外，還可通過 Fetchers 獲取，其是一種特殊函數，輸入為一個 attribute set，輸出則是 Nix Store 中的一個系統路徑
		- ### builtins.fetchurl
			- ### 從 url 中下載文件
		- ### builtins.fetchTarball
			- ### 從 url 中下載 tarball [壓縮包] 文件
		- ### builtins.fetchGit
			- ### 從 git 倉庫中下載文件
		- ### builtins.fetchClosure
			- ### 從 Nix Store 中獲取 Derivation
	- ### Derivation
		- `待補充`
		- ### https://nixos-and-flakes.thiscute.world/zh/the-nix-language/#derivations