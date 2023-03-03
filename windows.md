# windows command tips

## show seconds
```bat
reg add HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced /v ShowSecondsInSystemClock /t REG_DWORD /d 1 /f
taskkill /f /im explorer.exe
start %windir%\explorer.exe
```
