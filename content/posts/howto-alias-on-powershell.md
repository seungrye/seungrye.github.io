---
title: "How to alias neovim to vi on powershell"
date: 2020-04-10T16:37:11+09:00
draft: false
categories: [howtolog]
tags: [powershell, alias, profile]
---

Powershell 환경에서 alias 를 사용하기 위해서는 다음의 명령어를 사용하면 됩니다.

```powershell
PS> Set-Alias vi C:\tools\neovim\Neovim\bin\nvim.exe
```

그렇다면, powershell 을 실행했을때 자동으로 alias 가 적용되게 하려면 어떻게 해야 할까?
bash 와 유사하게 bash.rc 이런 파일은 없을까?
있다.

```powershell
PS> $profile
C:\Users\seung\OneDrive\문서\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
PS> mkdir C:\Users\seung\OneDrive\문서\WindowsPowerShell
PS> nvim C:\Users\seung\OneDrive\문서\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
```

Then set alias command to something.
In my case?

```powershell
PS> Get-Content C:\Users\seung\OneDrive\문서\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
set-alias vi nvim
```

It's EASY. isn't it? :P
