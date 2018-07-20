---
layout: post
title: Service Monitoring
author: 허재성
date:   2018-04-01 17:30:00 -0400
permalink: /service-monitoring
categories: projects
tags: Monitoring
excerpt: 사내 서비스 모니터링 시스템
github: 
external-website: 

image: /assets/img/project-images/monitoring.jpg
imageAlt: Service Monitoring
image-slider: /assets/img/project-images/slider-images/monitoring-slider.jpg
---

<div>
        <img src="http://bboroccu.github.io/assets/img/project-images/monitorstruct.png" width="80%"/>
</div>

## 요구사항
1. 서비스 서버 상태 체크(배송연동, 하마 등 공통 사항)
  - 서버 헬스 체크
  - 디스크 공간 체크
  - request 응답 속도 체크
  - 실행 로그 체크

2. 배송추적 싱크 체크
  - CallbackUrl 전송 실패 체크

3. 택배사 모니터링
- 택배사 응답속도 체크
- 택배사 타임아웃 체크

4. 기타처리
 - 택배 스캔 이상값 처리는 하마에서 별도 처리(별도 모니터링 필요 없음)