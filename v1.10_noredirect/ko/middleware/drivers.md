# 드라이버 개발

NuttX 장치 드라이버는 [Device](https://github.com/PX4/Firmware/tree/master/src/lib/drivers/device) 프레이워크에 기초합니다.

Linux와 QuRT 드라이버는 [DriverFramework](https://github.com/px4/DriverFramework)에 기초합니다. PX4는 NuttX와 같은 드라이버를 사용할 수 있도록 업데이트하고 있습니다.

> **Note** (2017년 12월) 몇몇의 Linux/QuRT I2C 드라이버가 업데이트 되었습니다 (주로 풍속 센서들). 우리는 남은 드라이버들을 계속해서 업데이트할 계획입니다.

## 드라이버 만들기

PX4는 보통 데이터를 [uORB](../middleware/uorb.md)에서 데이터를 독점적으로 가져옵니다. 일반적인 주변장치 타입의 드라이버들은 반드시 정확한 uORB 메세지들을 퍼블리시 해야합니다 ( 예: 자이로센서, 가속계, 압력 센서 등).

새로운 드라이버를 만드는 최선의 방법은 템플릿을 통해 만드는 것입니다 ([src/drivers](https://github.com/PX4/Firmware/tree/master/src/drivers)를 참고하세요).

> **Tip** 특정 I/O 버스나 센서를 작업하기 위한 더 많은 정보들은 [Sensor and Actuator Buses](../sensor_bus/README.md) 섹션에서 얻을 수 있습니다.

<span></span>

> **Note** 정확한 uORB 토픽을 퍼블리시하는것이 드라이버가 해야할 *유일한* 것 입니다.

## 중요 아키텍쳐

PX4는 [reactive system](../concept/architecture.md)이며 데이터 Pub/Sub을 위해 [uORB](../middleware/uorb.md)을 사용합니다. 파일을 핸들링 하는 것은 시스템의 중요 작업을 필요로하거나 사용하지 않습니다. 2가지의 주된 API가 사용됩니다.

* Pub/Sub 시스템은 PX4가 실행되는 시스템에 의존하는 네트워크나 공유메모리 백엔드가 있습니다.
* 글로벌 장치 레지스트리를 통해 디바이스 목록과 그 설정을 get/set할 수 있습니다. 이것은 링크리스트처럼 간단하며, 파일시스템에 매핑할 수도 있습니다.

## 디바이스 ID

PX4는 시스템에 상관없이 각각의 센서를 구별하기 위해 디바이스 ID를 사용합니다. ID는 설정 파라미터에 저장되어 있으며 ID는 설정 파라미터에 저장되어 있으며 센서 교정값을 일치시키기 위해 사용되고, 어떤 센서가 어떤 로그파일에 기록되는지 확인하기 위해 사용됩니다.

센서의 순서 (예. `/dev/mag0`가 있고 `/dev/mag1/`이 있을때) 는 우선수위를 결정하지 않습니다. 이 우선순위는 대신에 퍼블리시된 uORB 토픽의 일부분으로 저장됩니다.

### 디코딩 예제

한 시스템에 3개의 마그넷미터에 대한 예를 위해, 파라미터 덤프를 위한 flight log (.px4log)를 사용합니다. 3개의 파라미터는 센서 ID와 주된 마그넷미터 센서를 구별하는 `MAG_PRIME`를 인코딩합니다. 각 MAGx_ID는 24비트의 넘버이고 직접적인 디코딩을 위해서는 0으로 패딩되어야 합니다.

    CAL_MAG0_ID = 73225.0
    CAL_MAG1_ID = 66826.0
    CAL_MAG2_ID = 263178.0
    CAL_MAG_PRIME = 73225.0
    

이것은 버스 1에 주소 `0x1E` 에 I2C를 통해 연결된 외부의 HMC5983 입니다. 이것은 `IMU.MagX`에 로그파일로 저장되어 보여질 것입니다.

    # device ID 73225 in 24-bit binary:
    00000001  00011110  00001 001
    
    # decodes to:
    HMC5883   0x1E    bus 1 I2C
    

이것은 버스 1에 SPI를 통해 연결된 slot 5를 선택하는 HMC5983 입니다. 이것은 `IMU1.MagX` 로그파일에 보여질 것입니다.

    # device ID 66826 in 24-bit binary:
    00000001  00000101  00001 010
    
    # decodes to:
    HMC5883   dev 5   bus 1 SPI
    

그리고 이것은 버스 1에 SPI를 통해 연결된 slot 4를 선택하는 내부의 MPU9250 입니다. `IMU2.MagX` 로그파일에 보여질 것입니다.

    # device ID 263178 in 24-bit binary:
    00000100  00000100  00001 010
    
    #decodes to:
    MPU9250   dev 4   bus 1 SPI
    

### 디바이스 ID 인코딩

디바이스 ID는 다음과 같은 형태의 24비트 숫자입니다. 위의 디코딩 예제에서 첫번째 필드는 LSB인것을 유의하세요.

```C
struct DeviceStructure {
  enum DeviceBusType bus_type : 3;
  uint8_t bus: 5;    // which instance of the bus type
  uint8_t address;   // address on the bus (eg. I2C address)
  uint8_t devtype;   // device class specific device type
};
```

`bus_type`은 다음과 같이 디코딩됩니다.

```C
enum DeviceBusType {
  DeviceBusType_UNKNOWN = 0,
  DeviceBusType_I2C     = 1,
  DeviceBusType_SPI     = 2,
  DeviceBusType_UAVCAN  = 3,
};
```

`devtype`는 다음과 같이 디코딩됩니다.

```C
#define DRV_MAG_DEVTYPE_HMC5883  0x01
#define DRV_MAG_DEVTYPE_LSM303D  0x02
#define DRV_MAG_DEVTYPE_ACCELSIM 0x03
#define DRV_MAG_DEVTYPE_MPU9250  0x04
#define DRV_ACC_DEVTYPE_LSM303D  0x11
#define DRV_ACC_DEVTYPE_BMA180   0x12
#define DRV_ACC_DEVTYPE_MPU6000  0x13
#define DRV_ACC_DEVTYPE_ACCELSIM 0x14
#define DRV_ACC_DEVTYPE_GYROSIM  0x15
#define DRV_ACC_DEVTYPE_MPU9250  0x16
#define DRV_GYR_DEVTYPE_MPU6000  0x21
#define DRV_GYR_DEVTYPE_L3GD20   0x22
#define DRV_GYR_DEVTYPE_GYROSIM  0x23
#define DRV_GYR_DEVTYPE_MPU9250  0x24
#define DRV_RNG_DEVTYPE_MB12XX   0x31
#define DRV_RNG_DEVTYPE_LL40LS   0x32
```