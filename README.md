# secret-watcher 설치 가이드

## 구성 요소
* secret-watcher 
	* image: [https://hub.docker.com/r/tmaxcloudck/hypercloud4-secret-watcher/tags](https://hub.docker.com/r/tmaxcloudck/hypercloud4-secret-watcher/tags)
	* git: [https://github.com/tmax-cloud/secret-watcher](https://github.com/tmax-cloud/secret-watcher)
	
## Dependency
1.  Hypercloud Operator
    * [Hypercloud Operator 설치 가이드](https://github.com/tmax-cloud/install-hypercloud/tree/4.1#hypercloud-operator-%EC%84%A4%EC%B9%98-%EA%B0%80%EC%9D%B4%EB%93%9C)
        
## Prerequisite
1. 설치를 진행하기 전 아래의 과정을 통해 필요한 이미지 및 yaml 파일을 준비한다.
    * 작업 디렉토리 생성 및 환경 설정
    ```bash
	mkdir -p ~/secret-watcher-install
	export HPCD_SW_HOME=~/secret-watcher-install
	export HPCD_SW_VERSION=<tag1>
	cd ${HPCD_SW_HOME}
	
	* <tag1>에는 설치할 hypercloud4-secret-watcher 버전 명시
		예시: export HPCD_SW_VERSION=b4.1.0.9
    ```

    * install yaml을 다운로드한다.
    ```bash
    wget -O secret-watcher.tar.gz https://github.com/tmax-cloud/secret-watcher/archive/v${HPCD_SW_VERSION}.tar.gz
	wget https://raw.githubusercontent.com/tmax-cloud/install-secretwatcher/4.1/manifest/hypercloud-secret-watcher-daemonset.yaml
    ```

    * **폐쇄망에서 설치하는 경우** 사용하는 image repository에 필요한 이미지를 pull받아 tar파일로 저장한다. 
    ```bash
	# secret-watcher
	sudo docker pull tmaxcloudck/hypercloud4-secret-watcher:b${HPCD_SW_VERSION}
	sudo docker save tmaxcloudck/hypercloud4-secret-watcher:b${HPCD_SW_VERSION} > hypercloud4-secret-watcher_b${HPCD_SW_VERSION}.tar
    ```
  
2. 위의 과정에서 생성한 압축 파일 및 yaml파일을 `설치할 환경의 $HPCD_SW_HOME} 디렉토리에 이동`시키고 환경 설정을 한다.
	* 작업 디렉토리 생성 및 환경 설정
    ```bash
	mkdir -p ~/secret-watcher-install
	export HPCD_SW_HOME=~/secret-watcher-install
	export HPCD_SW_VERSION=<tag1>
	cd ${HPCD_SW_HOME}
	tar -xvzf secret-watcher.tar.gz

	* <tag1>에는 설치할 hypercloud4-secret-watcher 버전 명시
		예시: export HPCD_SW_VERSION=b4.1.0.9
	```

	**폐쇄망에서 설치하는 경우** 사용하려는 registry에 이미지를 push한다.
	* 환경설정
	```bash
	# Set Environment
	export REGISTRY=<REGISTRY_IP_PORT>

	* <REGISTRY_IP_PORT>에는 폐쇄망 Docker Registry IP:PORT명시
		예시: export REGISTRY=192.168.6.110:5000

    # Load Images
	sudo docker load < hypercloud4-secret-watcher_b${HPCD_SW_VERSION}.tar
    
    # Change Image's Tag For Private Registry
	sudo docker tag tmaxcloudck/hypercloud4-secret-watcher:b${HPCD_SW_VERSION} ${REGISTRY}/tmaxcloudck/hypercloud4-secret-watcher:b${HPCD_SW_VERSION}
    
    # Push Images
	sudo docker push ${REGISTRY}/tmaxcloudck/hypercloud4-secret-watcher:b${HPCD_SW_VERSION}
    ```

## Install Steps
1. [hypercloud-secret-watcher-daemonset.yaml 수정]
2. [hypercloud-secret-watcher-daemonset.yaml 실행]

## Step 1. hypercloud-secret-watcher-daemonset.yaml 수정
* 목적 : `hypercloud-secret-watcher-daemonset.yaml 수정`
* 준비:
	* image version 수정
		```bash
		sed -i 's/tmaxcloudck\/hypercloud4-secret-watcher:latest/tmaxcloudck\/hypercloud4-secret-watcher:'${HPCD_SW_VERSION}'/g' ${HPCD_SW_HOME}/hypercloud-secret-watcher-daemonset.yaml
		```
* 비고
	* 폐쇄망의 경우 
		```bash
		sed -i 's/tmaxcloudck\/hypercloud4-secret-watcher:latest/'${REGISTRY}'\/tmaxcloudck\/hypercloud4-secret-watcher:'${HPCD_SW_VERSION}'/g' ${HPCD_SW_HOME}/hypercloud-secret-watcher-daemonset.yaml
		
## Step 2. hypercloud-secret-watcher-daemonset.yaml 실행
* 목적 : `secret-watcher daemonset 생성`
* 실행: 
	```bash
	kubectl apply -f ${HPCD_SW_HOME}/hypercloud-secret-watcher-daemonset.yaml
	```

## 삭제 가이드

## Step 0. hypercloud-secret-watcher-daemonset.yaml 다운로드
* 목적 : `hypercloud-secret-watcher-daemonset.yaml 다운로드`
* 준비:
	* yaml 파일 다운로드
	```bash
	mkdir -p ~/secret-watcher-install
	export HPCD_SW_HOME=~/secret-watcher-install
	cd ${HPCD_SW_HOME}
	wget https://raw.githubusercontent.com/tmax-cloud/install-secretwatcher/4.1/manifest/hypercloud-secret-watcher-daemonset.yaml
	```

## Step 1. hypercloud-secret-watcher-daemonset.yaml 삭제 실행
* 목적 : `secret-watcher daemonset 삭제`
* 실행: 
	```bash
	kubectl delete -f ${HPCD_SW_HOME}/hypercloud-secret-watcher-daemonset.yaml
	```