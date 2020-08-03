# 리눅스에 리모콘 수신기 연결 (S.Bus 포함)

이 절에서는 [지원 리모콘 수신기](https://docs.px4.io/master/en/getting_started/rc_transmitter_receiver.html)를 임의의 직렬 포트에 연결하고 사용할 목적으로 리눅스 기반 오토파일럿을 설정하는 방법을 알려드립니다.

S.Bus에 비해 원격 조종 타입은 수신기를 직렬 포트에 연결하거나 USB to TTY 직렬 케이블(PL2302 USB to Serial TTL 변환기)로 USB에 연결할 수 있습니다.

> **Note** S.Bus 수신기(또는 후타바, 래디오링크 등의 인코더)를 사용하는 목적이라면 보통 [신호 반전 회로](#signal_inverter_circuit)를 거쳐 수신기와 장치를 연결해야겠지만, 이외의 경우 설정은 동일합니다.

이후, 장치에서 다음과 같이 [PX4 원격 조정 드라이버를 시작](#start_driver)하십시오.

## 드라이버 시작 {#start_driver}

원격 조종 드라이버를 각 UART 에서 시작하려면 (예: `/dev/ttS2`):

    rc_input start -d /dev/ttyS2
    

다른 드라이버 사용법을 보려면 [rc_input](../middleware/modules_driver.md#rcinput)을 참고하십시오.

## 신호 반전 회로(S.Bus 전용) {#signal_inverter_circuit}

S.Bus에서는 *반전* UART 통신 신호를 주고 받습니다.

일부 직렬 포트/비행 조종 유닛에서는 반전 UART 신호를 읽을 수 있으나 대부분 반전 신호를 복원하기 위해 신호 반전 회로가 필요합니다.

> **Tip** 이 회로는 직렬 포트 또는 USB-to-TTY 직렬 변환기에서 S.Bus 원격 제어 신호를 읽는데 필요합니다.

이 장에서는 적절한 회로를 만드는 방법을 알아봅니다.

### 필요한 소자

* 1x NPN 트랜지스터 (예: NPN S9014 TO92)
* 1x 10K 저항
* 1x 1K 저항

> **Note** 전류 드레인 값이 상당히 낮으므로 트랜지스터를 어떤 형식/모델로 써도 상관 없습니다.

### 회로 구성도/연결

아래에 설명(그리고 회로 구성도)하는 바와 같이 회로 소자를 연결하십시오:

* S.Bus 신호선 &rarr; 1K 저항 &rarr; NPN 트랜지스터 베이스
* NPN 트랜지스터 에밋 &rarr; GND
* 3.3VCC &rarr; 10K 저항 &rarr; NPN 트랜지스터 컬렉션 &rarr; USB-to-TTY rxd
* 5.0VCC &rarr; S.Bus VCC
* GND &rarr; S.Bus GND

![Signal inverter circuit diagram](../../assets/driver_sbus_signal_inverter_circuit_diagram.png)

The image below shows the connections on a breadboard.

![Signal inverter breadboard](../../assets/driver_sbus_signal_inverter_breadboard.png)