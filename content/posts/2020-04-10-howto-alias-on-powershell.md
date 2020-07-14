---
title: "How to alias neovim to vi on powershell"
date: 2020-04-10T16:37:11+09:00
draft: false
categories: [howtolog]
tags: [powershell, alias, profile]
---

On Powershell environment, you can use alis as following command pattern.

```cmd
PS> Set-Alias vi C:\tools\neovim\Neovim\bin\nvim.exe
```


If so, how can I automatically applied alias when open new powershell window?

<!-- more -->

Is there any run commands file something like bash.rc?
Yes It is.

```cmd
PS> $profile
C:\Users\seung\OneDrive\문서\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
PS> mkdir C:\Users\seung\OneDrive\문서\WindowsPowerShell
PS> nvim C:\Users\seung\OneDrive\문서\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
```


Then set alias command to something.
In my case?

```cmd
PS> Get-Content C:\Users\seung\OneDrive\문서\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
set-alias vi nvim
```


It's EASY. isn't it? NO!! it is not easy that much.
You would get following (similar) error if you open new powershell.

```cmd
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

새로운 크로스 플랫폼 PowerShell 사용 https://aka.ms/pscore6

. : 이 시스템에서 스크립트를 실행할 수 없으므로 C:\Users\seung\OneDrive\문서\WindowsPowerShell\Microsoft.PowerShell_pro
file.ps1 파일을 로드할 수 없습니다. 자세한 내용은 about_Execution_Policies(https://go.microsoft.com/fwlink/?LinkID=1351
70)를 참조하십시오.
위치 줄:1 문자:3
+ . 'C:\Users\seung\OneDrive\문서\WindowsPowerShell\Microsoft.PowerShell_ ...
+ +   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+     + CategoryInfo          : 보안 오류: (:) [], PSSecurityException
+         + FullyQualifiedErrorId : UnauthorizedAccess
PS>
```


You should set Security Policy.
I use following policy

```cmd
PS> Set-ExecutionPolicy RemoteSigned

실행 규칙 변경
실행 정책은 신뢰하지 않는 스크립트로부터 사용자를 보호합니다. 실행 정책을 변경하면 about_Execution_Policies 도움말
항목(https://go.microsoft.com/fwlink/?LinkID=135170)에 설명된 보안 위험에 노출될 수 있습니다. 실행 정책을
변경하시겠습니까?
[Y] 예(Y)  [A] 모두 예(A)  [N] 아니요(N)  [L] 모두 아니요(L)  [S] 일시 중단(S)  [?] 도움말 (기본값은 "N"): Y
```


But you can use following altenatively.

* Restricted – No scripts can be run.
* AllSigned – Only scripts signed by a trusted publisher can be run.
* RemoteSigned – Downloaded scripts must be signed by a trusted publisher.
* Unrestricted – All Windows PowerShell scripts can be run.


Detailed explaination is on [Set-ExecutionPolicy](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7#parameters)

End. seriously :)

