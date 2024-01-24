# device-plugin은 host-device-plugin과 virtual-device-plugin으로 구성되어 있음
- description 
 - 장치 플러그인이 가상 쿠버네티스에게 사용가능한 gpu를 알려주는 로직을 수정했습니다. 현재 파드에 할당 예정인 gpu uuid가 /gpu.env 파일에 저장되어 있으므로, 해당 파일을 읽고 장치 플러그인에서 gpu 조회할 때 파일에 적혀있는 uuid만 조회하고 반환하도록 했습니다. 이렇게 하면 파드에서는 여전히 전체 gpu를 바라보고 있지만 가상 쿠버네티스에서 조회할 땐 할당된 gpu만 조회되어 다른 파드와 충돌할 일이 없어집니다.
