# 大气层支持新固件的适配指南

根据对Atmosphere项目的分析，当任天堂更新Switch固件后，需要修改以下几个关键部分来实现大气层支持最新的固件版本：

## 1. 固件版本定义更新

**需要修改的文件：** `libraries/libvapours/include/vapours/ams/ams_target_firmware.h`

**修改内容：**
- 在文件末尾添加新的固件版本定义，例如：
  ```c
  #define ATMOSPHERE_TARGET_FIRMWARE_XX_X_X ATMOSPHERE_TARGET_FIRMWARE(XX, X, X)
  ```
- 更新`ATMOSPHERE_TARGET_FIRMWARE_CURRENT`为最新版本
- 在`TargetFirmware`枚举中添加对应的枚举值

## 2. Package1版本检测更新

**需要修改的文件：** `fusee/program/source/fusee_setup_horizon.cpp`

**修改内容：**
- 在`GetApproximateTargetFirmware`函数中添加新的固件版本检测逻辑
- 找到新固件对应的package1标识符（通常是日期字符串）
- 添加对应的case分支，返回新的固件版本

## 3. FS版本哈希更新

**需要修改的文件：** `fusee/program/source/fusee_stratosphere.cpp`

**修改内容：**
- 在`FsVersion`枚举中添加新的FS版本
- 在`FsHashes`数组中添加新的FS版本哈希值（需要从新固件中提取）
- 同时添加EXFAT版本的哈希值（如果适用）

## 4. NoGC补丁偏移量更新

**需要修改的文件：** `fusee/program/source/fusee_stratosphere.cpp`

**修改内容：**
- 在`AddNogcPatches`函数中为新的固件版本添加对应的补丁偏移量
- 需要分析新固件的FS模块，找到需要修改的位置
- 添加新的case分支，设置正确的补丁偏移量

## 5. 引导加载相关更新

**可能需要修改的文件：**
- `fusee/program/source/fusee_setup_horizon.cpp`（warmboot固件处理）
- `exosphere/warmboot/source/warmboot_main.cpp`（warmboot实现）

**修改内容：**
- 确保warmboot固件能够正确处理新的固件版本
- 检查并更新相关的魔术值和偏移量

## 6. 系统模块配置更新

**可能需要修改的文件：**
- `stratosphere/*/source/*_main.cpp`（各个系统模块）
- `stratosphere/*/*.json`（模块配置文件）

**修改内容：**
- 确保各个系统模块能够正确处理新的固件版本
- 更新模块配置文件中的版本信息

## 7. EmuMMC支持更新

**需要修改的文件：** `emummc/source/FS/FS_versions.h`

**修改内容：**
- 在`FS_VER`枚举中添加新的FS版本
- 更新`FS_VER_MAX`为最新版本

## 8. 测试和验证

**验证步骤：**
1. 编译更新后的Atmosphere
2. 在测试设备上测试引导过程
3. 验证系统功能是否正常
4. 测试EmuMMC功能是否正常
5. 检查是否有任何错误或崩溃

## 核心修改流程总结

1. **获取新固件信息**：提取package1标识符、FS哈希值等
2. **更新版本定义**：在相关文件中添加新的固件版本
3. **更新检测逻辑**：修改package1版本检测代码
4. **更新补丁信息**：添加新的FS哈希和nogc补丁偏移量
5. **编译测试**：验证修改是否正确
6. **发布更新**：提供支持新固件的Atmosphere版本

## 注意事项

- 每次固件更新可能会有不同程度的变化，有些版本可能只需要更新版本定义，而有些版本可能需要更复杂的修改
- 始终确保从可信来源获取新固件的信息
- 在修改代码前，建议先备份原始代码
- 测试时使用测试设备，避免在主要设备上测试未经验证的修改

在原作者不再维护该项目后，通过以上步骤尝试使Atmosphere保持对最新Switch固件的支持。