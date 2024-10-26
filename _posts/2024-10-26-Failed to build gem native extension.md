---
title: bundle update Error Failed to build gem native extension.
date: 2024-10-26
categories: [Etc, TIL]
tags: [wdm, ruby]
---

![image](https://github.com/user-attachments/assets/62f49681-caeb-4d29-b89d-1b20947c0239)


# 해결
[Github Issues](https://github.com/jekyll/jekyll/issues/9615)


wdm이 ruby의 최신 버전에서 동작하지 않는 것이 원인이었다.  wdm은 성능과 자동 재생성을 개선할 수 있는 것이지만 Windows 사용자에게는 필수 사항은 아니기 때문에 제외시키기로 했다.

![image](https://github.com/user-attachments/assets/8494282f-f82d-456c-a2e0-08f0ee634c9c)

따라서, GemFile에서 관련 명령어를 주석 처리하는 것으로 해결하였다. 

![image](https://github.com/user-attachments/assets/dd2866bf-4cc8-42f9-9b98-62aa95dce31c)

성공한 모습!