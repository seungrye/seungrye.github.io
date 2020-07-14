---
title: "Windows docker mount point setting"
date: 2020-07-14T15:11:00+09:00
draft: false
toc: false
categories:
  - studylog
      tags:
  - tip
  - docker
  - wsl2
  - windows
  - mounts
      ---

      Windows 10 에서 Docker Desktop 을 사용할때, mount point 를 어떻게 지정해야 하는지 모르겠을때 참고하기 위해 작성합니다.


![WSL2Integrated](/posts/resources/_gen/images/2020-07-15 000903.png)
WSL2 integrated 되어 있을때의 설정입니다.

![MountPoint](/posts/resources/_gen/images/2020-07-15 000851.png)
`/run/desktop/mnt/host/{drive}/{host path}` 와 같이 지정하면 됩니다.

Note. Windows container 이거나, WSL2 가 not integrated 일때는 mount point 가 달라져야 할 수 있습니다.


캡쳐된 이미지만 봐도 충분히 알수 있을것 입니다. ( ͡~ ͜ʖ ͡°)

