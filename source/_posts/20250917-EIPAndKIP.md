---
title: EIP/KIP 요약
date: 2025-09-17 20:03:12
category:
- BlockChain
tags:
- EIP
- KIP
- Token
---

# EIP/KIP 요약

## 이더리움에서 제일 많이 쓰는 EIP

| 번호 | 이름 | 핵심 내용 | iOS 개발에서 활용 포인트 |
|------|------|-----------|--------------------------|
| **EIP-20** | ERC-20 Token Standard | 대체 가능한 토큰(FT) 표준 | `balanceOf`, `transfer`, `approve` 같은 함수 호출 → 토큰 잔액/전송 |
| **EIP-721** | ERC-721 NFT Standard | 대체 불가능 토큰(NFT) 표준 | `ownerOf`, `tokenURI` → NFT 뷰어, NFT 거래 앱 |
| **EIP-1155** | Multi Token Standard | FT + NFT 동시 지원 | 게임 아이템, 멀티자산 지갑 |
| **EIP-155** | Simple Replay Attack Protection | 트랜잭션에 `chainId` 포함 | iOS 앱에서 트랜잭션 서명 시 반드시 `chainId` 넣어야 안전 |
| **EIP-1559** | Fee Market Change | `maxFeePerGas`, `maxPriorityFeePerGas` → 새로운 수수료 체계 | 앱에서 가스비 UI 설계 시 필수 |
| **EIP-712** | Typed Structured Data Hashing and Signing | 사람이 읽을 수 있는 메시지 서명 | iOS에서 서명 UX 개선(피싱 방지) |
| **EIP-2612** | Permit (ERC-20 Approvals via Signatures) | 토큰 전송 승인 시 가스 없는 서명 허용 | DEX/DeFi 앱에서 “가스 없는 approve” 구현 가능 |

---

## 클레이튼(카이아, Klaytn → KAI) 기반에서 많이 쓰는 KIP

클레이튼은 EIP를 확장한 **KIP(Klaytn Improvement Proposal)** 체계를 사용합니다.  
주요 표준은 이더리움과 거의 비슷하지만, 클레이튼 환경에 맞게 수정된 버전!!

| 번호 | 이름 | 설명 | 비고 |
|------|------|------|------|
| **KIP-7** | Fungible Token Standard | ERC-20과 유사, 클레이튼 FT 표준 | 카이아 기반 토큰 |
| **KIP-17** | Non-Fungible Token Standard | ERC-721과 유사, NFT 표준 | NFT 서비스 |
| **KIP-37** | Multi Token Standard | ERC-1155과 유사, FT + NFT 혼합 | 게임/멀티자산 |
| **KIP-13** | Interface Detection | ERC-165와 동일 | 스마트컨트랙트 호환성 체크 |
| **KIP-17 Metadata** | NFT 메타데이터 확장 | ERC-721 메타데이터와 유사 | `tokenURI` 지원 |

---

## 정리
- **이더리움(EIP)**: ERC-20, ERC-721, ERC-1155, EIP-155, EIP-1559, EIP-712, EIP-2612 → 가장 자주 마주침.  
- **클레이튼/카이아(KIP)**: KIP-7(FT), KIP-17(NFT), KIP-37(MultiToken)이 주력.  

👉 결론:  
iOS 앱에서 **토큰 지갑, NFT 뷰어, 트랜잭션 전송**을 구현한다면,  
- **EIP-20/721/1559/712**  
- **KIP-7/17/37**  
이 조합이 가장 많이 쓰임.  
