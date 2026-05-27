## 1. 과제 개요 및 목표

- **과제명:** HW. 5 Getting Started with Home Assistant on Raspberry Pi
- **목표:** 라즈베리 파이에 Home Assistant(HA)를 설치 및 구동하고, LED 등 GPIO 핀을 제어하여 홈 오토메이션 기초 환경을 구축
- **테스트 환경:** Raspberry Pi 5, Home Assistant 최신 버전 (HAOS 기반)

## 2. 실습 진행 결과 요약

본 과제의 전반부인 **'Home Assistant OS 설치 및 초기 네트워크 접속'은 무사히 완료**하였으나, 실습의 최종 목표인 **'Home Assistant를 통한 라즈베리 파이 GPIO(LED) 직접 제어' 단계에서 치명적인 시스템 호환성 오류가 발생하여 최종 연동에 실패**하였습니다.

## 3. 단계별 수행 내역 및 실패 원인 분석 (Root Cause Analysis)

### 3.1. [성공] OS 설치 및 시스템 초기화

- **수행 내역:** Raspberry Pi Imager를 사용하여 HAOS 플래싱 완료 후 부팅. 초기 네트워크 세팅 및 다운로드 대기(약 20분) 후 `http://homeassistant.local:8123` 접속 성공.
- **결과:** 초기 관리자 계정 생성 및 메인 대시보드 진입까지 정상적으로 구동됨.

### 3.2. [실패] 라즈베리 파이 5 칩셋 변경 및 HA 연동(Integration) 충돌

초기 세팅 완료 후, 과제 가이드에 따라 라즈베리 파이 본체의 GPIO 핀에 LED를 연결하고 제어를 시도하였으나 실패하였습니다.

- **수행 내역:** Home Assistant 내부의 `configuration.yaml` 파일에 기존 방식대로 GPIO LED 제어 코드를 추가하고 시스템을 재시작함.
- **발생 오류:** 시스템 로그에 아래와 같은 에러가 출력되며 LED 엔티티가 생성되지 않음.
    
    > `Integration 'rpi_gpio' not found.`
    > 
- **객관적 실패 원인 분석:**
    1. **Raspberry Pi 5 하드웨어 아키텍처의 물리적 변경:** 라즈베리 파이 5부터는 기존 모델들과 달리 외부 장치 제어를 전담하는 **'RP1 I/O 컨트롤러(Southbridge)'** 칩셋이 새로 도입되었습니다. 이로 인해 과거 라즈베리 파이 3나 4에서 사용되던 구형 GPIO 매핑 주소 및 제어 라이브러리(예: `RPi.GPIO`)가 물리적으로 더 이상 호환되지 않습니다.
    2. **Home Assistant 소프트웨어 지원 중단 (Deprecation):** 최신 버전의 Home Assistant에서는 유지보수 한계 및 최신 리눅스 커널과의 충돌 문제로 인해, 기존에 기본으로 제공하던 `rpi_gpio` integration 자체가 코어 모듈에서 완전히 제거(삭제)되었습니다. 기본 모듈이 존재하지 않으므로 yaml 파일에 설정을 기입해도 시스템이 이를 인식하지 못합니다.

## 4. 결론

### 결론

현재 제공된 교수님의 강의 가이드(과거 라즈베리 파이 3/4 및 구형 HA 버전 기준)는 최신형 **Raspberry Pi 5 및 최신 Home Assistant 시스템 환경**과 아키텍처 단위에서 충돌합니다. 따라서 가이드를 그대로 따라가는 기존의 다이렉트 GPIO 접근 방식으로는 5번 과제(LED 제어)를 성공적으로 수행할 수 없음을 객관적으로 확인하였습니다.
