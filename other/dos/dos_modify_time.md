

Windows下修改文件的创建时间、访问时间、修改时间

```powershell
@ECHO OFF
powershell.exe -command "$date='2022-02-19 19:20:28'; $path= resolve-path docsify-blog;  get-Item $path -exclude 'node_modules' | forEach{ $_.LastWriteTime = $date; $_.CreationTime = $date; $_.LastAccessTime= $date }; dir -Path $path -Exclude 'node_modules' | dir  -Recurse  | foreach-object { $_.LastWriteTime = $date; $_.CreationTime = $date; $_.LastAccessTime= $date }" 
PAUSE
```