---
title: "Project: iamport를 활용해서 만든 프로젝트 결제 과정"
date: 2022-12-04T21:30:35+09:00
draft: t
summary: 
tags: ["iamport"]
categories: ["Project"]
---
결제 프로세스 완료의 의미

결제 프로세스 완료는 아래의 모든 경우를 포함합니다.
결제 성공(결제 상태: paid, imp_success: true)
결제 실패(결제 상태: failed, imp_success: false)
PG 모듈 설정이 올바르지 않아, 결제 창이 열리지 않음
사용자가 임의로 X 버튼이나 취소 버튼을 눌러 결제를 종료함
카드 정보 불일치, 한도 초과, 잔액 부족 등의 사유로 결제가 중단됨
가상계좌 발급 완료(결제 상태: ready, imp_success: true)




https://skyseven73.tistory.com/17


IMP.init('아임포트 가입 유저가 갖는 가맹점 번호')


결제 버튼 클릭 -> call-payment.js 실행 -> passPaymentInfo 호출(view class에서 반환하는 merchant_id를 반환) -> PaymentPassView와 연결된 api 로 request 보냄 ->  PaymentPassView가 merchant_id를 만들어서 반환 -> passPaymentInfo function이 merchant_id를 받아서