
# subject

> ./ircserv \[port] \[password]

사용 가능한 함수는 서브젝트 참조

- 단일 서버
- 여러 클라이언트 동시 처리 (비동기 프로그래밍, I/O 차단 금지)
- write, read, listen 등은 하나의 poll계열 함수로만 처리해야함

- 하나의 irc 클라이언트를 레퍼런스로 선택하고 개발해야함
- 클라이언트 - 서버 통신은 TCP/IP를 통해 이루어져야함
	- socket()에서 설정 가능한 것으로 보임
- 공식 IRC 서버에서 사용하는 것과 유사해야하지만 필수 기능은 다음과 같음
	- 레퍼런스 클라이언트를 사용
		- 인증 (authenticate)
		- nickname 설정
		- username 설정
		- join a channel
		- send and receive private messages
	- 임의의 클라이언트가 채널에 전한 메세지는 모든 다른 클라이언트에게 전송되어야함 (그게 가능해야 채팅이겠지?)
	- 관리자와 일반 사용자가 존재해야함
		- KICK
		- INVITE
		- TOPIC
		- MODE
			- i: 초대 전용 채널 설정/제거  
			- t: TOPIC 명령의 제한을 채널 오퍼레이터에게 설정/제거합니다  
			- k: 채널키 설정/제거(비밀번호)   
			- o: 채널 운영자 권한 부여/받기  
			- l: 사용자 제한을 채널로 설정/제거  

> 이 외의 디테일은 RFC 문서를 참고하여 추가하는 방식으로 해야함
> bigshell을 만들 수 있어도 만들 필요는 없는 것 처럼 필수요소로만 구성해도 됨


# RFC 1459
## 1. INTRODUCTION

- 클라이언트는 최대길이가 9자인 고유한 닉네임으로 다른 클라이언트와 구별됨
- 특정 클라이언트가 유지관리 가능한 권한을 가진 운영자(오퍼레이터)가 됨 (SQUIT, CONNECT 참조)
- 오퍼레이터는 강제로 특정 사용자를 제거할 수 있음 (KILL)
- 채널을 첫번째 클라이언트가 채널에 가입할 때 암시적으로 생성되며 마지막 클라이언트가 떠날 때 존재하지 않게 됨
- 채널이 존재하는동안 채절의 이름을 통해 모든 클라이언트가 참조 가능
- 채널 이름은 최대 200자길이의 문자열이며 & 혹은 \#으로 시작해야하고 공백, 컨트롤 G, ','를 포함할 수 없음
- Oikarinen & Reed (이게 뭔지 모르겠는데 그냥 있다고 함)
- 클라이언트는 새 채널을 만들거나 가입할 수 있음
- 클라이언트의 최대 가입 채널을 설정하는 것이 좋다고 함
- 채널이 초대 전용일땐 초대받았을때만 가능
- 채널 오퍼레이터는 별명 옆의 @기호로 식별됨 (NAMES WHO WHOIS 모두 응답)
- 채널 오퍼레이터(chop, chanop)만 사용 가능한 명령으로 KICK, MODE, INVITE가 있음

- IRC 메시지는 접두사(옵션), 명령 및 명령 매개변수(최대 15개)로 이루어짐
- 하나의 ASCII 공백문자 (0x20) 으로 나누어짐
- 접두사의 존재는 메시지 자체의 첫 번째 문자여야하는 ':' (0x3b)로 표현됨 (콜론과 접두사 사이에 공백이 없어야함)
- 

\<message>  ::= \[':' \<prefix> \<SPACE> ] \<command> \<params> \<crlf>
\<prefix>   ::= \<servername> | \<nick> \[ '!' \<user> ] \[ '@' \<host> ]
\<command>  ::= \<letter> { \<letter> } | \<number> \<number> \<number>
\<SPACE>    ::= ' ' { ' ' }
\<params>   ::= \<SPACE> \[ ':' \<trailing> | \<middle> \<params> ]

\<middle>   ::= \<\Any \*non-empty* sequence of octets not including SPACE or NUL or CR or LF, the first of which may not be ':'>
\<trailing> ::= \<\Any, possibly *empty*, sequence of octets not including NUL or CR or LF>

\<crlf>     ::= CR LF

NOTES:

  1)    \<SPACE> is consists only of SPACE character(s) (0x20). Specially notice that TABULATION, and all other control characters are considered NON-WHITE-SPACE.

  2)    After extracting the parameter list, all parameters are equal, whether matched by \<middle> or \<trailing>. \<Trailing> is just a syntactic trick to allow SPACE within parameter.

  3)    The fact that CR and LF cannot appear in parameter strings is just artifact of the message framing. This might change later.

  4)    The NUL character is not special in message framing, and basically could end up inside a parameter, but as it would cause extra complexities in normal C string handling. Therefore NUL is not allowed within messages.

  5)    The last parameter may be an empty string.

  6)    Use of the extended prefix (\['!' \<user> ] \['@' \<host> ]) must not be used in server to server communications and is only intended for server to client messages in order to provide clients with more useful information about who a message is from without the need for additional queries.

  
   Most protocol messages specify additional semantics and syntax for
   the extracted parameter strings dictated by their position in the
   list.  For example, many server commands will assume that the first
   parameter after the command is the list of targets, which can be
   described with:

   \<target>     ::= \<to> \[ "," \<target> ]
   \<to>         ::= \<channel> | \<user> '@' \<servername> | \<nick> | \<mask>
   \<channel>    ::= ('#' | '&') \<chstring>
   \<servername> ::= \<host>
   \<host>       ::= see [RFC 952](https://datatracker.ietf.org/doc/html/rfc952) \[DNS:4] for details on allowed hostnames
   \<nick>       ::= \<letter> { \<letter> | \<number> | \<special> }
   \<mask>       ::= ('#' | '$') \<chstring>
   \<chstring>   ::= \<\any 8bit code except SPACE, BELL, NUL, CR, LF and comma (',')>

   Other parameter syntaxes are:

   \<user>       ::= \<nonwhite> { \<nonwhite> }
   \<letter>     ::= 'a' ... 'z' | 'A' ... 'Z'
   \<number>     ::= '0' ... '9'
   \<special>    ::= '-' | '[' | ']' | '\' | '`' | '^' | '{' | '}'
   \<nonwhite>   ::= \<\any 8bit code except SPACE (0x20), NUL (0x0), CR (0xd), and LF (0xa)>


## 2. THE IRC SPECIFICATION

2.4 숫자응답  
  
서버로 전송되는 대부분의 메시지는 일종의 응답을 생성합니다. 가장 일반적인 응답은 숫자 응답으로, 오류 응답과 일반 응답 모두에 사용됩니다. 숫자 응답은 반드시 발신인 접두사, 세 자리 숫자 및 응답 대상으로 구성된 하나의 메시지로 전송되어야 합니다. 숫자 응답은 클라이언트로부터 전송되는 것이 허용되지 않으며, 서버에 수신된 이러한 메시지는 자동으로 삭제됩니다. 다른 모든 점에서 숫자 응답은 일반 메시지와 같지만, 키워드가 문자열이 아닌 세 자리 숫자로 구성됩니다. 다른 응답 목록은 6절에 제공됩니다.

## 3. IRC Concepts

## 4. MESSAGE DETAILS

## 5. OPTIONAL MESSAGES

## 6. REPLIES

## 7. Client and server authentication

## 8. Current Implementations Details

## 9. 문제점
- 단일 서버의 특성상 닉네임 혹은 채널명의 중복이 문제가 될 수 있음
- 기존에 존재하는 하나만 성공하거나 둘 다 KILL이 되도록 해보자