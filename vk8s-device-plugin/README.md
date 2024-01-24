# device-plugin은 host-device-plugin과 virtual-device-plugin으로 구성되어 있음
- 파드에서는 여전히 전체 gpu를 바라보고 있지만 가상 쿠버네티스에서 조회할 땐 할당된 gpu만 조회되어 다른 파드와 충돌할 일이 없어짐
