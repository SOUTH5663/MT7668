# MT7668

## Porting Guide (这是无关紧要的)

在 WiFi driver 的目录中有 3 个 Makefile，我们使用的是 Makefile.ce。Porting 到不同的平
台，需要修改 Makefile.ce。需要修改的有:

1. PLATFORM 是自定义的平台名字，如:
   
   ```
   PLATFORM:= MS6586
   ```

2. MODULE_NAME 是 7668BU 的 ko 的名字，可根据各家自定义，如:

   ```
   MODULE_NAME := mt7668tu
   ```

3. LINUX_SRC 是指定的 kernel path，如：

   ```
   LINUX_SRC=/opt/wifi/customer/mstar/6586/tegra
   ```

4. CROSS_COMPILE 是所要使用的交叉编译工具链，如：

   ```
   CROSS_COMPILE= /opt/wifi/customer/mstar/6586/arm-2014.05/bin/arm-none-linuxgnueabi-
   ```

5. ARCH 根据不同的平台做适配，如果平台是 32 位

   ```
   export ARCH:=arm 
   ```

   如果平台是 64 位

   ```
   export ARCH:=arm64
   ```

6. CONFIG_MTK_PREALLOC_MEMORY 这个宏要打开，以便生成 mt7668tu_prealloc.ko，这
个 ko 的作用稍后介绍

   ```
   export CONFIG_MTK_PREALLOC_MEMORY=y
   ```

7. 如果不进行下面的配置，build 出来的 ko 会很大，所以这里需要配置

   ```
   ifeq ($(PLATFORM), AMLOGIC_T972)
    $(CROSS_COMPILE)strip --strip-unneeded $(MODULE_NAME).ko
   Endif
   ```
   
   上面所需要修改的 7 点配置正确 build 成功后会在 Makefile.ce 所在的目录下生成两个
ko：mt7668tu.ko 和 mt7668tu_prealloc.ko。

   在前面提到的 CONFIG_MTK_PREALLOC_MEMORY 这个宏，开了这个宏生成了上面的 2
个 ko，prealloc 的作用是为 WiFi 提前申请资源。

   如果关闭宏 CONFIG_MTK_PREALLOC_MEMORY，只会生成 mt7668tu.ko，WiFi 申请资
源只在用到时才申请
