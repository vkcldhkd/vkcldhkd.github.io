---
title: 블록체인 용어정리
date: 2022-06-27 13:52:06
category:
 - BlockChain
tags: 
- BlockChain
---

블록체인

- 데이터가 담긴 블록을 체인처럼 연결하는 분산된 데이터 처리기술을 뜻한다.
중앙화된 서버 없이 거래 처리 가능하다
- P2P방식으로 거래 장부를 공유한다.
- 이전 해시값과 현재 블록의 해시값으로 이어지는 링크드리스트의 구조를 지닌다


코인
- 메인넷이나 프로토콜을 보유하고있는 프로젝트의 화폐

토큰
- 특정 메인넷이나 프로토콜을 활용하여 발행한 화폐

메인넷 / 자체 프로토콜
- 특정 플랫폼에 종속되지 않고 독자적으로 운영되는 블록체인 네트워크
- 클레이튼은 kip-7

Erc20
- 이더리움 토큰 구현 표준 중에 하나이며 전 세계 표준처럼 사용되고있다

Erc721
- 이더리움 토큰 구현 표중 중에 하나로, 대체 불가능한 토큰(NFT)의 대표적인 기술 표준이다.

백서(white paper)
- 스타트업으로 따지면 사업계획서와 같은 것
- 어떤 블록체인 기술을 활용해서 어떤 제품을 런칭하여 해결한것인지에 대한 정보가 담겨있다.

핫월렛
- 인터넷에 연결된 월렛
- 개인키를 온라인에 연결해서 입력하기때문에 해킹 등 보안문제에 취약함

콜드월렛
- 인터넷에 연결되지않은 월렛
- 실시간 거래가 불가능하지만 개인키를 오프라인에서 처리해서 보내기 때문에 보안측면에서 조금 더 안전하다

해시 함수
- 어떠한 길이의 데이터를 입력받아도 고정된 길이의 데이터로 매핑해주는 단방향 함수
- 단방향 함수의 특성상, 입력값을 통해 해시값을 알아낼 수 있으나 해시값을 통해 입력값을 알아낼 수 없다
- 블록체인에서는 sha256 해시함수를 주로 사용한다.

pow(작업증명방식(proof of work))
- 가능한 모든 논스값을 대입하고 해쉬값을 대조하는 방식으로 이루어짐
- 작업 증명에서 논스를 알아내고 새로운 블록 생성결과를 다른 노드들에게 전파하려면 많은 해시레이트가 필요한데 이에 대한 보상을 주는 행위를 채굴이라고 한다.

pos(지분 증명 박식(proof of stake))
-  지분을 가장 많이 가지고있는 노드들에게 블록 생성 권한이 주어진다.
- 일정 가상화폐를 예치해두고 이자를 받는 느낌

DPos(위임된 지분증명방식)
- 일정이상 지분을 가진 소수 대표 노드들에 의해 거래를 확정짓는 방식

IFO
- 어떠한 경우건 무료로 토큰/코인을 주는것
- 주로 메이저코인으로 하드포크 한 경우 에어드롭으로 제공하는 경우가 많음

컨트랙트
- 솔리디티에서 사용되는 코드 집합들의 상위개념
- 상태변수와 이를 수정할 수 있는 함수에 대한 데이터를 포함한 것을 지칭한다
- 계약이라고함
- 비트코인에는 스마트 컨트렉트가 없어서 NFT를 발행할 수 없다.

DEX(탈중앙거래소)
- KYC 불필요
- 거래 상대방 위험 없음


WalletConnect
- P2E 서비스를 이용할때 사용하는 중간 브릿지 역활

HD Wallet
- Hierarchicalk Deterministic의 약자로 시드 값만 가지고 있으면 여러개의 계정을 쉽게 생성할 수 있는 방법 제공
- 크게 두가지가 필요함. 시드와 계정까지의 경로
    - 시드는 단어집합
    - 경로는 여러개의 정수로 구성되며 개수에 제한 없음
- HD Wallet이 있으면, ChainID를 가지고 여러 코인의 주소를 만들 수 있다
