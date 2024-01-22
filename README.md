# UART 데이터 패킷 및 통신
## UART
## ST function (interrupt)
> ```
> 수신부
> HAL_UART_Receive_IT( UART 핸들, 수신 버퍼의 포인터, 수신 데이터 크기 );      // UART 데이터 수신이 이루어졌을 때 인터럽트를 발생시키도록 설정하는 함수 / 위치는 main 함수 내부 while 시작 전 (user code 2) + HAL_UART_RxCpltCallback() 내부 
> 수신 버퍼의 타입 == unsigned chat*
> 수신 데이터 크기 == 설정한 크기만큼 데이터를 수신받았을 때 인터럽트를 발생 (byte)
> 설정한 크기만큼의 데이터 수신이 완료되면 HAL_UART_RxCpltCallback() 함수를 호출함 -> 이 함수 안에서 할 일을 작성
>
> 송신부
> HAL_StatusTypeDef HAL_UART_Transmit_IT(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size);
> huart: uart 인스턴스
> pData: 송신 데이터 버퍼
> Size: 송신 데이터 개수
> Uart 송신 인터럽트 함수: Uart Tx로 데이터를 Size만큼 전송하면 인터럽트가 발생한다
> ```
> HAL_UART_RxCpltCallback() 에서 해야할 일 
>
> 1. 수신 버퍼의 데이터를 다른 버퍼에 저장. / 수신 버퍼는 수신시에만 사용하는 버퍼이므로 데이터를 어느 정도 쌓아두고 처리할 때는 별도의 버퍼 사용하는 것이 좋음.
> 2. HAL_UART_Receive_IT 호출 / user code 2 에서 설정한 HAL_UART_Receive_IT()는 1회용임 -> 첫 바이트를 수신하면 해당 설정을 비활성화함 그렇기 때문에 다시 수신 인터럽트 설정을 해줘야 함.
> ```C
> 예제)
>
> void HAL_UART_RxCpltCallback(UART_HandleTypeDef* uartHandle)
> {
>     rx_buff[buff_count] = rx_buff_temp[0];
>     buff_count += 1;
>
>     if (buff_count == 256)
>     {
>           buff_count = 0;
>     }
>
>     HAL_UART_Receive_IT(&huart3, rx_buff_temp, RX_BUFF_SIZE);
> }
위 코드에서 rx_buff_temp는 크기가 1Byte인 수신 버퍼이며, rx_buff는 크기가 256Byte인 데이터 처리용 버퍼임.
그리고 buff_count는 데이터 처리용 버퍼의 현재 인덱스 값을 의미함. 데이터를 계속 수신하다가 인덱스 값이 256이 되면 다시 0으로 바꾸어 순환하도록 함.
마지막 줄에는 UART 수신 인터럽트 활성 설정을 하는 함수를 사용.
 



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
