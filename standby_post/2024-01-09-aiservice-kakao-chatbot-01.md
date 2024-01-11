---
layout: single
title: "AI service - 카카오챗봇 01 (채널 개설, 챗봇 생성)"
categories: AI-service
tag: [aiservice, 카카오챗봇]
toc: true
toc_sticky: true
toc_label: "목차"
---
카카오톡 채널에서 인공지능 챗봇을 구현 하는 방법을 알아보겠습니다. 인공지능은 chatGPT의 API를 사용하겠습니다. 안타깝지만 **유료**입니다.

## 채널 개설

[홈 - 비즈니스 관리자센터](https://business.kakao.com/)에 접속하여 로그인을 합니다. [내비즈니스]를 클릭합니다. 2단계 인증이 필요하신 분은 [카카오계정 2단계 인증 설정하기]로 이동하여 설정을 완료합니다.

![비즈니스 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_21.png)

상단 메뉴에서 [채널] -> [채널]을 클릭합니다.

![비즈니스 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_22.png)

채널 관리자 센터로 이동하였습니다. [새 채널만들기]를 클릭합니다.

![비즈니스 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_23.png)

채널이름과 검색용 아이디를 입력하고 카테고리를 선택합니다. [확인]을 클릭합니다.

![비즈니스 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_24.png)

입력한 정보를 다시 확인하는 창이 나타납니다. [개설하겠습니다.]을 클릭합니다.

![비즈니스 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_25.png)

채널이 개설 되었습니다. [대시보드로 이동하기]를 클릭합니다.

![비즈니스 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_26.png)

비공개 개설 안내 창이 나타납니다. [확인]을 클릭합니다.

![비즈니스 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_27.png)

대시보드의 '프로필 설정'에서 [채널 공개], [검색 허용], [1:1채팅]을 'ON'으로 설정합니다. [저장]을 클릭합니다.

![비즈니스 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_28.png)

<!--
좌측 메뉴의 [친구 모으기] -> [채널 홍보]에서 채널 URL과 채팅 URL, 친구추가 URL 등을 확인 하실 수 있습니다.

![비즈니스 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_29.png)
-->

## 카카오 챗봇 생성

상단 메뉴에서 [채널] -> [챗봇] -> [봇만들기]를 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_31.png)

[카카오톡 챗봇]을 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_32.png)

챗봇의 이름('인공지능 챗봇')을 입력하고 [확인]을 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_33.png)

'인공지능 챗봇'이 생성되었습니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_34.png)

좌측 메뉴의 [설정]을 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_35.png)

[운영 채널 선택하기]를 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_36.png)

[저장]을 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_37.png)

배포 여부의 확인 창이 나타납니다. **[취소]**을 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_38.png)

카카오톡 챗봇이 생성되었습니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_39.png)

상단 메뉴에서 [채널] -> [챗봇]을 클릭합니다.

![챗봇 관리자센터]({{site.url}}/images/kakao-chatbot/kakao-chatbot-01_40.png)

내 챗봇의 목록에 조금 전 생성한 '인공지능 챗봇'이 나타납니다.

---

해시태그: #카카오 #카카오채널 #카카오챗봇 #ai-service
