# 红外发射接收器

- 安装lirc
	* sudo apt-get install lirc
		#绑定模块
		#这里注意下，执行 sudo modprobe lirc_rpi， 默认会加上 gpio_in_pin=18 gpio_out_pin=17 绑定 gpio18口为输入，gpio17口为输入
- 配置端口
        #gpio的绑定根据自己怎么接的线决定 
	    #我绑定的是 23和24
	1. sudo modprobe lirc_rpi gpio_in_pin=23 gpio_out_pin=24
    2. 手动添加
    
        ``` 
        sudo sudo nano /boot/config.txt
        dtoverlay=lirc-rpi,gpio_in_pin=18,gpio_out_pin=17
        ```
- 编辑配置文件
    * sudo vim /etc/lirc/hardware.conf
    ```
    ########################################################
    # /etc/lirc/hardware.conf
    #
    # Arguments which will be used when launching lircd
    LIRCD_ARGS="--uinput --listen"
     
    # Don't start lircmd even if there seems to be a good config file
    # START_LIRCMD=false
     
    # Don't start irexec, even if a good config file seems to exist.
    # START_IREXEC=false
     
    # Try to load appropriate kernel modules
    LOAD_MODULES=true
     
    # Run "lircd --driver=help" for a list of supported drivers.
    DRIVER="default"
    # usually /dev/lirc0 is the correct setting for systems using udev
    DEVICE="/dev/lirc0"
    MODULES="lirc_rpi"
     
    # Default configuration files for your hardware if any
    LIRCD_CONF=""
    LIRCMD_CONF=""
    ########################################################
    ```
- 编辑modules配置 绑定gpio接口
    * sudo vim /etc/modules
    
    ```
        lirc_dev
        lirc_rpi gpio_in_pin=23 gpio_out_pin=24
    ```
- 重启lirc
    
    ```
    sudo /etc/init.d/lirc stop
    sudo /etc/init.d/lirc start
    ```
- 测试
    * 按遥控器
    
    ```
        mode2 -d /dev/lirc0
    ```
    
- 录制
    ```
    #查看可以使用的key列表，随后输入key必须在这列表里
    irrecord --list-namespace
    #录制前需要关闭lirc
    sudo /etc/init.d/lirc stop
    #开始录制，开始后需要随便按键通过2*80的调试
    #按后会出现...，第一排长按，第二排点按
    irrecord -n -d /dev/lirc0 lircd.conf
    irrecord -d /dev/lirc0 ~/lircd.conf
    常用命令
    KEY_VIDEO_PREV
    KEY_VIDEO_NEXT
    #当前路径就生成了 lircd.conf 文件，复制到 /etc/lirc/ 下
    sudo cp lircd.conf /etc/lirc/lircd.conf
    sudo /etc/init.d/lirc restart
    ```
    
- 测试
    * irsend SEND_ONCE /home/pi/lircd.conf KEY_VIDEO_PREV
    * 使用irw命令测试 sudo irw
