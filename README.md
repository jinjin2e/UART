# UART 데이터 패킷 및 통신
## UART


## 패킷
> 패깃 -
>
> 1개의 시작 비트
> 최대 9개의 데이터 비트
> 1개의 패리티 비트(옵션) 
> 1 또는 2개의 정지 비트 
> 가 기본 형태인데 쓰임새 별, 전송하고 싶은 정보의 양 , 암호화 종류 등등으로 변경될 수 있음
>
## 통신 순서
> 1. 패킷의 형태에 맞춰 전송하고 싶은 데이터를 가공 ( 암호화 전 데이터 )
> 2. 패킷의 checksum 계산 및 암호화 
> 3. 암호화한 데이터와  magic code, random값 등을 합친 암호화가 완료된 송신할 패깃 생성
> 4. 데이터 송신 
> 5. 데이터 수신 및 magic code 일치 확인 
> 6. checksum 계산 및 대조 
> 7. 동일하다면 오류 없이 전송완료.

##
