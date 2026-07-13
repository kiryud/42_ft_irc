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

## 소개

단일 irc 서버를 구현하는 과제이므로 서버간의 통신을 고려하지 않음

- 구현해야하는 명령어
	- `PASS` `NICK` `USER` `PING` `PONG` `LIST` `WHO` `JOIN` `PART` `PRIVMSG` `QUIT` `INVITE` `KICK` `TOPIC` `MODE {{+-}olkit}`

- 서버는 포트(필수)와 비밀번호(optional)을 입력받음


## 제작 기록
담당한 영역 : irc 로직 분석을 통한 컨트롤러 구조 정의, 자료구조 정의, QA  

start : 2024.01.12.(금)

RFC문서 및 irc protocol 분석 : 2024.01.13 ~ 01.16
	- RFC문서(RFC 1459, 2810, 2811, 2812)
에 RPL, ERR 메세지 규약이 제대로 없는 경우가 많음
		- 여러가지 RFC 문서 중 해당 부분을 발췌독
		- IRCprotocol의 RPL, ERR메세지를 기반으로 검색해 IRCv3같은것도 찾아냄
	- 확실한 검증을 위해 상용 irc서버와 irssi의 통신을 tcp 통신을 열람해서 확인
	
[남아있는 분석 기록](#분석)


1차 제작 : 2024.01.18 ~ 01.24

- 접속시 user nick pass의 세 명령어의 우선순위, 동작방식, 조건 등에 대해 확인해 조건과 대기시간을 파악함
- nc를 통해 eof를 통해 전송 가능한 미완성의 명령줄에 대해 test

서버와 irc로직을 분리하기 위한 각 객체의 책임과 역할 정리 : 2024.01.25 ~ 01.29

서버와 irc로직을 분리하기로 결정 : 2024.01.25
	- 서버 1명, irc 프로토콜 2명의 구성에서 서로의 개발 편의와 메모리 관리 등을 위하여 서버 객체와 irc 객체를 나누어 작성하기로 함
		- 서버에서 진행하는 kqueue의 구조를 배워 어떤 event처리가 가능한지 배움
		- 해당 event에서 진행해야하는 irc객체의 동작과 그 때 받는 정보와 반환할 정보를 정리함

[메세지 기록 (초안 제시한 내용)](#분리)

이후 정리된 문서와 자료를 토대로 구현 테스트 구현의 반복

- (2월 9일) vector의 문제점 발견

vector의 재할당이 있더라도 기존 데이터의 주소까지는 변경되지 않을것이라 잘못 추측함. 실제로는 새로운 영역을 통째로 재할당하고 채워넣는 과정에서 주소가 뒤바뀜.

따라서 재할당 위험이 없는 list를 사용하기로 함

- 각 명령어에서 존재할 수 있는 오류에 대해 testcase를 만들어 공유하며 RFC에 없는 영역이라 비교하기 쉽게 하기 위해 상용 서버의 명령어 처리 방식, 순서 등을 참고하여 검증 작업의 순서 정의


finish (115) : 2024.02.13.(화)

서버 내장 `manner_bot` 완성

finish (125) : 2024.02.21.(수)




### 분석

<img width="1243" height="1670" alt="스크린샷 2026-07-13 오후 2 20 26" src="https://github.com/user-attachments/assets/55737746-f7c6-43bc-8cc2-93c81042a78f" />

<img width="1242" height="766" alt="스크린샷 2026-07-13 오후 2 21 24" src="https://github.com/user-attachments/assets/03853673-3cc0-4928-b17f-baaa785ffd14" />

<img width="1289" height="1334" alt="스크린샷 2026-07-13 오후 2 21 54" src="https://github.com/user-attachments/assets/93cbbd2e-922b-4476-9134-6cb2ebab8c6f" />

<img width="1523" height="497" alt="스크린샷 2026-07-13 오후 2 22 24" src="https://github.com/user-attachments/assets/4cf8b1a1-1f84-42a8-ab01-09fd7cdbc9c0" />

### 분리
<img width="1442" height="1858" alt="스크린샷 2026-07-13 오후 2 10 21" src="https://github.com/user-attachments/assets/890f07a8-e994-40ed-859b-a0d6c194d825" />
