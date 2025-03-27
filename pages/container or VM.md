public:: true

- ## 前言
- # 目錄
	- ((adcea832-e9cb-44ff-80be-356de76568ef))
	-
- # podman
  id:: adcea832-e9cb-44ff-80be-356de76568ef
  collapsed:: true
	- ### 要能夠讓容器中的視窗軟體呈現出來
		- 利用x11的server讓主系統接收容器視窗
		- 必須添加`--env DISPLAY`提供主系統的`$DISPLAY`環境變數
		- 主系統必須有xhost程式，使用`xhost +`時還傳結果必須為`access control disabled, clients can connect from any host`
		- `--volume /tmp/.X11-unix:/tmp/.X11-uni`此參數為使主系統（`/tmp/.X11-unix`）與容器（`/tmp/.X11-uni`）共享資料夾
		  id:: 66c1f26d-0f28-4418-85fa-ae92b346b1ed
		- ```bash
		  podman run --interactive --tty --name <container> --volume /tmp/.X11-unix:/tmp/.X11-unix --env DISPLAY <image>
		  ```
	- ### 啟動並連接容器
		- ```bash
		  podman start <container>
		  podman exec --env DISPLAY=$DISPLAY -it <container> /bin/bash
		  ```
	- ### 更改容器網路設置
		- ```bash
		  # 解除容器網路狀態
		  podman network disconnect mynetwork mycontainer
		  # 設置新網路狀態給容器
		  podman network connect newnetwork mycontainer
		  ```
	- ### 將容器轉成映像檔
		- ```bash
		  podman commit -a "Author" -m "Update Commit Info" <container> <new_image_name>
		  ```