# 修改文件前缀

```bat
@echo off
Setlocal Enabledelayedexpansion
set "str=前缀字符"
for /f "delims=" %%i in ('dir /b *.mp4') do (
set "var=%%i" 
:: 去掉括号
set "var=!var:(=!"
set "var=!var:)=!"
:: 去掉空格
set "var=!var: =!"
:: 去掉想要删除的字符
set "var=!var:%str%=!"
:: 显示
echo %%i  !var!
:: 重命名
ren "%%i" "!var!"
)
pause
```