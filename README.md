# ft_irc
```md
project : ft_irc
Circle : 5
Language : cpp
Type : group(3)
start : 2024.01.12.(금)
finish (115) : 2024.02.13.(화)
finish (125) : 2024.02.21.(수)
introduce : irc protocol을 기반으로 작동하는 단일 서버 제작 (상용 클라이언트와 통신 가능)
```

<img width="1920" height="1200" alt="스크린샷 2024-02-14 오후 8 10 34" src="https://github.com/user-attachments/assets/c013cb9b-ae90-4df1-bf7d-bdf0c156a3c2" />

## 제작 기록
(팀장분 노션으로 자료 모으고 정리했는데 노션 닫아두셔서 열람 불가)

start : 2024.01.12.(금)

RFC문서 및 irc protocol 분석 : 2024.01.13 ~ 01.16
	- RFC문서(RFC 1459, 2810, 2811, 2812)
에 RPL, ERR 메세지 규약이 제대로 없는 경우가 많음
		- 여러가지 RFC 문서 중 해당 부분을 발췌독
		- IRCprotocol의 RPL, ERR메세지를 기반으로 검색해 IRCv3같은것도 찾아냄
	- 확실한 검증을 위해 상용 irc서버와 irssi의 통신을 tcp 통신을 열람해서 확인

1차 제작 : 2024.01.18 ~ 01.24

서버와 irc로직을 분리하기로 결정 : 2024.01.25
	- 서버 1명, irc 프로토콜 2명의 구성에서 서로의 개발 편의와 메모리 관리 등을 위하여 서버 객체와 irc 객체를 나누어 작성하기로 함
		- 서버에서 진행하는 kqueue의 구조를 배워 어떤 event처리가 가능한지 배움
		- 해당 event에서 진행해야하는 irc객체의 동작과 그 때 받는 정보와 반환할 정보를 정리함

[메세지 기록](###서버)
		
서버와 irc로직을 분리하기 위한 각 객체의 책임과 역할 정리 : 2024.01.25 ~ 01.29

이후 정리된 문서와 자료를 토대로 구현 테스트 구현의 반복

finish (115) : 2024.02.13.(화)

서버 내장 `manner_bot` 완성

finish (125) : 2024.02.21.(수)





### 서버, 컨트롤러 객체 분리 초안 메세지 기록
<img width="1442" height="1858" alt="스크린샷 2026-07-13 오후 2 10 21" src="https://github.com/user-attachments/assets/890f07a8-e994-40ed-859b-a0d6c194d825" />
