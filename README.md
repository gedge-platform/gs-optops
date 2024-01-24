# GS-OptOps
Machine Learning(ML) Platform at Edge-computing

# Overview
GS-OptOps는 kubernets 기반의 Gedge Platform에서 사용자 요구사항에 따라, on-demand로 ML/DL 실행 환경을 자동으로 구성해주는 오픈소스 프로그램 입니다.
특히, 호스트 설치 환경에 의존성 없이 개발자가 필요한 학습-추론 환경을 가상 클러스터로 자동 구성해주는 기능을 포함하고 있으며 해당 기능을 vk8s(virutal k8s)라 명명하고 있습니다.
본 코드에는 vk8s cluster상에서 kubeflow을 자동 설치해주는 프로그램을 포함하고 있어, 개발자가 학습-추론 실행 플로우를 실행할 수 있습니다. 
- KinK 구조 : 호스트 쿠버네티스가 여러 노드에 걸쳐서 떠 있고 그 위에 virtual 쿠버네티스들이 실행되는 kubernetes-in-kubernetes 구조입니다.
## Requirements
1. linux kernel version >= 5.4 (recommended)
1. 호스트 쿠버네티스 설치
1. 호스트 도커 storage-driver가 overlay2이어야 함  

## vk8s on centos
centos에 virtual kubernetes를 구성하기 전에 해야할 것들을 정리해놓은 문서입니다.  
  
### nvidia-driver 설치
(optional) 기존에 설치된 nvidia-driver 삭제

```bash
sudo ./NVIDIA-Linux-x86_64-460.32.03.run --uninstall
```

사용하는 gpu에 맞는 nvidia-driver 설치

```bash
wget https://kr.download.nvidia.com/tesla/470.82.01/NVIDIA-Linux-x86_64-470.82.01.run
sudo chmod +x NVIDIA-Linux-x86_64-470.82.01.run
sudo ./NVIDIA-Linux-x86_64-470.82.01.run
```

### 호스트 도커 storage-driver 확인
호스트 도커의 storage-driver가 overlay2를 사용하고 있는지 확인합니다.
```bash
sudo docker info | grep Storage

Storage Driver: overlay2
```
만약 overlay2가 아니라면 */etc/docker/daemon.json* 파일을 수정하여 overlay2로 바꿔줍니다.
```bash
sudo vi /etc/docker/daemon.json

{
    ...
    "storage-driver": "overlay2", # 추가
    ...
}

sudo systemctl restart docker
sudo docker info | grep Storage
```

## QuickStart
**Master Node: node1; Worker Node: node2**
### Virtual Kubernetes 구축하기
1. Vk8s Component 설치
    ```bash
    # ./install.sh <image-registry> <ip-reservation-range>
    ./install.sh 10.40.103.111:15001 192.168.100.0/24 
    ```
1. Vk8s 리소스 생성
    ```bash
    kubectl apply -f config/samples/vk8s-1-20.yaml
    ```
    `Vk8s` 리소스를 생성하면 yaml 스펙에 맞게 자동으로 `statefulset`과 `service`가 생성되고 `virtual kubernetes`가 구축되기 시작하며, 네트워크 상태에 따라 수십분이 걸릴 수도 있습니다. 진행사항을 확인하시려면 `vk8s`리소스의 status나 `vk8s-controller-manager`파드 로그를 확인하시면 됩니다.
1. Vk8s Component 삭제
    ```bash
    ./uninstall.sh
    ```
## 프로젝트
### vk8s controller
vk8s Custom Resource 스펙과 Custom Resource에 대한 로직을 직접 수정하실 수 있습니다.
### vnode
vnode(virtual node) Dockerfile, vk8s 스크립트 등 vnode 이미지 빌드와 관련된 파일이 있는 폴더 입니다. 
### ip manager
calico static ip 할당을 위해, ippool과 할당 가능한 ip 등의 관리를 담당하는 서비스입니다. 

## 학습-추론 예제 실행
Federated Learning 실행을 위해 tensorflow 예제가 구현되어 있습니다. 위에서 연합학습한 모델을 다운로드 받아서 한번 더 학습하는 예제입니다.
(https://github.com/gedge-platform/gs-optops/tree/master/vk8s/vk8s/federated-learning)
