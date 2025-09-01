---
date: 2025-01-15
tags:
  - WebSocket
  - WebRTC
title: Real Man 채팅관련 코드 정리
index:
---
# 백엔드

## ChatController (컨트롤러)

### 동기 처리(페이지 이동)
#### chatting()
사용자가 채팅방 접속할때 호출.
- **경로**: `GET /chat/main/{serverNo}/{channelNo}`
- **과정** : 
	1. 로그인 회원 정보 확인
	2. model에 다음과 같은 데이터를 저장
		- 서버 목록 / 채널 목록/ 서버 멤버 목록/ 서버 이름 / 관리자 여부 / 채팅 메시지들 / 사용자 프로필 이미지
	3. 채널 타입에 맞는 페이지로 이동(뷰 반환)
#### selectSmallestChatNo()
비동기 방식이지만 페이지 이동에 사용됨.
사용자가 특정 채널을 클릭하지 않고 서버를 클릭했을때 호출.
- **경로**: `POST /chat/selectSmallestChatNo`
- **과정**:
	1. 클라이언트에서 전송한 서버넘버에서 가장 작은 채널넘버를 가져온다.`sService.selectChannelNo(serverNo)`
	2. 반환.
클라이언트는 반환받은 채널넘버로 다시 위의 `Chatting()`으로 재요청을 보내는 방식으로 작동.
### 채팅 메시지 전송
#### sendMessage()
채팅 메시지 DB저장, WebSocket 발행
- **경로**: `@MessageMapping("/chat/{channelNo}/{separetor}")` 
	- 클라이언트쪽에서는 `pub/chat/{}/{}` 주소로 전송.
- **과정**:
    1. 세션에서 로그인 멤버 정보 조회
    2. 메시지 객체에 다음의 정보 저장
	    - 사용자 프로필 사진, 채널넘버, 구분자(separator : 문자채팅 or 화상채팅)
    3. Firebase에 메시지 저장 `cService.insertChat(message)`
    4. WebSocket 발행 `messagingTemplate.convertAndSend(주제(경로), 메시지)`
### Voice 채팅 관련 
#### joinVoiceChannel()
음성 채팅방 입장 처리
- **경로**: `@MessageMapping("/chat/joinVoice")`
	- 클라이언트 쪽에서는 `pub/chat/joinVoice`주소로 전송
- **과정**: 사용자를 음성 채널 맵에 추가하고 전체 목록 반환.
####  joinVoiceChannel() **(위의 메소드와 다름)**
현재 음성 채팅 사용자 목록 조회
- **경로** : `GET /chat/api/voiceUsers` 
- **과정** :  `userInChannel`(채널 참가자 맵)객체 단순 반환.
#### leaveVoiceChannel()
음성 채팅방 퇴장 처리
- **경로**: `@MessageMapping("/chat/leaveVoice")`
- **과정**: 사용자를 `userInChannel`(채널 참가자 맵)에서 제거.
### WebRTC 관련 메소드들 (영상 통화)
#### PeerHandleOffer()
(사용 안됨)
WebRTC Offer 정보 중계
- **경로**: `@MessageMapping("/peer/offer/{camKey}/{roomId}")`
#### PeerHandleIceCandidate()
ICE Candidate 정보 중계 및 비디오 참가자 관리
- **경로** : `@MessageMapping("/peer/iceCandidate/{camKey}/{roomId}")`
- **과정** : 
	1. `videoInChannel`(채널 참가자 맵) 에서 클라이언트가 전송한 camKey가 있는지 확인하고 이미 있으면 제거. (중복 참가 방지)
	2. `videoInChannel.computeIfAbsent()`로  사용자를 맵에 삽입한다. (`computeIfAbsent()`메소드는 스레드 안정성을 위해서 사용된다.)
#### PeerHandleAnswer()
(사용 안됨)
WebRTC Answer 정보 중계
- **경로**: `@MessageMapping("/peer/answer/{camKey}/{roomId}")`
#### callKey(), sendKey()
영상통화용 키 교환
- **경로**: `@MessageMapping("/call/key")`, `@MessageMapping("/send/key")`

> #### ICE 후보?
> ICE Candidate는 WebRTC를 사용해 화상통화를 진행할 때  필요한 정보들이다.
> 각 클라이언트에서 화상통화에 필요한 여러 정보들을 서버로 보내면 서버에서 중계 로직에 따라 최적의 연결 방법을 각 클라이언트에 알려준다.
> (현재 프로젝트에는 로직이 구현되지 않고, 단순히 각 클라이언트에 전달만)
> #### 키?
>  Key는 접속된 사용자들의 식별자(이름) 역할.
>  참가자 김,이,박이 화상통화에 접속하면 각각 키 A,B,C를 부여받는다.
>  그리고 이 키를 사용해 각 클라이언트끼리 비디오 스트림을 주고받는다. 
## ChatService (서비스)
### 채널 관련 
#### chattingSidebar()
특정 서버의 채널 목록 조회
- **연결**: `ChatController.chatting()` → DB에서 채널 정보 조회
#### selectChannel()
특정 채널 정보 조회 (타입 확인용)
- **연결**: `ChatController.chatting()` → 채널이 Voice인지 Chat인지 판별
### Firebase 채팅 메시지 관련
#### insertChat()
채팅 메시지를 Firebase Firestore에 저장
- **연결**: `ChatController.sendMessage()` → Firebase "RealMan01" 컬렉션에 저장
- **저장 데이터**: 메시지 내용, 닉네임, 생성시간, 채널번호, 구분자
#### selectChatList()
채팅 메시지 목록을 Firebase에서 조회
- **연결**: `ChatController.chatting()` → Firebase에서 특정 채널의 메시지 조회
- **과정**:
    1. Firebase에서 채널별 메시지 조회 (생성일 역순)
    2. 시간 포맷 변환 (24시간제/12시간제)
    3. ChatMessage 객체로 변환하여 반환.
### DM(Direct Message) 관련
#### createDM()
DM 채팅방 생성
#### insertDM()
DM 메시지를 Firebase에 저장
- **과정**: "RealMan01" 컬렉션에 DM 정보 저장
#### selectDmList()
특정 회원의 DM 목록 조회
- **연결**: DB에서 DM 목록 조회
#### selectDm()
특정 DM 채팅의 메시지 목록 조회
- **과정**: Firebase에서 DM 메시지 조회 및 시간 포맷 변환
#### selectDmUseNickname(), findDMByMembers()
닉네임/멤버 정보를 통한 DM 조회