##**iOS Demo 真机调试时可能遇到“不包含 Bitcode”的错误**
---
###**问题**
官方网站提供的 iOS SDK Demo 程序在真机调试时，可能会遇到“不包含 Bitcode”的错误提示：
<br>

>ld: '/YunBa-iOS-sdk-1.5.2/YunBaSDK/libYunBa.a(YunBaService.o)' does not contain bitcode. You must rebuild it with bitcode enabled (Xcode setting ENABLE_BITCODE), obtain an updated library from the vendor, or disable bitcode for this target. for architecture armv7

>clang: error: linker command failed with exit code 1 (use -v to see invocation)


###**解决方案**
在 Xcode 开发环境中，找到 Product --> Build --> Build Settings --> Build Options --> Enable Bitcode 项，将 Yes 改为 No 即可。

