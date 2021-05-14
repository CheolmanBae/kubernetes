# kubernetes

- 목차

# 1 GCP 실습 환경 구성

## 1.1 GCP 프로젝트 생성하기

프로젝트를 관리할 수 있는 창으로 들어가자.

![image](https://user-images.githubusercontent.com/61235451/117742142-09153280-b23f-11eb-9991-0d34cff21fe2.png)


프로젝트를 삭제할 때는 관리 창으로가고 새 프로젝트를 생성할 때는 "새 프로젝트"를 선택하면 된다.

![image](https://user-images.githubusercontent.com/61235451/117742175-221de380-b23f-11eb-8d91-014b7debe561.png)

프로젝트를 다 사용하고 삭제하면 비용 청구가 되지 않는다.

새프로젝트를 만들어보자. 프로젝트 이름은 고유하게 만드는 것이 좋다. <my id>-gke-<오늘날짜>로 생성하자. 위치는 "조직 없음"으로 그냥 놔두자.

![image](https://user-images.githubusercontent.com/61235451/117742318-70cb7d80-b23f-11eb-93ff-a924c3474d00.png)

프로젝트 선택 화면으로 넘어가면 우리가 생성한 프로젝트를 확인할 수 있다. 

![image](https://user-images.githubusercontent.com/61235451/117742376-9eb0c200-b23f-11eb-8c3a-5338598fca81.png)

## 1.2 프로젝트에 가상 머신 할당하기

컴퓨트 엔진 메뉴로 접근한다.

![image](https://user-images.githubusercontent.com/61235451/117742409-abcdb100-b23f-11eb-804d-9a672f6a686a.png)

API 서비스를 활용하도록 "사용" 버튼을 누르자.

![image](https://user-images.githubusercontent.com/61235451/117742423-b7b97300-b23f-11eb-9f69-9ea793e3a8c8.png)

![image](https://user-images.githubusercontent.com/61235451/117742433-bdaf5400-b23f-11eb-98df-b5e87a9871eb.png)

인스턴스 만들 때 부팅 디스크를 다음과 같이 변경한다.

- Ubutu: 20.04 LTS
- 100GB 디스크

![image](https://user-images.githubusercontent.com/61235451/117742458-d0c22400-b23f-11eb-8003-dd427e5e71e8.png)

![image](https://user-images.githubusercontent.com/61235451/117742480-dae42280-b23f-11eb-921c-0007b786ba45.png)

방화벽 룰을 다음과 체크한다. 

![image](https://user-images.githubusercontent.com/61235451/117742490-df104000-b23f-11eb-8a1e-f6a8db47cf0d.png)

디스크와 방화벽 설정을 잘 변경했다면 "만들기" 버튼을 클릭한다.

## 1.3 가상머신에 SSH 접속

가상머신이 잘 만들어졌다면 SSH로 접근할 수 있다. SSH 버튼을 눌러서 접근해보자. 검은색 창이 하나 열리면서 쉘이 확인된다.

![image](https://user-images.githubusercontent.com/61235451/117742523-f0594c80-b23f-11eb-896f-c1f6e21ac155.png)

# 2 도커

## 2.1 도커 설치

작업의 편의를 위해 sudo -i를 사용해 관리자 권한으로 올라간다. apt를 사용해 도커를 설치하자.

```bash
sudo -i
apt update && apt install docker.io -y
```

도커에서 version 명령을 실행해 도커 설치 정보를 확인하자.

```bash
# docker version
Client:
 Version:           20.10.2
 API version:       1.41
 Go version:        go1.13.8
 Git commit:        20.10.2-0ubuntu1~20.04.2
 Built:             Tue Mar 30 21:24:57 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server:
 Engine:
  Version:          20.10.2
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.8
  Git commit:       20.10.2-0ubuntu1~20.04.2
  Built:            Mon Mar 29 19:10:09 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.3.3-0ubuntu2.3
  GitCommit:        
 runc:
  Version:          spec: 1.0.2-dev
  GitCommit:        
 docker-init:
  Version:          0.19.0
  GitCommit:
```

## 2.2 도커 이미지 검색하기

1. docker search 명령을 사용하기

    ```bash
    docker search tomcat
    ```

    ![image](https://user-images.githubusercontent.com/61235451/117742586-0ebf4800-b240-11eb-9296-8f4688f0b2ca.png)

2. [hub.docker.com](http://hub.docker.com) 사용하기

    ![image](https://user-images.githubusercontent.com/61235451/117742611-167eec80-b240-11eb-8793-c6b4b074d5d0.png)

## 2.3 도커 이미지 관리

도커 이미지는 레이어 방식으로 저장하기 떄문에 레이어 단위로 나눠서 다운로드를 받는다.

```bash
# docker pull consol/tomcat-7.0
Using default tag: latest
latest: Pulling from consol/tomcat-7.0
Image docker.io/consol/tomcat-7.0:latest uses outdated schema1 manifest format. Please upgrade to a schema2 image for better future compatibility. More information at https://docs.docker.com/registry/spec/deprecated-schema-v1/
e5ad7970bc69: Pull complete 
a3ed95caeb02: Pull complete 
d1bc08d19aa0: Pull complete 
e4b877670a03: Pull complete 
7fe52da169a9: Pull complete 
dd8c3151a5a5: Pull complete 
70eb33b1a032: Pull complete 
878a118528b8: Pull complete 
1c0747c147d5: Pull complete 
0d0d0f226ce8: Pull complete 
56357e7ea3fa: Pull complete 
3c3554f7c62d: Pull complete 
23de17079739: Pull complete 
Digest: sha256:8256b5e8e01fc4f6c1913e1fd70dea95ae656400f70fb1c12157d0e89e1ccaf7
Status: Downloaded newer image for consol/tomcat-7.0:latest
docker.io/consol/tomcat-7.0:latest
```

도커 이미지가 잘 다운로드 됐는지 확인해보자.

```bash
# docker images
REPOSITORY          TAG       IMAGE ID       CREATED       SIZE
consol/tomcat-7.0   latest    7c34bafd1150   5 years ago   601MB
```

도커 이미지는 다음과 같이 이미지 이름 하나로 태그를 통해 여러 버전을 관리할 수 있다.

mysql:5.4 mysql:5.5 mysql:5.6

만약 태그를 생략하면 자동으로 latest라는 태그가 붙는다.

도커 이미지 삭제

```bash
docker rmi consol/tomcat-7.0
```

도커 이미지 메니페스트 정보 확인하기

```bash
# docker inspect consol/tomcat-7.0
[
    {
        "Id": "sha256:7c34bafd11507bff45ed89ae7b2d56da68abd6b0f373c5e2a90393057785cca6",
        "RepoTags": [
            "consol/tomcat-7.0:latest"
        ],
        "RepoDigests": [
            "consol/tomcat-7.0@sha256:8256b5e8e01fc4f6c1913e1fd70dea95ae656400f70fb1c12157d0e89e1ccaf7"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2015-06-06T05:54:45.287323113Z",
        "Container": "5544910d974f121a6630b9676fe3469ae3cf9656b7263eb05066f31a99fd32b0",
        "ContainerConfig": {
            "Hostname": "eadddbb61a32",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "8080/tcp": {}, // 컨테이너 개발자가 명시
                "8778/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [          // 가상환경에서 사용할 환경 변수
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/tomcat/bin",
                "LANG=C.UTF-8",
                "JAVA_VERSION=7u79",
                "JAVA_DEBIAN_VERSION=7u79-2.5.5-1~deb8u1",
                "JOLOKIA_VERSION=1.3.1",
                "TOMCAT_VERSION=7.0.62",
                "DEPLOY_DIR=/maven",
                "CATALINA_HOME=/opt/tomcat"
            ],
            "Cmd": [          // 프로세스를 실행할때 사용하는 커맨드라인
                "/bin/sh",
                "-c",
                "#(nop) CMD [\"/bin/sh\" \"-c\" \"/opt/tomcat/bin/deploy-and-run.sh\"]"
            ],
            "Image": "bfc5f40a572ad8d95e5907efb341146879f13be8d1b224d4f6868d06a5220eb8",
            "Volumes": {
                "/opt/tomcat/logs": {},
                "/opt/tomcat/temp": {},
                "/opt/tomcat/work": {},
                "/tmp/hsperfdata_root": {}
            },
            "WorkingDir": "", // 기본 실행 경로, 비어있으면 루트
            "Entrypoint": null,
            "OnBuild": [],
            "Labels": {}
        },
...
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/d45f52493e4018552cd98125ed3e78fc77ad2f6e0792735c3775a5a59f022966/diff:/var/lib/docker/overlay2/a2d5d1b891d582c86ab5bc4b789b777dc147848f0a4e6eb3eb41c57be1935d10/diff:/var/lib/docker/overlay2/7da86d30a34020f24f5b77a4085ff54b5632e7929fc46b47bb3e2230e573ac01/diff:/var/lib/docker/overlay2/e92ed5b670f401e756d2d5a883642616e7959678d4342082fd0af7f85d8c16f1/diff:/var/lib/docker/overlay2/bca71d27372ad339cb59f6366f99f28fd9555479bce66317f500a0e0fcf48314/diff:/var/lib/docker/overlay2/1af71ead7e57d6eb0b844219e8b054c63bbca4b64914221e7b9757d94ad0a09e/diff:/var/lib/docker/overlay2/6270832f6175de53352e6b6c771e0c3480ad0b0ecb889832c88b1bf622c10429/diff:/var/lib/docker/overlay2/6c61c879d20e0956b1b5a8f290c184f6f40fad58205e311036c13e1a64c64040/diff:/var/lib/docker/overlay2/fcdc48803829976d11e8de357cc251ab1e408b8d74e6ab466793e3468db52200/diff:/var/lib/docker/overlay2/b4e07c5201c5ee93dd01281e35eef08ac656c597d210c57e88acf7fb6f119490/diff:/var/lib/docker/overlay2/89b6000eba5ac4bfd6a57b38ab26b604eb4715fc43e6f519724bb5f17cd3d04a/diff:/var/lib/docker/overlay2/b207bcde7778abb12c15a2293632b10c7d09bdb04261ceb6a60b9d728170717f/diff:/var/lib/docker/overlay2/a681c369d906056f788c78fde1b6268ccd11ff724292a98b387fbf961e2709ac/diff:/var/lib/docker/overlay2/237ca3a0934b9db7549300f931e59159a706777e7e8f318797ecec80692e80ed/diff:/var/lib/docker/overlay2/b990343c5508c53506bb325466c982f6423ce581f4b0df730668656ff3bf4ad6/diff:/var/lib/docker/overlay2/e5d7e78696e75388235f1316b16ccf5fb5fe3f80b65020514def08f874ae9f11/diff:/var/lib/docker/overlay2/5506c94f25798fd9c448349aec573d928c4df02e04bf097d8b367967506c6201/diff:/var/lib/docker/overlay2/c2c22d72eb4d4864603a3299651c8c957a7493f4fc077fb6704473f5e8295f60/diff:/var/lib/docker/overlay2/e9ac12c8d905ce282793584bb0c471ab946eb9d71ce5ffbd811918f368311ba9/diff:/var/lib/docker/overlay2/946d65299b609972a669e984428a49cacde463de2837a4c7397498fc73345e61/diff:/var/lib/docker/overlay2/0abcf38728a8e8c8e88e7ceb41504a3b3759f94408834015fe22a0f612fcfcbe/diff:/var/lib/docker/overlay2/4dbc90fd4a54a8fb0f4391e7f93ad987e712ffa902bc50db90107482cafbf7a7/diff:/var/lib/docker/overlay2/25a66461d01e13a2668c1d5f1f460ac80a93c4be504bce029797637427376baa/diff:/var/lib/docker/overlay2/e20135d067b188a67e9cd31ebc62e6add10b19b2a2eae3fa8df453d800aeadbf/diff:/var/lib/docker/overlay2/8e5846695b5d6df97e7030c3d2d7b6593d7e317be2bac4df7dbb7a9cfa261b43/diff:/var/lib/docker/overlay2/03a3ca06030b7332c59f24c01fbc3fea7c997dea7534d69ff5f363cbf8022b3a/diff",
                "MergedDir": "/var/lib/docker/overlay2/12a3148aee3b49c29358cad1c3038e4d509648dd91db539509c78d13c224b887/merged",
                "UpperDir": "/var/lib/docker/overlay2/12a3148aee3b49c29358cad1c3038e4d509648dd91db539509c78d13c224b887/diff",
                "WorkDir": "/var/lib/docker/overlay2/12a3148aee3b49c29358cad1c3038e4d509648dd91db539509c78d13c224b887/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:c5cc83103be7b2981f7ebd1354f512b0d544d78f53037e3d9128054633d544b5",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:55aabb022b6a880f761cf2af299cc5d66acddd27eded631ee0f6227c415d3cc3",
                "sha256:34ab461be6c671a29897523cce7b76b6e6804cd91cd54b8521edd1968f4e39c4",
                "sha256:c900a3fbdb49641a807664efb96ebdab799169e276986c168d87e8ea4da82ecc",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:78b32d65e0a59660aa5178f82c3aebb16f5451a41016045145c2ab7596342d3c",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:0bab0fa4ed5aaa82dc52b0143e62c0bf8dce3fd2eccd4f4b03f723625eb17812",
                "sha256:14c44f9f4a1a22f22034105ba3f8681c53f1e44a312c84065b7c8270d9a60992",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:d1a571b7eec96acf772cac2435ecf44c02b80b9cf3f8aeab6638c2305a3c2bd2",
                "sha256:9e5189be98a802151447beebbe4462deabf23dc775f54e299703b64d150be324",
                "sha256:936f772feac2e8e1b54ba6445d06136152a5970051cb93d3e951d96c8a820b0b",
                "sha256:89607351ec2612fb58b19410d3b6cca7d211d344b3d2e9679b5af54b77006b66",
                "sha256:ee3d91310d77693f3aa65fa38c08963feb9973866b0d10b97b12bf6c29907af3",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]

```

도커 이미지의 변경사항 확인하기

```bash
# docker history consol/tomcat-7.0
IMAGE          CREATED       CREATED BY                                      SIZE      COMMENT
7c34bafd1150   5 years ago   /bin/sh -c #(nop) CMD ["/bin/sh" "-c" "/opt/…   0B        
<missing>      5 years ago   /bin/sh -c #(nop) ENV PATH=/usr/local/sbin:/…   0B        
<missing>      5 years ago   /bin/sh -c #(nop) ENV CATALINA_HOME=/opt/tom…   0B        
<missing>      5 years ago   /bin/sh -c #(nop) VOLUME [/opt/tomcat/logs /…   0B        
<missing>      5 years ago   /bin/sh -c rm -rf /opt/tomcat/webapps/exampl…   0B        
<missing>      5 years ago   /bin/sh -c #(nop) ADD file:3f33d30e62b5e9f33…   428B      
<missing>      5 years ago   /bin/sh -c #(nop) ADD file:77830e2870a510a81…   40B       
<missing>      5 years ago   /bin/sh -c #(nop) ADD file:308c46f776046517b…   1.61kB    
<missing>      5 years ago   /bin/sh -c wget http://archive.apache.org/di…   13.6MB    
<missing>      5 years ago   /bin/sh -c #(nop) ENV DEPLOY_DIR=/maven         0B        
<missing>      5 years ago   /bin/sh -c #(nop) ENV TOMCAT_VERSION=7.0.62     0B        
<missing>      5 years ago   /bin/sh -c #(nop) EXPOSE 8080/tcp 8778/tcp      0B        
<missing>      5 years ago   /bin/sh -c #(nop) MAINTAINER roland@jolokia.…   0B        
<missing>      5 years ago   /bin/sh -c #(nop) CMD ["/bin/sh" "-c" "java …   0B        
<missing>      5 years ago   /bin/sh -c chmod 755 /bin/jolokia_opts && mk…   410kB     
<missing>      5 years ago   /bin/sh -c #(nop) ADD file:44336399b00083a4c…   1.1kB     
<missing>      5 years ago   /bin/sh -c #(nop) ENV JOLOKIA_VERSION=1.3.1     0B        
<missing>      5 years ago   /bin/sh -c #(nop) MAINTAINER roland@jolokia.…   0B        
<missing>      5 years ago   /bin/sh -c apt-get update && apt-get install…   295MB     
<missing>      5 years ago   /bin/sh -c #(nop) ENV JAVA_DEBIAN_VERSION=7u…   0B        
<missing>      5 years ago   /bin/sh -c #(nop) ENV JAVA_VERSION=7u79         0B        
<missing>      5 years ago   /bin/sh -c #(nop) ENV LANG=C.UTF-8              0B        
<missing>      5 years ago   /bin/sh -c apt-get update && apt-get install…   788kB     
<missing>      5 years ago   /bin/sh -c apt-get update && apt-get install…   122MB     
<missing>      5 years ago   /bin/sh -c apt-get update && apt-get install…   44.4MB    
<missing>      5 years ago   /bin/sh -c #(nop) CMD ["/bin/bash"]             0B        
<missing>      5 years ago   /bin/sh -c #(nop) ADD file:5de08c81c24812789…   125MB
```

## 2.4 다운로드 받은 이미지를 사용해 컨테이너 시작하기

run 명령을 사용해 이미지를 컨테이너로 실행해보자.

```bash
docker run -d -p 80:8080 --name tc consol/tomcat-7.0
```

- -d: 백그라운드에서 실행
- -p: publish 포트포워드 <호스트 포트>:<컨테이너 포트>
- —name: 이름을 명시, 이름을 명시하지 않으면 랜덤하게 만들어지는데 이 이름을 가지고 컨테이너를 컨트롤 할 수 있다.

도커 컨테이너가 잘 동작하는지 ps 명령으로 확인한다.

```bash
# docker ps -a
CONTAINER ID   IMAGE               COMMAND                  CREATED          STATUS          PORTS                            NAMES
65aae85fb9ad   consol/tomcat-7.0   "/bin/sh -c /opt/tom…"   29 seconds ago   Up 28 seconds   8778/tcp, 0.0.0.0:80->8080/tcp   tc
```

인스턴스의 외부 IP를 확인하고 접속해보자.

![image](https://user-images.githubusercontent.com/61235451/117743066-09163200-b241-11eb-88c5-22fb0e1f8d8c.png)

외부IP를 통해서 들어오면 tomcat이 오픈된것을 확인할 수 있으며 ManagerApp에서 admin//admin 인증정보를 테스트 할 수 있다.

![image](https://user-images.githubusercontent.com/61235451/117743086-0fa4a980-b241-11eb-9d32-adebaa05b02d.png)

## 2.5 도커 컨테이너 중지와 삭제, 생성, 시작

도커 컨테이너 중지를 한다.

```bash
docker stop tc
```

정지된 도커 컨테이너 확인

- -a 옵션을 붙여야 모든 도커 컨테이너를 확인할 수 있다. -a 옵션이 없으면 실행중인 컨테이너만 확인된다.

```bash
# docker ps -a
CONTAINER ID   IMAGE               COMMAND                  CREATED             STATUS                        PORTS     NAMES
65aae85fb9ad   consol/tomcat-7.0   "/bin/sh -c /opt/tom…"   About an hour ago   Exited (137) 21 seconds ago             tc
```

도커 컨테이너 삭제를 한다.

```bash
docker rm tc
```

도커 컨테이너 생성한다. run 명령과 매우 유사하지만 -d 옵션은 존재하지 않는다.

```bash
docker create -p 80:8080 --name tc consol/tomcat-7.0
```

컨테이너를 시작한다.

```bash
docker start <컨테이너 이름>
docker start tc
```

## 2.6 도커 컨테이너를 컨트롤할때 유용한 명령어

### 원격 접속 (SSH처럼 접속하기)

```bash
docker exec -it tc bash
```

- exec: 도커 컨테이너에 명령을 실행한다.
- -i: input
- -t: terminal tty
- tc: 컨테이너 이름
- bash: 실행할 명령어

실행을 하면 호스트 이름이 변경된다.

```bash
root@instance-1:~# docker exec -it tc bash
root@91e21a01a4fd:/# exit
```

exit 명령을 실행하고 docker ps -a 명령을 실행하면 91e~로 시작하는 정보가 무슨 정보인지 확인할 수 있다.

```bash
# docker ps -a
CONTAINER ID   IMAGE               COMMAND                  CREATED          STATUS          PORTS                            NAMES
91e21a01a4fd   consol/tomcat-7.0   "/bin/sh -c /opt/tom…"   19 minutes ago   Up 19 minutes   8778/tcp, 0.0.0.0:80->8080/tcp   tc
```

다시 컨테이너에 접속해서 프로세스를 실행하는 쉘 스크립트도 확인해보자.

```bash
cat /opt/tomcat/bin/deploy-and-run.sh
```

### 도커 로그 확인하기

도커는 stdout과 stderr를 로그로 본다.

```bash
docker logs tc
```

### 파일 복제하기

컨테이너 → 컨테이너, 호스트 → 컨테이너, 컨테이너 → 호스트 방향으로 파일을 복제하는 방법

```bash
docker cp /etc/passwd tc:/passwd
docker cp tc:/passwd /root/passwd
docker cp tc1:/passwd tc2:/passwd
```

내 컨테이너 IP 확인하기

```bash
# docker network inspect bridge

...
"Containers": {
            "91e21a01a4fdc0d999bf34f5d7b122eca7f1915d25862f40b98b09d7c2ec431a": {
                "Name": "tc",
                "EndpointID": "439ced6e66db63e2adfe846143938d18afac27c24be9c77a71a78a3e5c2e6d99",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
...

# curl 172.17.0.2:8080  // 컨테이너 포트로 직접 요청하기
```

## 2.7 컨테이너 활용 연습문제

1. 기존에 설치된 모든 컨테이너와 이미지 정지 및 삭제

    ```bash
    docker stop `docker ps -a -q`
    docker rm `docker ps -a -q`
    docker rmi `docker images -q`
    ```

2. 도커 기능을 사용해 Jenkins 검색(hub.docker.com을 검색하면 대체된 이미지에 대한 소개글 확인하기!)

    ```bash
    docker pull jenkins/jenkins:lts
    docker inspect jenkins/jenkins:lts # 8080 포트 확인!
    ```

3. Jenkins를 사용하여 설치

    ```bash
    docker run -d -p 80:8080 --name jk jenkins/jenkins:lts
    ```

4. Jenkins 포트로 접속하여 웹 서비스 열기

    ```bash
    curl 34.122.156.232
    ```

    ![image](https://user-images.githubusercontent.com/61235451/117743147-2a771e00-b241-11eb-9a37-7560cac49836.png)

5. 초기 패스워드 찾아서 설치화면으로 넘어가기

    1) 경로에 있는 파일 내용 확인하기

    ```bash
    docker exec jk cat /var/jenkins_home/secrets/initialAdminPassword
    ```

    2) 출력된 로그 확인하기

    ```bash
    docker logs jk
    ```

# 3 도커 이미지 빌드와 배포

## 3.1 도커 이미지 개발하기

도커 이미지를 개발할 소스코드를 작성한다.

```python
#! test_server.py
import socket

with socket.socket() as s:
	s.bind(("0.0.0.0", 12345)) # server
	s.listen()
	conn, addr = s.accept() # client request accepted
	with conn: # start data transfer
		print("Connect by", addr) # client info
		while True:
			data = conn.recv(1024)
			if 'q' == data.decode().strip(): break
			conn.sendall(data)
```

프로젝트 디렉토리를 하나 만들고 echo_server.py를 생성한다.

```bash
mkdir my_first_project
cd my_first_project
vim test_server.py
```

- vim 사용 방법
    1. vim에서 i를 입력하면 인서트 모드로 변경되는데 이 모드에서만 타이핑이 가능하다.
    2. ESC 누르면 인서트 모드에서 나갈 수 있다.
    3. Insert 모드에서 Ctrl + V를 누르면 복사한 내용을 옮겨넣을 수 있다.
    4. ESC 눌러서  인서트 모드에서 나간 후 :wq 를 입력한뒤 엔터를 입력한다.

우리가 생성한 파일이 잘 동작하는지 테스트해보자. 서버를 실행한다.

```bash
# python3 test_server.py
Connect by ('127.0.0.1', 34468) // 클라이언트 접속시 나오는 내용
```

셸을 하나더 열어서 nc로 접속해보자.

```bash
$ nc 127.0.0.1 12345
test // 보낸 데이터
test // 받은 데이터
1234
1234
q
```

## 3.2 개발한 코드를 이미지로 빌드하기

다음과 같이 dockerfile을 작성한다.

- FROM: 도커 이미지 python:3.7에서 작업을 시작한다.
- RUN: mkdir /echo 명령을 실행한다.
- COPY: test_server.py를 /echo 디렉토리로 복사한다.
- CMD: 컨테이너 시작 시 실행할 명령어를 등록한다.

```docker
FROM python:3.7

RUN mkdir /echo
COPY test_server.py /echo

CMD ["python", "/echo/test_server.py"]
```

도커 파일을 작성하고 빌드 명령을 실행한다.

```docker
vim dockerfile
docker build -t echo_test .
```

도커 빌드가 잘 실행됐다면 도커 이미지 목록에 python:3.7과 echo_test:latest가 추가돼있다.

```docker
# docker images
REPOSITORY        TAG       IMAGE ID       CREATED          SIZE
echo_test         latest    cbafd1a5c983   17 minutes ago   877MB
python            3.7       3a781253f798   6 days ago       877MB
```

우리가 작성한 echo_test가 잘동작하는지 컨테이너를 실행해보자.

```docker
docker run -d -p 12345:12345 --name echo echo_test:latest
```

셸을 하나더 열어서 nc로 접속해보자.

```bash
$ nc 127.0.0.1 12345
test // 보낸 데이터
test // 받은 데이터
1234
1234
q
```

## 3.3 도커 허브에 내 레파지토리에 echo_test 이미지 푸시하기

[hub.docker.com](http://hub.docker.com) 에 회원가입을 하면 내 레파지토리가 생성된다.

도커 공용 허브에 이미지를 업로드할 때는 도커 이미지 이름을 변경해야 한다.

```bash
docker tag echo_test:latest <my_id>/echo_test:latest

# 내 id로 변경한 태그 이름
docker tag echo_test:latest gasbugs/echo_test:latest
```

태그가 잘 추가됐는지 확인

```bash
# docker images
REPOSITORY          TAG       IMAGE ID       CREATED          SIZE
gasbugs/echo_test   latest    cbafd1a5c983   26 minutes ago   877MB
echo_test           latest    cbafd1a5c983   26 minutes ago   877MB
```

해당 아이디로 로그인 수행

```bash
# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: gasbugs
Password: <패스워드>
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

푸시를 실행해보자. 푸시된 레이어는 두 개 뿐이다. 기존에 python에 존재하는 이미지를 사용했기 때문에 중복되는 레이어는 업로드하지 않는다.

```bash
# docker push gasbugs/echo_test:latest
The push refers to repository [docker.io/gasbugs/echo_test]
e9ec808befc5: Pushed 
c582f5cb400f: Pushed 
201a43818b9e: Mounted from library/python 
ad316ea514d2: Layer already exists 
84a815ddec01: Layer already exists 
8d18b38717e2: Layer already exists 
651326e9f1ca: Layer already exists 
5d5962699bd5: Layer already exists 
a42439ce9650: Layer already exists 
26270c5e25fa: Layer already exists 
e2c6ff462357: Layer already exists 
latest: digest: sha256:41bc6d68d83d9b1f2f89b2f72230747ecbcc810af108f70da3404b1d50e7ac83 size: 2631
```

## 3.4 프라이베이트 레지스트리

[https://hub.docker.com/_/registry](https://hub.docker.com/_/registry)

도커에서 지원하는 공식 레지스트리 컨테이너

다음 명령을 사용하면 로컬 프라이베이트 레지스트리 구현된다.

```bash
docker run -d -p 5000:5000 --restart always --name registry registry:2
```

echo_test에 태그를 새로 작성하고 레지스트리에 업로드해보자. 변경된 이름으로 푸시를 진행한다.

```bash
docker tag echo_test:latest 127.0.0.1:5000/echo_test:latest
docker push 127.0.0.1:5000/echo_test:latest
```

푸시를 할때도 레이어의 특징을 확인할 수 있다. 레지스트리가 비어있기 때문에 모든 레이어가 푸시된다.

```bash
# docker tag echo_test:latest 127.0.0.1:5000/echo_test:latest
root@instance-1:~/my_first_project# docker push 127.0.0.1:5000/echo_test:latest
The push refers to repository [127.0.0.1:5000/echo_test]
e9ec808befc5: Pushed 
c582f5cb400f: Pushed 
201a43818b9e: Pushed 
ad316ea514d2: Pushed 
84a815ddec01: Pushed 
8d18b38717e2: Pushed 
651326e9f1ca: Pushed 
5d5962699bd5: Pushed 
a42439ce9650: Pushed 
26270c5e25fa: Pushed 
e2c6ff462357: Pushed 
latest: digest: sha256:41bc6d68d83d9b1f2f89b2f72230747ecbcc810af108f70da3404b1d50e7ac83 size: 2631
```

registry는 restAPI를 사용한다. 다음 명령으로 이미지 목록을 확인할 수 있다.

```bash
curl -X GET http://localhost:5000/v2/_catalog

# 출력
{"repositories":["echo_test"]}
```

이미지 이름을 조회했다면 태그 이름도 조회할 수 있다.

```bash
curl -X GET http://localhost:5000/v2/echo_test/tags/list
{"name":"echo_test","tags":["latest"]}
```

## 3.5 도커 컨테이너 자원 사용량 통계 보기

```bash
# docker stats
CONTAINER ID   NAME       CPU %     MEM USAGE / LIMIT    MEM %     NET I/O          BLOCK I/O         PIDS
c50f36f14f40   registry   0.00%     11.1MiB / 3.843GiB   0.28%     339MB / 1.73MB   483kB / 209MB     10
3fd8e3f045cf   jk         0.15%     567MiB / 3.843GiB    14.41%    5.41MB / 947kB   9.71MB / 8.54MB   39 
```

# 4. 쿠버네티스 클러스터 구성하기

왼쪽 메뉴에서 쿠버네티스 엔진로 접근하면 클러스터 "만들기"를 클릭할 수 있다.

![image](https://user-images.githubusercontent.com/61235451/117743269-6d38f600-b241-11eb-9579-d4957c51a648.png)

다수의 노드를 구성해서 클러스터를 만드는게 원칙이지만...

클라우드 제공 업체는 Serverless 방식을 사용해 노드 없이 클러스터 방식을 개발했다.

![image](https://user-images.githubusercontent.com/61235451/117743281-732ed700-b241-11eb-9444-3223bbbb21a1.png)

구성을 선택하고 "만들기"를 바로 선택한다.

생성이 완료되면 오른쪽에 연결 버튼을 클릭하고

![image](https://user-images.githubusercontent.com/61235451/117743343-95c0f000-b241-11eb-9944-731dba76baa5.png)

클라우드 쉘에서 실행을 선택한다.

![image](https://user-images.githubusercontent.com/61235451/117743351-9c4f6780-b241-11eb-9d9e-b2fa9930accb.png)

그러면 클라우드 셸이 하단에 창이 생성되면서 명령어가 자동 입력된다. 마스터 노드에 접근할 수 있는 인증정보를 설정하는 커맨드다.

![image](https://user-images.githubusercontent.com/61235451/117743373-acffdd80-b241-11eb-9330-f84e98264e49.png)

마스터 노드에 질의를 시작해보자.

```bash
$ kubectl get nodes
NAME                                       STATUS   ROLES    AGE     VERSION
gke-cluster-1-default-pool-329c78b9-1z0s   Ready    <none>   5m13s   v1.18.17-gke.100
gke-cluster-1-default-pool-329c78b9-gfvw   Ready    <none>   5m13s   v1.18.17-gke.100
gke-cluster-1-default-pool-329c78b9-p0zt   Ready    <none>   5m13s   v1.18.17-gke.100
```

nginx 배포하기

```bash
kubectl create deploy nx --image=nginx
kubectl expose deploy nx --port=80 --type=LoadBalancer
```

외부 부하분산기가 IP를 받을 떄까지 다음 명령을 실행한다.

```bash
$ kubectl get svc
NAME         TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
kubernetes   ClusterIP      10.8.0.1     <none>          443/TCP        7m55s
nx           LoadBalancer   10.8.1.179   35.193.233.98   80:30294/TCP   44s
```

클러스터에 배포된 nginx로 접근해보자.

![image](https://user-images.githubusercontent.com/61235451/117743393-bb4df980-b241-11eb-94f7-d6846682ac39.png)

퇴실할 때 클라우드 정리하기 

1. 클러스터(cluster-1)는 삭제
2. 인스턴스(instance-1)는 중지



--------------------------------------------------------------------------------------------------------------------


- 목차

# 온프레미스 환경에서 쿠버네티스 클러스터 구축

## 노드 준비

인스턴스를 1~3번까지 3개를 생성한다. 생성시 다음과 같이 운영체제를 선택하고 100GB 디스크를 사용하도록 하자.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/325c30f5-e9e3-4059-ae57-66e8f15c84d0/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/325c30f5-e9e3-4059-ae57-66e8f15c84d0/Untitled.png)

각각의 인스턴스 내부 IP를 확인하고 SSH로 접속한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7b70a65b-4019-4e6d-bdde-1f0a26b08810/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7b70a65b-4019-4e6d-bdde-1f0a26b08810/Untitled.png)

접속 후에는 다른 인스턴스와 Ping 통신이 되는지 확인한다. (Ctrl + C를 누르면 종료)

```bash
ping 10.128.0.6
ping 10.128.0.7
```

쿠버네티스 공식 도큐먼트에서 kubeadm 인스톨 확인하자.

[https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm/install-kubeadm/](https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

## 쿠버네티스 구축을 시작하기 전에 확인해야 되는 사항

- 호환되는 리눅스 머신. 쿠버네티스 프로젝트는 데비안 기반 배포판, 레드햇 기반 배포판, 그리고 패키지 매니저를 사용하지 않는 경우에 대한 일반적인 가이드를 제공한다.
- 2 GB 이상의 램을 장착한 머신. (이 보다 작으면 사용자의 앱을 위한 공간이 거의 남지 않음)
- 2 이상의 CPU.
- 클러스터의 모든 머신에 걸친 전체 네트워크 연결. (공용 또는 사설 네트워크면 괜찮음)
- 모든 노드에 대해 고유한 호스트 이름, MAC 주소 및 product_uuid. 자세한 내용은 여기를 참고한다.
- 컴퓨터의 특정 포트들 개방. 자세한 내용은 여기를 참고한다.
- 스왑의 비활성화. kubelet이 제대로 작동하게 하려면 반드시 스왑을 사용하지 않도록 설정한다.
MAC 주소

    스왑을 비활성화 시키는 명령어

    ```bash
    sudo swapoff -a # 운영 체제 현재 상태를 swap off시키는 기능, 리붓 하면 설정이 사라짐
    sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab # 리붓할 때부터 기능이 활성화됨
    ```

## 도커 런타임 설치

```bash
sudo apt update && sudo apt install docker.io -y
```

## kubeadm, kubelet 및 kubectl 설치

- kubeadm: 클러스터를 부트스트랩하는 명령이다.
- kubelet: 클러스터의 모든 머신에서 실행되는 파드와 컨테이너 시작과 같은 작업을 수행하는 컴포넌트이다.
- kubectl: 클러스터와 통신하기 위한 커맨드 라인 유틸리티이다.

다음 내용을 kube_install.sh 파일로 저장하자.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

다음과 같이 쿠버네티스 설치 스크립트 파일을 생성하고 배시로 실행하자.

```bash
vim kube_install.sh
sudo bash kube_install.sh
```

설치된 버전 확인하기

```bash
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"21", GitVersion:"v1.21.0", GitCommit:"cb303e613a121a29364f75cc67d3d580833a7479", GitTreeState:"clean", BuildDate:"2021-04-08T16:31:21Z", GoVersion:"go1.16.1", Compiler:"gc", Platform:"linux/amd64"}
```

kubeadm으로 클러스터 생성하기

[https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)

# 마스터 노드 초기화하기

우리는 마스터노드를 instance-1로 설정하겠다.

```bash
sudo kubeadm init
```

만약 인터넷이 되지 않는 환경에서 init을 수행하는 경우, 인터넷이 되는 환경에서 다음 명령을 수행해 필요한 도커 컨테이너 이미지를 모두 풀링 받고 수행해야한다. (docker save 기능을 사용하면 도커 이미지를 tar 파일로 출력할 수 있다)

```bash
kubeadm config images pull
```

kubeadm이 init 에 성공하면 다음과 같은 내용이 나온다.

```bash
Your Kubernetes control-plane has initialized successfully!

# 관리자 유저를 세팅하는 방법, 마스터 노드에서만 세팅하도록 하자.
To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

# 컨테이너 네트워크 구성하는 방법
You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

# 다른 노드를 참여시키는 방법
Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.128.0.2:6443 --token becubc.s60bgyz7jmodrt1j \
        --discovery-token-ca-cert-hash sha256:223bb019b5949cbf91ffe461a8b77fb5b1c87baca4a9f14a8adc811804e0439f
```

## 클러스터 구성하기

유저 설정 가이드에 따라서 kubelet의 설정을 변경하고 노드에 대한 질의를 수행해보자. 현재는 마스터 노드만 확인된다.

```bash
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config

$ kubectl get node
NAME         STATUS     ROLES                  AGE    VERSION
instance-1   NotReady   control-plane,master   4m9s   v1.21.0
```

각 워커 노드(instance-2, instance-3 )에 가서 kubeadm join 명령을 관리자 권한으로 실행한다.

```bash
sudo kubeadm join 10.128.0.2:6443 --token becubc.s60bgyz7jmodrt1j \
        --discovery-token-ca-cert-hash sha256:223bb019b5949cbf91ffe461a8b77fb5b1c87baca4a9f14a8adc811804e0439f
```

쿠버네티스 클러스터의 노드를 조회하면 세개로 늘어나 있는 모습을 확인할 수 있다.

```bash
$ kubectl get node
NAME         STATUS     ROLES                  AGE     VERSION
instance-1   NotReady   control-plane,master   7m23s   v1.21.0
instance-2   NotReady   <none>                 32s     v1.21.0
instance-3   NotReady   <none>                 28s     v1.21.0
```

NotReady가 된 이유는 포드끼리 통신할 때 필요한 플러그인 설치가 필요한 상태이기 떄문이다.

다음 링크를 따라가서 weavenet 설치 방법을 확인할 수 있다. kubectl apply 명령으로 weavenet을 설치하자.

[https://www.weave.works/docs/net/latest/kubernetes/kube-addon/](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/)

```bash
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

1~2분 후 노드의 준비상태가 Ready로 변경된다.

```bash
$ kubectl get nodes
NAME         STATUS   ROLES                  AGE     VERSION
instance-1   Ready    control-plane,master   11m     v1.21.0
instance-2   Ready    <none>                 4m40s   v1.21.0
instance-3   Ready    <none>                 4m36s   v1.21.0
```

- 참고: kubeadm이 이미 join 됐거나 init 된 경우에는 다시 init 하면 오류가 발생한다. 이 때는 다음 과 같이 reset 명령을 실행하고 init하면 정상실행된다. (디렉토리가 이미 있다는 문구가 나오는 경우에는 해당 디렉토리를 모두 삭제하고 진행한다.

    ```bash
    sudo kubeadm reset
    sudo rm -rf <디렉토리>
    sudo rm -rf /etc/kubernetes/manifests
    sudo rm -rf /etc/kubernetes/kubelet.conf
    sudo rm -rf /etc/kubernetes/pki/ca.crt
    ```

삭제명령을 실행하지 마세요!

```bash
kubectl delete nodes instance-2
```

조인 명령을 다시 생성하는 방법

```bash
$ kubeadm token create --print-join-command
kubeadm join 10.128.0.2:6443 --token ifxc61.7km08knk51jivqaz --discovery-token-ca-cert-hash sha256:223bb019b5949cbf91ffe461a8b77fb5b1c87baca4a9f14a8adc811804e0439f
```

현재 발급된 토큰 조회

```bash
$ kubeadm token list
TOKEN                     TTL         EXPIRES                USAGES                   DESCRIPTION                                                EXTRA GROUPS
becubc.s60bgyz7jmodrt1j   22h         2021-05-12T01:12:55Z   authentication,signing   The default bootstrap token generated by 'kubeadm init'.   system:bootstrappers:kubeadm:default-node-token
ifxc61.7km08knk51jivqaz   23h         2021-05-12T01:51:46Z   authentication,signing   <none>                                                     system:bootstrappers:kubeadm:default-node-token
```

## 클러스터에 앱 배포하기

어제 배포했던 nx를 온프레미스 환경에서 배포해보자.

```bash
kubectl create deploy nx --image=nginx
kubectl expose deploy nx --port=80 --type=NodePort # 외부 부하 분산기는 배치되지 않는다.
```

컨테이너가 잘 배치됐는지 확인한다.

```bash
$ kubectl get pod
NAME                  READY   STATUS    RESTARTS   AGE
nx-5d8c979794-bfjr7   1/1     Running   0          47s
```

서비스가 잘 노출됐는지 확인한다.

```bash
$ kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        44m
nx           NodePort    10.101.134.190   <none>        80:31564/TCP   66s
```

노드에 포트가 정말 열렸는지 확인해보자.

```bash
curl 127.0.0.1:31564
```

# 쿠버네티스 앱 실행과 관찰

## gasbugs/http-go 테스트

강사가 미리 만들어둔 http-go 컨테이너를 테스트해보자.

```bash
docker run -d -p 8888:8080 --name http-go gasbugs/http-go
```

클라우드 셸에서 이 명령을 실행하고 접속해보자. 클라우드 셸은 완전히 별도의 또다른 가상머신이다. 클라우드 셸의 웹 미리보기 기능을 사용하면 해당 노드로 접근할 수 있다. 우리가 접근할 포트는 8888번 포트다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/650c0b74-1061-4f00-9bf0-8e610722462a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/650c0b74-1061-4f00-9bf0-8e610722462a/Untitled.png)

## GKE 쿠버네티스 생성

gke 클러스터를 구성할 때는 현재 인스턴스가 구성된 곳과는 전혀 다른 리전을 사용해야 한다. 리전이 겹치는 경우 할당량 제한으로 클러스터 생성이 제한된다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d0fa65eb-1483-4932-9938-150e96189b15/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d0fa65eb-1483-4932-9938-150e96189b15/Untitled.png)

클러스터가 생성되면 연결 버튼을 눌러서 클러스터로 접근한다.

## http-go 서비스 배포와 확인

클라우드 셸에서 다음 명령을 실행한다.

--target-port=8080 : 컨테이너의 포트가 8080

--port=80: 서비스를 실제 오픈할 때 어떤 포트를 사용할지 결정(

```bash
$ kubectl delete all --all
$ kubectl create deploy http-go --image=gasbugs/http-go
$ kubectl expose deploy http-go --port=80 --target-port=8080 --type=LoadBalancer 

$ kubectl get all
NAME                           READY   STATUS    RESTARTS   AGE
pod/http-go-5bcd97bbdf-588hq   1/1     Running   0          49s

NAME                 TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)        AGE
service/http-go      LoadBalancer   10.12.15.210   34.75.142.42   80:31662/TCP   40s
service/kubernetes   ClusterIP      10.12.0.1      <none>         443/TCP        103s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/http-go   1/1     1            1           49s

NAME                                 DESIRED   CURRENT   READY   AGEreplicaset.apps/http-go-5bcd97bbdf   1         1         1       50s
```

받은 IP에 curl 요청을 수행한다. 받아온 호스트 이름이 자신이 생성한 포드 이름과 동일한지 확인한다.

```bash
$ curl 34.75.142.42
Welcome! http-go-5bcd97bbdf-588hq

$ kubectl get pod
NAME                       READY   STATUS    RESTARTS   AGE
http-go-5bcd97bbdf-588hq   1/1     Running   0          81s
```

포드를 스케일링해서 개수를 늘려보자. 그리고 여전히 서비스를 잘하는지도 확인해보자.

```bash
kubectl scale deploy http-go --replicas=3
```

스케일 명령을 실행하면 포드의 개수가 세개로 늘어난다.

```bash
$ kubectl get pod
NAME                       READY   STATUS    RESTARTS   AGE
http-go-5bcd97bbdf-588hq   1/1     Running   0          3m46s
http-go-5bcd97bbdf-nm5qj   1/1     Running   0          7s
http-go-5bcd97bbdf-t8z8w   1/1     Running   0          7s
```

curl 요청을 해보면 로드밸런싱 되는 모습을 확인할 수 있다.

```bash
$ curl 34.75.142.42
Welcome! http-go-5bcd97bbdf-588hq

$ curl 34.75.142.42
Welcome! http-go-5bcd97bbdf-nm5qj

$ curl 34.75.142.42
Welcome! http-go-5bcd97bbdf-t8z8w
```

컨테이너의 위치 확인하기

```bash
$ kubectl get pod -o wide
NAME                       READY   STATUS    RESTARTS   AGE     IP         NODE                                       NOMINATED NODE   READINESS GATES
http-go-5bcd97bbdf-588hq   1/1     Running   0          6m41s   10.8.1.5   gke-cluster-1-default-pool-35aca696-bth0   <none>           <none>
http-go-5bcd97bbdf-nm5qj   1/1     Running   0          3m2s    10.8.1.6   gke-cluster-1-default-pool-35aca696-bth0   <none>           <none>
http-go-5bcd97bbdf-t8z8w   1/1     Running   0          3m2s    10.8.1.7   gke-cluster-1-default-pool-35aca696-bth0   <none>           <none>
```

포드 디스크라이브 확인하기

```bash
$ kubectl describe pod http-go-5bcd97bbdf-588hq
Name:         http-go-5bcd97bbdf-588hq
Namespace:    default
Priority:     0
Node:         gke-cluster-1-default-pool-35aca696-bth0/10.142.0.3
Start Time:   Tue, 11 May 2021 03:06:15 +0000
Labels:       app=http-go
              pod-template-hash=5bcd97bbdf
Annotations:  <none>
Status:       Running
IP:           10.8.1.5
IPs:  IP:           10.8.1.5
Controlled By:  ReplicaSet/http-go-5bcd97bbdf
Containers:
  http-go:
    Container ID:   docker://1488c8f157704407f49118d76d9ba48b293f0077ea87f73a30d6232a7c5e85ed
    Image:          gasbugs/http-go
    Image ID:       docker-pullable://gasbugs/http-go@sha256:5cf243f818caf7a750761cfe2ff822a284d6eb459356318d3f0bd4b1efe141d9
    Port:           <none>
    Host Port:      <none>
    State:          Running
       Started:      Tue, 11 May 2021 03:06:45 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-hmhkr (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-hmhkr:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-hmhkr
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
**Events:**
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  8m51s  default-scheduler  Successfully assigned default/http-go-5bcd97bbdf-588hq to gke-cluster-1-default-pool-35aca696-bth0
  Normal  Pulling    8m51s  kubelet            Pulling image "gasbugs/http-go"
  Normal  Pulled     8m24s  kubelet            Successfully pulled image "gasbugs/http-go"
  Normal  Created    8m21s  kubelet            Created container http-go
  Normal  Started    8m21s  kubelet            Started container http-go
```

## 연습문제

쿠버네티스를 활용해서 Jenkins 서비스하기

kubectl delete all --all 를 실행해서 모든 서비스를 지우고 시작한다.

(클라우드 셸)

```bash
kubectl delete all --all
kubectl create deploy jk --image=jenkins/jenkins:lts
kubectl expose deploy jk --port=80 --target-port=8080 --type=LoadBalancer 
```

생성된 객체들에 대한 정보 확인

```bash
$ kubectl get all
NAME                      READY   STATUS    RESTARTS   AGE
pod/jk-67755cd86b-qj6v2   1/1     Running   0          54s

NAME                 TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
service/jk           LoadBalancer   10.12.11.151   35.237.53.178   80:30076/TCP   48s
service/kubernetes   ClusterIP      10.12.0.1      <none>          443/TCP        79s

NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/jk   1/1     1            1           54s

NAME                            DESIRED   CURRENT   READY   AGE
replicaset.apps/jk-67755cd86b   1         1         1       55s
```

# CKA 시험 준비

## CKA 시험 준비할 때 좋은 강의

[https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/)

## 쿠버네티스 치트시트

[https://kubernetes.io/docs/reference/kubectl/cheatsheet/](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

```bash
source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc
alias k=kubectl
complete -F __start_kubectl k
```

# 쿠버네티스 컴포넌트

## 핵심 컴포넌트 확인

큐브시스템 네임스페이스에서 핵심 컴포넌트에 대한 정보 확인

```bash
$ kubectl get pod -n kube-system
NAME                                 READY   STATUS    RESTARTS   AGE
coredns-558bd4d5db-95lv9             1/1     Running   0          4h13m
coredns-558bd4d5db-kxb4w             1/1     Running   0          4h13m
etcd-instance-1                      1/1     Running   0          4h13m
kube-apiserver-instance-1            1/1     Running   0          4h13m
kube-controller-manager-instance-1   1/1     Running   0          4h13m
kube-proxy-4vhzd                     1/1     Running   0          4h7m
kube-proxy-m8hx8                     1/1     Running   0          4h6m
kube-proxy-nsfph                     1/1     Running   0          4h13m
kube-scheduler-instance-1            1/1     Running   0          4h13m
weave-net-6bf2z                      2/2     Running   1          4h2m
weave-net-ctlp5                      2/2     Running   1          4h2m
weave-net-d86t5                      2/2     Running   1          4h2m
```

쿠버네티스 핵심 컴포넌트 설정 파일 위치

```bash
ls -la /etc/kubernetes/manifests/
```

api 서버 정보확인

```bash
sudo vim /etc/kubernetes/manifests/kube-apiserver.yaml

(여기서 빠져 나올때는 :q!)
```

## etcd 활용

etcd 릴리즈 사이트

[https://github.com/etcd-io/etcd/releases](https://github.com/etcd-io/etcd/releases)

etcd 다운로드 및 압축 해제

```bash
cd ~
wget https://github.com/etcd-io/etcd/releases/download/v3.2.32/etcd-v3.2.32-linux-amd64.tar.gz
tar -xf etcd-v3.2.32-linux-amd64.tar.gz
cd etcd-v3.2.32-linux-amd64

```

ectdctl을 사용해서 쿠버네티스 etcd에 접속해보자. get 요청을 사용해 /로 시작하는 모든 키를 조회한다.

```bash
sudo ETCDCTL_API=3 ./etcdctl --endpoints 127.0.0.1:2379 \
                             --cacert /etc/kubernetes/pki/etcd/ca.crt \
                             --cert   /etc/kubernetes/pki/etcd/server.crt \
                             --key    /etc/kubernetes/pki/etcd/server.key \
                             get / --prefix --keys-only
```

key와 value를 작성해서 DB에 삽입해보자.

```bash
sudo ETCDCTL_API=3 ./etcdctl --endpoints 127.0.0.1:2379 \
                             --cacert /etc/kubernetes/pki/etcd/ca.crt \
                             --cert   /etc/kubernetes/pki/etcd/server.crt \
                             --key    /etc/kubernetes/pki/etcd/server.key \
                             put key1 value1
```

우리가 입력한 정보가 잘 조회되는지 확인한다.

```bash
sudo ETCDCTL_API=3 ./etcdctl --endpoints 127.0.0.1:2379 \
                             --cacert /etc/kubernetes/pki/etcd/ca.crt \
                             --cert   /etc/kubernetes/pki/etcd/server.crt \
                             --key    /etc/kubernetes/pki/etcd/server.key \
                             get key1
```

# Pod

포드의 yaml 작성 요령: http-go-pod.yaml

```bash
# http-go-pod.yaml
# 이 디스크립터는 쿠버네티스 API v1를 사용
apiVersion: v1
# 리소스 포드에 대한 설명
kind: Pod
metadata:
  # 포드의 이름
  name: http-go
spec:
  containers:
  # 생성할 컨테이너의 컨테이너 이미지
  - image: gasbugs/http-go
    name: http-go
    ports:
    # 응답 대기할 애플리케이션 포트
    - containerPort: 8080
      protocol: TCP
```

다른 포드의 yaml 파일 확인하기

```bash
# 대상 선정을 위해 포드를 조회
kubectl get pod 

# 포드의 정보를 yaml 파일 형식으로 출력한다.
kubectl get pod jk-67755cd86b-qj6v2 -o yaml
```

프로젝트용 디렉토리 생성

```bash
mkdir gke-20210510
```

클라우드 셸에서 새창에서 열기를 선택한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/da473f2d-6c30-4a31-975f-bfc256918d5b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/da473f2d-6c30-4a31-975f-bfc256918d5b/Untitled.png)

클라우드 셸에서 편집기를 클릭한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6013eacd-c3bd-4f50-8b2e-2185d772c49a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6013eacd-c3bd-4f50-8b2e-2185d772c49a/Untitled.png)

클라우드 셸 에디터에서 해당 디렉토리를 오픈하고 http-go-pod.yaml 파일을 생성한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e95ebdfc-188a-4ca5-a2c2-8071eb51c62e/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e95ebdfc-188a-4ca5-a2c2-8071eb51c62e/Untitled.png)

생성한 파일을 토대로 kubectl을 실행한다.

```bash
cd gke-20210510
kubectl create -f http-go-pod.yaml
```

## 포드 정보 조회하는 방법

컨테이너 로그 확인하기

```bash
kubectl logs <포드 이름>
```

포드 정보 확인하기

```bash
kubectl get pod
kubectl get pod -o wide
kubectl get pod <pod_name> -o json
kubectl get pod <pod_name> -o yaml
kubectl describe pod <pod_name>
```

## 포드에 유용한 기능

명령어 실행하기

```bash
docker exec -it <container_id> <command>

kubectl exec -it <pod_name> -- <command> 
```

포트포워딩 기능 실행하기(디버깅 용도)

```bash
kubectl port-forward <pod_name> <host_port>:<container_port>

kubectl port-forward http-go 8888:8080
(프로세스가 실행되는 동안에만 포트포워드)
중단하려면 Ctrl + C
```

포트포워드 명령이 실행하는 동안에 웹미리보기 실행

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c1b35838-ea6d-4a16-b5d1-f26ee54ec566/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c1b35838-ea6d-4a16-b5d1-f26ee54ec566/Untitled.png)

## 포드 삭제하기

```bash
kubectl delete pod <pod_name> # 특정 포드 삭제
kubectl delete pod --all # 모든 포드 삭제
```

## 연습문제

1. 모든 리소스 삭제

    ```bash
    kubectl delete all --all
    ```

2. YAML을 사용하여 도커 이미지 jenkins로 jenkins-manual 포드를 생성하기

    kubectl create -f ~/gke-20210510/jenkins-manual-pod.yaml

    ```bash
    # jenkins-manual-pod.yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: jenkins-manual
    spec:
      containers:
      - image: jenkins/jenkins:lts
        name: jenkins
        ports:
        - containerPort: 8080
          protocol: TCP
    ```

3. jenkins 포드 안에서 curl 명령어로 로컬호스트:8080 접속하기

    ```bash
    kubectl exec -it jenkins-manual -- curl localhost:8080
    ```

4. jenkins 포트를 8888로 포트포워딩하기

    ```bash
    kubectl port-forward jenkins-manual 8888:8080
    ```

5. 현재 Jenkins-manual의 설정을 yaml로 출력하기

    ```bash
    kubectl get pod jenkins-manual -o yaml
    ```

6. 초기 패스워드 찾아서 Unlock 진행하기

    ```bash
    # stdout 확인하기
    kubectl logs jenkins-manual

    # 특정 경로 파일을 확인하기
    kubectl exec -it jenkins-manual -- cat /var/jenkins_home/secrets/initialAdminPassword
    ```

# 라이브네스, 레디네스, 스타트업 프로브

[https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

## 라이브네스 커맨드 테스트

다음 명령을 실행해 라이브네스 커맨드가 등록된 포드를 생성하고 관찰해 보자.

```bash
kubectl apply -f https://k8s.io/examples/pods/probe/exec-liveness.yaml
```

도큐먼트 코드를 잘 살펴보면 /tmp/healthy 파일을 30초 후에 삭제한다. 라이브네스 커맨드는 30초 후부터는 이 파일 조회에 실패할 것이다.

파일 생성 후 30초가 지난 뒤 describe 명령으로 이벤트를 조회한다.

```bash
$ kubectl describe pod liveness-exec
...
Events:
  Type     Reason     Age              From               Message
  ----     ------     ----             ----               -------
  Normal   Scheduled  39s              default-scheduler  Successfully assigned default/liveness-exec to gke-cluster-1-default-pool-35aca696-bth0
  Normal   Pulling    38s              kubelet            Pulling image "k8s.gcr.io/busybox"
  Normal   Pulled     37s              kubelet            Successfully pulled image "k8s.gcr.io/busybox"
  Normal   Created    37s              kubelet            Created container liveness
  Normal   Started    37s              kubelet            Started container liveness
  Warning  Unhealthy  2s (x2 over 7s)  kubelet            Liveness probe failed: cat: can't open '/tmp/healthy': No such file or directory '
(이 뒤에 Killing)

...
5분 후 다시 조회
Events:
  Type     Reason     Age                    From               Message
  ----     ------     ----                   ----               -------
  Normal   Scheduled  5m24s                  default-scheduler  Successfully assigned default/liveness-exec to gke-cluster-1-default-pool-35aca696-bth0
  Normal   Pulled     2m56s (x3 over 5m22s)  kubelet            Successfully pulled image "k8s.gcr.io/busybox"
  Normal   Created    2m56s (x3 over 5m22s)  kubelet            Created container liveness
  Normal   Started    2m56s (x3 over 5m22s)  kubelet            Started container liveness
  Warning  Unhealthy  2m12s (x9 over 4m52s)  kubelet            Liveness probe failed: cat: can't open '/tmp/healthy': No such file or directory
  Normal   Killing    2m12s (x3 over 4m42s)  kubelet            Container liveness failed liveness probe, will be restarted
  Normal   Pulling    101s (x4 over 5m23s)   kubelet            Pulling image "k8s.gcr.io/busybox"
```

get pod을 실행하면 리스타트한 횟수도 확인할 수 있다.

```bash
$ kubectl get pod
NAME                  READY   STATUS             RESTARTS   AGE
liveness-exec         0/1     CrashLoopBackOff   5          8m26s
```

## 라이브네스 HTTP

테스트할 서버를 구성하는 코드를 확인해보자.

started = 컨테이너가 시작한 시간을 기록하는 전역 변수

duration = 컨테이너가 시작한 뒤 얼마나 흘렀는지 저장하는 변수

시간이 10초가 지나면 500을 반환하고 그외에는 200을 반환한다.

```go
http.HandleFunc("/healthz", func(w http.ResponseWriter, r *http.Request) {
    duration := time.Now().Sub(started)
    if duration.Seconds() > 10 {
        w.WriteHeader(500) // 응답 코드
        w.Write([]byte(fmt.Sprintf("error: %v", duration.Seconds())))
    } else {
        w.WriteHeader(200) // 응답 코드
        w.Write([]byte("ok"))
    }
})
```

테스트 컨테이너를 동작시키고 디스크라이브를 확인해 우리가 예측한 예측한 시나리오 대로 동작하는지 확인해보자.

```go
kubectl apply -f https://k8s.io/examples/pods/probe/http-liveness.yaml
```

describe 명령으로 이벤트를 확인한다.

```bash
$ kubectl describe pod/liveness-http
...

Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  43s               default-scheduler  Successfully assigned default/liveness-http to gke-cluster-1-default-pool-35aca696-3z74
  Normal   Pulling    4s (x3 over 41s)  kubelet            Pulling image "k8s.gcr.io/liveness"
  Normal   Pulled     4s (x3 over 41s)  kubelet            Successfully pulled image "k8s.gcr.io/liveness"
  Normal   Created    4s (x3 over 41s)  kubelet            Created container liveness
  Warning  Unhealthy  4s (x6 over 28s)  kubelet            Liveness probe failed: HTTP probe failed with statuscode: 500
  Normal   Killing    4s (x2 over 22s)  kubelet            Container liveness failed liveness probe, will be restarted
  Normal   Started    3s (x3 over 40s)  kubelet            Started container liveness
```

# 레이블과 셀렉터

포드 yaml 파일에 레이블 설정하기

- metadata.labels 부분에 설정할 수 있다.

```yaml
# http-go-v2-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: http-go-v2
  labels:
    creation_method: manual
    env: prod
spec:
  containers:
  - image: gasbugs/http-go
    name: http-go
    ports:
    - containerPort: 8080
      protocol: TCP
```

```yaml
kubectl create -f ~/gke-20210510/http-go-v2-pod.yaml
```

## 레이블 조회하기

전체 레이블 조회

```yaml
**$ kubectl get pod --show-labels**
NAME                  READY   STATUS             RESTARTS   AGE     LABELS
http-go               1/1     Running            0          121m    <none>
http-go-v2            1/1     Running            0          40s     creation_method=manual,env=prod
jenkins-manual        1/1     Running            0          80m     <none>
jk-67755cd86b-qj6v2   1/1     Running            0          3h34m   app=jk,pod-template-hash=67755cd86b
liveness-exec         0/1     CrashLoopBackOff   15         44m     test=liveness
liveness-http         0/1     CrashLoopBackOff   13         27m     test=liveness
```

특정 레이블을 조회

```yaml
**$ kubectl get pod -L creation_method**
NAME                  READY   STATUS             RESTARTS   AGE     CREATION_METHOD
http-go               1/1     Running            0          122m
http-go-v2            1/1     Running            0          72s     manual
jenkins-manual        1/1     Running            0          80m
jk-67755cd86b-qj6v2   1/1     Running            0          3h34m
liveness-exec         0/1     CrashLoopBackOff   15         44m
liveness-http         0/1     CrashLoopBackOff   13         28m
```

필터링해서 조회하기

```yaml
**$ kubectl get pod -l creation_method=manual --show-labels**
NAME         READY   STATUS    RESTARTS   AGE     LABELS
http-go-v2   1/1     Running   0          2m21s   creation_method=manual,env=prod
```

## 레이블 추가, 수정, 삭제

추가하기

```yaml
kubectl label pod http-go-v2 test=foo
```

변경하기

```yaml
kubectl label pod http-go-v2 test=poo --overwrite
```

삭제하기

```yaml
kubectl label pod http-go-v2 test-
```

## 연습문제

1. YAML 파일을 사용하여 app=nginx 레이블을 가진 포드를 생성하라.

    ```yaml
    # nginx-pod.yaml
    # kubectl create -f nginx-pod.yaml
    # kubectl get pod -w
    apiVersion: v1
    kind: Pod
    metadata:
      name: nx
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
    ```

2. app=nginx를 가진 포드를 get하라.

    ```bash
    $ kubectl get pod -l app=nginx
    NAME   READY   STATUS    RESTARTS   AGE
    nx     1/1     Running   0          2m16s
    ```

3. get된 포드의 레이블의 app을 확인하라.

    ```yaml
    $ kubectl get pod -l app=nginx -L app
    NAME   READY   STATUS    RESTARTS   AGE     APP
    nx     1/1     Running   0          2m56s   **nginx**
    ```

4. app=nginx 레이블을 가진 포드에 team=dev1 레이블을 추가하라.

    ```bash
    # 원하는 포드에 레이블 추가
    $ kubectl label pod -l app=nginx team=dev1

    # 잘 추가됐는지 확인
    $ kubectl get pod --show-labels
    NAME   READY   STATUS    RESTARTS   AGE    LABELS
    nx     1/1     Running   0          4m9s   app=nginx,team=dev1
    ```
    
-------------------------------------------------------------------------------------------------------------------

- 목차

# 레플리카셋

[https://kubernetes.io/ko/docs/concepts/workloads/controllers/replicaset/](https://kubernetes.io/ko/docs/concepts/workloads/controllers/replicaset/)

## 레플리카셋 작성 요령

레플리카셋 http-go-rs.yaml

```yaml
# http-go-rs.yaml
# kubectl create -f http-go-rs.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: http-go
spec:
  replicas: 3
  selector:
    matchLabels:
      app: http-go
  template:
    metadata:
      labels:
        app: http-go
    spec:
      containers:
      - name: http-go
        image: gasbugs/http-go
```

포드 정보 질의하기

```yaml
$ kubectl get pod
NAME            READY   STATUS    RESTARTS   AGE
http-go-2447z   1/1     Running   0          40s
http-go-9wdhw   1/1     Running   0          40s
http-go-c6cnb   1/1     Running   0          40s
nx              1/1     Running   0          21m
```

rs 정보 질의 하기

```yaml
$ kubectl get rs
NAME      DESIRED   CURRENT   READY   AGE
http-go   3         3         3       74s
```

## 포드를 유지하는 기능 확인하기

직접 포드를 삭제하고 복구되는지 테스트해보자.

```yaml
$ kubectl delete pod http-go-2447z
pod "http-go-**2447z**" deleted

$ kubectl get pod
NAME            READY   STATUS    RESTARTS   AGE
**http-go-7f4tc   1/1     Running   0          9s**
http-go-9wdhw   1/1     Running   0          2m52s
http-go-c6cnb   1/1     Running   0          2m52s
nx              1/1     Running   0          24m
```

관리 레이블 변경해서 포드 격리하기

```yaml
# app 레이블을 다른 것으로 변경
kubectl label pod **http-go-7f4tc** app=bug --overwrite

# APP 레이블 확인
$ kubectl get pod -L app
NAME            READY   STATUS    RESTARTS   AGE     APP
http-go-5vpg4   1/1     Running   0          22s     http-go
http-go-7f4tc   1/1     Running   0          5m33s   bug
http-go-9wdhw   1/1     Running   0          8m16s   http-go
http-go-c6cnb   1/1     Running   0          8m16s   http-go
```

## 레플리카셋 스케일하기

rs를 스케일하는 세 가지 방법

- scale

    ```yaml
    kubectl scale rs http-go --replicas=5
    ```

- apply

    http-go-rs.yaml → http-go-v2-rs.yaml (replicas 변경)

    ```yaml
    kubectl apply -f http-go-v2-rs.yaml
    ```

- edit

    ```yaml
    kubectl edit rs http-go (vim)
    ```

# 디플로이먼트

## 기본 연습문제

1. jenkins 디플로이먼트를 deploy-jenkins를 생성하라.
2. jenkins 디플로이먼트로 배포되는 앱을 app: jenkins-test로 레이블링하라.

    ```yaml
    # deploy-jenkins.yaml
    # kubectl create -f deploy-jenkins.yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: deploy-jenkins
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: jenkins-test
      template:
        metadata:
          labels:
            app: jenkins-test
        spec:
          containers:
          - name: jenkins
            image: jenkins/jenkins:lts
            ports:
            - containerPort: 8080
    ```

3. 디플로이먼트로 배포된 포드를 하나 삭제하고 이후 생성되는 포드를 관찰하라.

    ```yaml
    $ kubectl delete pod deploy-jenkins-6996c6559c-4tmhf
    pod "deploy-jenkins-6996c6559c-4tmhf" deleted

    $ kubectl get pod
    NAME                              READY   STATUS        RESTARTS   AGE
    deploy-jenkins-6996c6559c-4tmhf   0/1     Terminating   0          2m39s
    deploy-jenkins-6996c6559c-v26hf   1/1     Running       0          2s
    deploy-jenkins-6996c6559c-vrtq5   1/1     Running       0          2m39s
    deploy-jenkins-6996c6559c-x82vs   1/1     Running       0          2m39s
    ```

4. 새로 생성된 포드의 레이블을 바꾸어 Deployment의 관리 영역에서 벗어나게 하라.

    ```bash
    $ kubectl label pod deploy-jenkins-6996c6559c-v26hf app- # 레이블 제거

    $ kubectl get pod -L app
    NAME                              READY   STATUS    RESTARTS   AGE     APP
    deploy-jenkins-6996c6559c-v26hf   1/1     Running   0          93s
    deploy-jenkins-6996c6559c-vrtq5   1/1     Running   0          4m10s   jenkins-test
    deploy-jenkins-6996c6559c-x82vs   1/1     Running   0          4m10s   jenkins-test
    deploy-jenkins-6996c6559c-xmr2d   1/1     Running   0          7s      jenkins-test
    ```

5. Scale 명령을 사용해 레플리카 수를 5개로 정의한다.

    ```bash
    $ kubectl scale deploy deploy-jenkins --replicas=5

    $ kubectl get pod -l app=jenkins-test
    NAME                              READY   STATUS    RESTARTS   AGE
    deploy-jenkins-6996c6559c-qnvm6   1/1     Running   0          18s
    deploy-jenkins-6996c6559c-stbbv   1/1     Running   0          18s
    deploy-jenkins-6996c6559c-vrtq5   1/1     Running   0          5m26s
    deploy-jenkins-6996c6559c-x82vs   1/1     Running   0          5m26s
    deploy-jenkins-6996c6559c-xmr2d   1/1     Running   0          83s
    ```

6. edit 기능을 사용하여 10로 스케일링하라.

    ```bash
    $ kubectl edit deploy deploy-jenkins
    (spec.replicas를 찾아서 10으로 수정하고 :wq를 입력한다)

    $ kubectl get pod -l app=jenkins-test
    NAME                              READY   STATUS    RESTARTS   AGE
    deploy-jenkins-6996c6559c-4kggw   1/1     Running   0          8s
    deploy-jenkins-6996c6559c-546df   1/1     Running   0          7s
    deploy-jenkins-6996c6559c-55szq   1/1     Running   0          8s
    deploy-jenkins-6996c6559c-gnd86   1/1     Running   0          8s
    deploy-jenkins-6996c6559c-qnvm6   1/1     Running   0          85s
    deploy-jenkins-6996c6559c-stbbv   1/1     Running   0          85s
    deploy-jenkins-6996c6559c-vrtq5   1/1     Running   0          6m33s
    deploy-jenkins-6996c6559c-x82vs   1/1     Running   0          6m33s
    deploy-jenkins-6996c6559c-xmr2d   1/1     Running   0          2m30s
    deploy-jenkins-6996c6559c-zz7l9   1/1     Running   0          7s
    ```

## 디플로이먼트 업데이트 준비

모든 리소스를 삭제한다.

```yaml
kubectl delete all --all
```

업데이트를 진행할 디플로이먼트를 만든다. (—record=true)

```yaml
# http-go-v1-deploy.yaml
# kubectl apply -f http-go-v1-deploy.yaml --record=true
apiVersion: apps/v1
kind: Deployment
metadata:
  name: http-go
spec:
  replicas: 3
  selector:
    matchLabels:
      app: http-go
  template:
    metadata:
      labels:
        app: http-go
    spec:
      containers:
      - name: http-go
        image: gasbugs/http-go:v1
        ports:
        - containerPort: 8080
```

히스토리에 잘 기록됐는지 확인하기

```yaml
kubectl rollout history deploy http-go
```

10초의 딜레이를 갖도록 레디 타임을 수정

```bash
kubectl patch deploy http-go -p '{"spec": {"minReadySeconds":10}}'
```

디플로이먼트를 서비스할 로드밸런서 생성

```bash
kubectl expose deploy http-go --port=80 --target-port=8080 --type=LoadBalancer
```

생성된 서비스의 IP 확인

```yaml
$ kubectl get svc -w
NAME         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
http-go      LoadBalancer   10.12.13.56   <pending>       80:30962/TCP   9s
kubernetes   ClusterIP      10.12.0.1     <none>          443/TCP        3m21s
http-go      LoadBalancer   10.12.13.56   34.75.201.189   80:30962/TCP   47s
```

모니터링 스크립트 실행

```bash
while true; do curl 34.75.201.189; sleep 1; done;
```

## 업데이트 실행하기

업데이트 방법

- set image 명령

    ```bash
    kubectl set image deploy <deploy_name> <container_name>=<image_name>

    kubectl set image deploy http-go http-go=gasbugs/http-go:v2 --record=true
    ```

- apply 명령

    ```bash
    kubectl apply -f http-go-v2-deploy.yaml --record=true
    ```

- edit 명령

    ```yaml
    kubectl edit deploy http-go
    (변경하기 원하는 이미지 이름으로 직접 변경하고 :wq)
    ```

첫 번째 업데이트

```bash
kubectl set image deploy http-go http-go=gasbugs/http-go:v2 --record=true
```

업데이트 후 히스토리를 확인한다.

```bash
$ kubectl rollout history deploy http-go
deployment.apps/http-go
REVISION  CHANGE-CAUSE
1         kubectl apply --filename=http-go-v1-deploy.yaml --record=true
2         kubectl set image deploy http-go http-go=gasbugs/http-go:v2 --record=true
```

rs을 확인하면 두 개가 생성돼 있다.

```bash
$ kubectl get rs
NAME                 DESIRED   CURRENT   READY   AGE
http-go-7f47944bdb   3         3         3       2m18s   # v2
http-go-d5498847     0         0         0       13m     # v1
```

두 번째 업데이트

http-go-v3-deploy.yaml 파일을 준비하고 다음 명령을 실행한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f7a3c281-18d8-4290-8ad0-afc543a62e3f/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f7a3c281-18d8-4290-8ad0-afc543a62e3f/Untitled.png)

```bash
kubectl apply -f http-go-v3-deploy.yaml --record=true
```

현재 업데이트 방식 확인하기

```bash
$ kubectl get deploy http-go -o yaml
...
spec:
  minReadySeconds: 10
  progressDeadlineSeconds: 600 # 업데이트 데드라인(데드라인 후에는 롤백 수행)
  replicas: 3
  revisionHistoryLimit: 10 # 히스토리 리미트
  selector:
    matchLabels:
      app: http-go
  strategy: # 전략
    rollingUpdate: 
      maxSurge: 25% # 최대 125% 생성
      maxUnavailable: 25% # 최소 75% 유지
    type: RollingUpdate # 롤링업데이트 사용
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: http-go
    spec:
      containers:
      - image: gasbugs/http-go:v3
        imagePullPolicy: IfNotPresent
...
```

## 롤백 수행하기

이전 버전으로 롤백 수행하기

```bash
kubectl rollout undo deploy http-go
```

리비전 넘버가 다음과 같이 변경된다 (2번이 4번이 된다)

```bash
$ kubectl rollout history deploy http-go
deployment.apps/http-go
REVISION  CHANGE-CAUSE
1         kubectl apply --filename=http-go-v1-deploy.yaml --record=true
3         kubectl apply --filename=http-go-v3-deploy.yaml --record=true
4         kubectl set image deploy http-go http-go=gasbugs/http-go:v2 --record=true
```

특정 리비전으로 롤백 수행하기

```bash
kubectl rollout undo deploy http-go --to-revision=1
```

## 업데이트 연습문제

1. 다음 mongo 이미지를 사용하여 업데이트와 롤백을 실행하라.

    ```yaml
    # mongo-4.2-deploy.yaml
    # kubectl apply -f mongo-4.2-deploy.yaml --record=true
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: mongo-deployment
      labels:
        app: mongo
    spec:
      strategy: # 전략
        rollingUpdate: 
          maxSurge: 50% # 최대 150% 생성
          maxUnavailable: 50% # 최소 50% 유지
        type: RollingUpdate # 롤링업데이트 사용
      replicas: 10
      selector:
        matchLabels:
          app: mongo
      template:
        metadata:
          labels:
            app: mongo
        spec:
          containers:
          - name: mongo
            image: mongo:4.2
    ```

2. 모든 revision 내용은 기록돼야 한다.
3. mongo:4.2 이미지를 사용하여 deployment를 생성하라.
    - Replicas: 10
    - maxSurge: 50%
    - maxUnavailable: 50%
4. mongo:4.4 롤링 업데이트를 수행하라.

    [https://kubernetes.io/ko/docs/reference/kubectl/jsonpath/](https://kubernetes.io/ko/docs/reference/kubectl/jsonpath/)

    ```yaml
    # 현재 사용중인 이미지 확인하기
    kubectl get pod -o=jsonpath='{..image}'
    ```

    ```yaml
    kubectl set image deploy mongo-deployment mongo=mongo:4.4 --record=true
    ```

5. mongo:4.2로 롤백을 수행하라.

    ```yaml
    $ kubectl rollout history deployment mongo-deployment

    deployment.apps/mongo-deploymentREVISION  CHANGE-CAUSE
    1         kubectl apply --filename=mongo-4.2-deploy.yaml --record=true
    2         kubectl set image deploy mongo-deployment mongo=mongo:4.4 --record=true
    ```

    ```yaml
    kubectl rollout undo deploy mongo-deployment
    ```

# 네임스페이스

## 네임스페이스 생성하기

kubectl 명령으로 생성하기

```yaml
kubectl create ns dev1
```

네임스페이스 목록 조회

```yaml
$ kubectl get ns
NAME              STATUS   AGE
default           Active   4h22m
dev1              Active   7s
kube-node-lease   Active   4h22m
kube-public       Active   4h22m
kube-system       Active   4h22m
```

yaml 파일 생성하기

```yaml
kubectl create ns dev2 --dry-run=client -o yaml > dev2-ns.yaml
```

## 네임스페이스에서 질의하기

특정 네임스페이스 질의하는 경우에는 -n 옵션을 사용한다.

```yaml
kubectl get pod -n dev1
```

모든 네임스페이스에 질의하기

```yaml
kubectl get pod -A
(-A 대신에 --all-namespaces를 사용해도 된다.)
```

## 네임스페이스 포드 할당하기

특정 네임스페이스에 포드를 생성하는 방법

```yaml
# dev1-http-go-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: http-go
  namespace: dev1 # 네임스페이스에 대한 정보를 넣는다.
spec:
  containers:
  - image: gasbugs/http-go
    name: http-go
    ports:
    - containerPort: 8080
      protocol: TCP
```

yaml 파일을 사용해 포드를 생성하고 dev1 네임스페이스에서 포드를 확인한다.

```yaml
**$ kubectl create -f dev1-http-go-pod.yaml**
pod/http-go created

**$ kubectl get pod -n dev1**
NAME      READY   STATUS    RESTARTS   AGE
http-go   1/1     Running   0          9s
```

# 서비스

[https://kubernetes.io/docs/concepts/services-networking/service/](https://kubernetes.io/docs/concepts/services-networking/service/)

## 클러스터IP

지금까지 작성한 리소스를 정리하자.

```yaml
kubectl delete all --all
```

서비스할 컨테이너와 컨테이너를 지원하는 서비스를 만들자.

```yaml
# http-go-deploy-svc.yaml
# kubectl create -f http-go-deploy-svc.yaml
# 서비스
apiVersion: v1
kind: Service
metadata:
  name: http-go
spec:
  selector:
    app: http-go
  ports:
    - protocol: TCP
      port: 80          # service의 포트
      targetPort: 8080  # container의 포트 (포드)
---
# 디플로이먼트
apiVersion: apps/v1
kind: Deployment
metadata:
  name: http-go
  labels:
    app: http-go
spec:
  replicas: 3
  selector:
    matchLabels:
      app: http-go
  template:
    metadata:
      labels:
        app: http-go
    spec:
      containers:
      - name: http-go
        image: gasbugs/http-go
        ports:
        - containerPort: 8080
```

http-go를 서비스하는 svc 확인

- type을 명시하지 않으면 default로 Cluster IP가 된다.
- 내부 서비스 용이기 때문에 외부 IP를 갖지 않는다. 노드에 대한 포트도 갖지 않는다.
- 하지만 포드 네트워크에서 사용할 수 있는 내부 IP와 포트 번호를 갖는다.

```yaml
$ kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
http-go      ClusterIP   10.12.3.9    <none>        80/TCP    27s
kubernetes   ClusterIP   10.12.0.1    <none>        443/TCP   5m19s
```

포드 하나를 사용해 서비스로 요청을 수행해보자.

```yaml
kubectl exec <pod_name> -- curl 10.12.3.9:80 -s

kubectl exec http-go-669ddfd48-54zws -- curl 10.12.3.9:80 -s
```

Cluster IP도 로드밸런싱하는 기능을 갖고 있다.

```yaml
$ kubectl exec http-go-669ddfd48-54zws -- curl 10.12.3.9:80 -s
Welcome! http-go-669ddfd48-54zws

$ kubectl exec http-go-669ddfd48-54zws -- curl 10.12.3.9:80 -s
Welcome! http-go-669ddfd48-9fmct

$ kubectl exec http-go-669ddfd48-54zws -- curl 10.12.3.9:80 -s
Welcome! http-go-669ddfd48-hx7bg
```

describe 명령을 사용하면 어떤 포드로 연결될지 엔드포인트를 확인할 수 있다.

```yaml
$ kubectl describe svc http-go
Name:              http-go
Namespace:         default
Labels:            <none>
Annotations:       cloud.google.com/neg: {"ingress":true}
Selector:          app=http-go
Type:              ClusterIP
IP Families:       <none>
IP:                10.12.3.9
IPs:               <none>
Port:              <unset>  80/TCP
TargetPort:        8080/TCP
Endpoints:         10.8.0.45:8080,10.8.0.46:8080,10.8.1.39:8080
Session Affinity:  None
Events:            <none>
```

Endpoints에 나와있는 IP는 앞서 생성한 포드의 IP와 일치한다.

```yaml
$ kubectl get pod -o wide
NAME                      READY   STATUS    RESTARTS   AGE     IP          NODE                                       NOMINATED NODE   READINESS GATES
http-go-669ddfd48-54zws   1/1     Running   0          7m24s   **10.8.0.46**   gke-cluster-1-default-pool-3e0866f0-zjcc   <none>           <none>
http-go-669ddfd48-9fmct   1/1     Running   0          7m24s   **10.8.1.39**   gke-cluster-1-default-pool-3e0866f0-4t9j   <none>           <none>
http-go-669ddfd48-hx7bg   1/1     Running   0          7m24s   **10.8.0.45**   gke-cluster-1-default-pool-3e0866f0-zjcc   <none>           <none>
```

## 노드포트

노드 포트 작성하기

```yaml
# http-go-np.yaml
# kubectl create -f http-go-np.yaml
# 서비스
apiVersion: v1
kind: Service
metadata:
  name: http-go-np
spec:
  type: NodePort
  selector:
    app: http-go
  ports:
    - protocol: TCP
      port: 80          # service의 포트
      targetPort: 8080  # container의 포트 (포드)
      nodePort: 30001   # node에 오픈할 포트 
```

노드 정보 확인하기

```yaml
$ kubectl get svc
NAME         TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
http-go      ClusterIP   10.12.3.9     <none>        80/TCP         37m
**http-go-np   NodePort    10.12.6.186   <none>        80:30001/TCP   3s**
kubernetes   ClusterIP   10.12.0.1     <none>        443/TCP        42m
```

VPC의 방화벽에 30001번 포트 접속 허용하기

```yaml
gcloud config set project gasbugs-gke-20210510 # 프로젝트 선택하는 명령어
gcloud compute firewall-rules create http-go --allow=tcp:30001
```

노드 아이피 확인하기

```yaml
$ kubectl get nodes -o wide
NAME                                       STATUS   ROLES    AGE     VERSION            INTERNAL-IP   EXTERNAL-IP      OS-IMAGE          KERNEL-VERSION   CONTAINER-RUNTIME
gke-cluster-1-default-pool-3e0866f0-3nds   Ready    <none>   5h27m   v1.18.17-gke.100   10.142.0.7    35.231.32.221    Container-Optimized OS from Google   5.4.89+          docker://19.3.14
gke-cluster-1-default-pool-3e0866f0-4t9j   Ready    <none>   5h27m   v1.18.17-gke.100   10.142.0.6    34.74.236.112    Container-Optimized OS from Google   5.4.89+          docker://19.3.14
gke-cluster-1-default-pool-3e0866f0-zjcc   Ready    <none>   5h27m   v1.18.17-gke.100   10.142.0.5    35.229.103.100   Container-Optimized OS from Google   5.4.89+          docker://19.3.14
```

노드에 요청을 수행해보자.

```bash
curl 35.231.32.221:30001
curl 34.74.236.112:30001
curl 35.229.103.100:30001
```

## 로드밸런서 생성하기

yaml 파일 작성

```bash
# http-go-lb.yaml
# kubectl create -f http-go-lb.yaml
# 서비스
apiVersion: v1
kind: Service
metadata:
  name: http-go-lb
spec:
  type: LoadBalancer
  selector:
    app: http-go
  ports:
    - protocol: TCP
      port: 80          # service의 포트
      targetPort: 8080  # container의 포트 (포드)
```

로드밸런서의 IP는 1~2분후에 받아진다.

```bash
$ kubectl get svc
NAME         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
http-go-lb   LoadBalancer   10.12.1.186   34.75.201.189   80:30574/TCP   49s
```

GCP에서 "네트워크 서비스"를 타이핑해서 부하분산 메뉴를 찾는다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2e95d751-44d5-42f9-80ca-9ecd9fc43969/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2e95d751-44d5-42f9-80ca-9ecd9fc43969/Untitled.png)

부하분산기가 하나 배포돼있는데 이 분산기가 우리가 만든 LoadBalancer에 의해서 생성된 것이다. 백엔드로 연결된 내용을 확인하면 우리가 사용하는 클러스터의 노드들이 연결된 모습을 확인할 수 있다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/55cb695b-02d1-4a7b-83f5-70956efcec62/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/55cb695b-02d1-4a7b-83f5-70956efcec62/Untitled.png)

## 서비스 연습문제

1. tomcat을 노드포트로 서비스하기(30002번 포트 사용) 

    이미지는 consol/tomcat-7.0을 사용하도록 한다.

2. tomcat을 로드밸런스로 서비스하기(80번 포트 사용)

```yaml
# tomcat-deploy-np-lb.yaml
# kubectl create -f tomcat-deploy-np-lb.yaml
# 노드포트
apiVersion: v1
kind: Service
metadata:
  name: tomcat-np
spec:
  type: NodePort
  selector:
    app: tomcat
  ports:
    - protocol: TCP
      port: 80          # service의 포트
      targetPort: 8080  # container의 포트 (포드)
      nodePort: 30002   # 노드에 오픈되는 포트
---
# 로드밸런서
apiVersion: v1
kind: Service
metadata:
  name: tomcat-lb
spec:
  type: LoadBalancer
  selector:
    app: tomcat
  ports:
    - protocol: TCP
      port: 80          # service의 포트
      targetPort: 8080  # container의 포트 (포드)
---
# 디플로이먼트
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat
  labels:
    app: tomcat
spec:
  replicas: 3
  selector:
    matchLabels:
      app: tomcat
  template:
    metadata:
      labels:
        app: tomcat
    spec:
      containers:
      - name: tomcat
        image: consol/tomcat-7.0
        ports:
        - containerPort: 8080
```

VPC의 방화벽에 30002번 포트 접속 허용하기

```yaml
gcloud config set project gasbugs-gke-20210510 # 프로젝트 선택하는 명령어
gcloud compute firewall-rules create tomcat --allow=tcp:30002
```

## ingress

GKE에서 인그레스를 구성하려면 노드포트가 필요하다.

```yaml
kubectl apply -f http-go-deploy-svc.yaml
kubectl apply -f http-go-np.yaml
kubectl delete svc http-go
kubectl delete svc tomcat-lb

$ kubectl get svc
NAME         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
http-go-np   NodePort       10.12.9.165   <none>          80:30001/TCP   48s
kubernetes   ClusterIP      10.12.0.1     <none>          443/TCP        24m
tomcat-np    NodePort       10.12.0.252   <none>          80:30002/TCP   23m
```

인그레스 생성하기

```yaml
# ingress-http-go.yaml
# kubectl create -f ingress-http-go.yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: ingress
spec:
  rules:
  - host: http-go.gasbugs.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          serviceName: http-go-np
          servicePort: 80
```

서비스 및 수신 - 인그레스에 가면 인그레스가 생성중인 정보를 확인할 수 있다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/21cfb79f-b9ce-49a5-99b8-330b9f5b8b7b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/21cfb79f-b9ce-49a5-99b8-330b9f5b8b7b/Untitled.png)

kubectl에서도 인그레스에 대한 정보를 확인할 수 있다.

```yaml
$ kubectl get ingress -w
NAME      CLASS    HOSTS                 ADDRESS         PORTS   AGE
ingress   <none>   http-go.gasbugs.com   34.117.108.18   80      4m7s
```

/etc/hosts를 수정하면 임의로 도메인 질의에 대한 정보를 넣을 수 있다.

```yaml
sudo vim /etc/hosts

마지막 라인에 다음 내용 추가
34.117.108.18     http-go.gasbugs.com 

```

다음 명령어로 ALB에 요청을 수행한다.

```yaml
$ curl http-go.gasbugs.com
Welcome! http-go-669ddfd48-9w5j8

$ curl 34.117.108.18
response 404 (backend NotFound), service rules for the path non-existent
```

## 연습문제

톰캣 서비스로 연결되는 ingress룰을 추가하고 다시 ingress를 실행해서 톰캣과 http-go를 동시에 서비스하도록 만들어라. tomcat.gasbugs.com

```yaml
# ingress-http-go-tomcat.yaml
# kubectl create -f ingress-http-go-tomcat.yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: ingress-ht
spec:
  rules:
  - host: http-go.gasbugs.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          serviceName: http-go-np
          servicePort: 80
  - host: tomcat.gasbugs.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          serviceName: tomcat-np
          servicePort: 80
```

```yaml
$ kubectl get ingress
NAME         CLASS    HOSTS                                    ADDRESS         PORTS   AGE
ingress      <none>   http-go.gasbugs.com                      34.117.108.18   80      22m
ingress-ht   <none>   http-go.gasbugs.com,tomcat.gasbugs.com   **34.117.244.79**   80      4m42s
```

/etc/hosts 마지막 라인에 다음라인을 추가한다.

```yaml
**34.117.244.79**   http-go.gasbugs.com
**34.117.244.79**   tomcat.gasbugs.com
```

5분~10분 후에 다음과 같은 결과를 확인할 수 있다.

```yaml
$ curl http-go.gasbugs.com -s
Welcome! http-go-669ddfd48-v4n4w

$ curl tomcat.gasbugs.com -s
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>Apache Tomcat/7.0.62</title>
```

nginx-ingress 사용하기

[https://blog.naver.com/isc0304/221888916772](https://blog.naver.com/isc0304/221888916772)

# 쿠버네티스 네트워크 이해

instance-1으로 접속해서 실습을 진행한다.

ifconfig와 ip addr 명령을 실행하려면 다음 패키지를 설치해야 한다.

```yaml
sudo apt install net-tools -y
```

pause 이미지를 사용하고 있는 컨테이너를 조사하자.

```yaml
$ sudo docker ps -a | grep pause
af8d69ad31eb   k8s.gcr.io/pause:3.4.1   "/pause"                 7 hours ago    Up 7 hours                                           k8s_POD_weave-net-ctlp5_kube-system_34413dbe-1136-4ee2-b6f0-717fdeeafe24_1
1e3d60835faf   k8s.gcr.io/pause:3.4.1   "/pause"                 7 hours ago    Up 7 hours                                           k8s_POD_kube-proxy-nsfph_kube-system_8ab50774-1309-473a-b591-76eb4c72f818_1
e4ff97e54903   k8s.gcr.io/pause:3.4.1   "/pause"                 7 hours ago    Up 7 hours                                           k8s_POD_kube-controller-manager-instance-1_kube-system_12e64f7b86c9ba21b17a5ebdb303785d_1
271577e2a202   k8s.gcr.io/pause:3.4.1   "/pause"                 7 hours ago    Up 7 hours                                           k8s_POD_kube-apiserver-instance-1_kube-system_b137928019a844c63ec3811394a9bdb6_1
61c98eafcf5b   k8s.gcr.io/pause:3.4.1   "/pause"                 7 hours ago    Up 7 hours                                           k8s_POD_etcd-instance-1_kube-system_c42c492a7d986709603d1197838d55d0_1
0a373e651bd5   k8s.gcr.io/pause:3.4.1   "/pause"                 7 hours ago    Up 7 hours                                           k8s_POD_kube-scheduler-instance-1_kube-system_de6ea4984bfa4dbf70147c453c1a1c1e_1
2c083cdc7baa   k8s.gcr.io/pause:3.4.1   "/pause"                 30 hours ago   Exited (0) 23 hours ago                              k8s_POD_weave-net-ctlp5_kube-system_34413dbe-1136-4ee2-b6f0-717fdeeafe24_0
76befa2630e9   k8s.gcr.io/pause:3.4.1   "/pause"                 31 hours ago   Exited (0) 23 hours ago                              k8s_POD_kube-proxy-nsfph_kube-system_8ab50774-1309-473a-b591-76eb4c72f818_0
861a9a20fd14   k8s.gcr.io/pause:3.4.1   "/pause"                 31 hours ago   Exited (0) 23 hours ago                              k8s_POD_kube-controller-manager-instance-1_kube-system_12e64f7b86c9ba21b17a5ebdb303785d_0
4127ebb28eb8   k8s.gcr.io/pause:3.4.1   "/pause"                 31 hours ago   Exited (0) 23 hours ago                              k8s_POD_kube-scheduler-instance-1_kube-system_de6ea4984bfa4dbf70147c453c1a1c1e_0
0a6e3ea81771   k8s.gcr.io/pause:3.4.1   "/pause"                 31 hours ago   Exited (0) 23 hours ago                              k8s_POD_kube-apiserver-instance-1_kube-system_b137928019a844c63ec3811394a9bdb6_0
e51f8cc090d4   k8s.gcr.io/pause:3.4.1   "/pause"                 31 hours ago   Exited (0) 23 hours ago                              k8s_POD_etcd-instance-1_kube-system_c42c492a7d986709603d1197838d55d0_0
```

```yaml
$ sudo docker ps -a | grep kube-apiserver
c6f26bd20f9e   4d217480042e             "kube-apiserver --ad…"   7 hours ago    Up 7 hours                                           k8s_kube-apiserver_kube-apiserver-instance-1_kube-system_b137928019a844c63ec3811394a9bdb6_1
271577e2a202   k8s.gcr.io/pause:3.4.1   "/pause"                 7 hours ago    Up 7 hours                                           k8s_POD_kube-apiserver-instance-1_kube-system_b137928019a844c63ec3811394a9bdb6_1
```

## 포드 간의 통신 이해

각 노드에는 별도의 네트워크 대역대가 할당되며 이 네트워크 간의 통신은 CNI 플러그인인 weavenet이 연결을 담당한다.

```yaml
$ sudo netstat -antp |  grep weave
tcp        0      0 127.0.0.1:6784          0.0.0.0:*               LISTEN      3738/weaver         
tcp        0      0 **10.128.0.2**:47905        **10.128.0.6**:6783         ESTABLISHED 3738/weaver         
tcp        0      0 **10.128.0.2**:45945        **10.128.0.7**:6783         ESTABLISHED 3738/weaver         
tcp        0      0 10.128.0.2:45940        10.96.0.1:443           ESTABLISHED 3671/weave-npc      
tcp6       0      0 :::6781                 :::*                    LISTEN      3671/weave-npc      
tcp6       0      0 :::6782                 :::*                    LISTEN      3738/weaver         
tcp6       0      0 :::6783                 :::*                    LISTEN      3738/weaver
```

## 포드와 서비스 사이의 통신

API 서버의 iptables 정책을 확인해보자. kube-apiserver를 서비스하는 객체를 확인하고 이 ip를 요청했을 떄 어느 방향으로 쫓아가는지 확인해본다.

```yaml
$ kubectl get svc
NAME         TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1     <none>        443/TCP        29h
```

10.96.0.1에 대한 정보를 iptables 정책에서 확인

```yaml
$ sudo iptables -S -t nat | grep 10.96.0.1/32
-A KUBE-SERVICES -d 10.96.0.1/32 -p tcp -m comment --comment "default/kubernetes:https cluster IP" -m tcp --dport 443 -j KUBE-SVC-NPX46M4PTMTKRN6Y
# 목적지가 10.96.0.1:443 tcp 통신을 요청하는 경우에 다음 정책을 따른다. KUBE-SVC-NPX46M4PTMTKRN6Y
```

정책을 따라가 보도록 한다.

```yaml
$ sudo iptables -S -t nat | grep KUBE-SVC-NPX46M4PTMTKRN6Y
-N KUBE-SVC-NPX46M4PTMTKRN6Y
-A KUBE-SERVICES -d 10.96.0.1/32 -p tcp -m comment --comment "default/kubernetes:https cluster IP" -m tcp --dport 443 -j KUBE-SVC-NPX46M4PTMTKRN6Y
***-A KUBE-SVC-NPX46M4PTMTKRN6Y -m comment --comment "default/kubernetes:https" -j KUBE-SEP-XD66YDEGQEOQLZXE

# 위 내용을 따라 다음 정보도 확인해보자.***
$ sudo iptables -S -t nat | grep KUBE-SEP-XD66YDEGQEOQLZXE
-N KUBE-SEP-XD66YDEGQEOQLZXE
-A KUBE-SEP-XD66YDEGQEOQLZXE -s 10.128.0.2/32 -m comment --comment "default/kubernetes:https" -j KUBE-MARK-MASQ
***-A KUBE-SEP-XD66YDEGQEOQLZXE -p tcp -m comment --comment "default/kubernetes:https" -m tcp -j DNAT --to-destination 10.128.0.2:6443
# 목적지를 변경하는 정책 --to-destination 10.128.0.2:6443***
-A KUBE-SVC-NPX46M4PTMTKRN6Y -m comment --comment "default/kubernetes:https" -j KUBE-SEP-XD66YDEGQEOQLZXE
```

결론적으로 이 인터페이스 를 통과하는 패킷중에 10.96.0.1:443으로 가는 트래픽이 있다면 목적지를 10.128.0.2:6443로 변경한다.

## 도메인 네임 서비스 사용하기

쿠버네티스는 내부의 자체 DNS 운영해서 <svc_name>.<namespace>와 같은 형식으로 질의할 수 있도록 서비스를 제공한다.

```yaml
$ kubectl get pod
NAME                      READY   STATUS    RESTARTS   AGE
**http-go-669ddfd48-9w5j8   1/1     Running   0          90m**
http-go-669ddfd48-jv2w8   1/1     Running   0          90m
http-go-669ddfd48-v4n4w   1/1     Running   0          90m
**tomcat-6d67d95d79-gk2vc   1/1     Running   0          113m**
tomcat-6d67d95d79-h698s   1/1     Running   0          113m
tomcat-6d67d95d79-vwhnj   1/1     Running   0          113m

$ kubectl get svc
NAME         TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
http-go-np   NodePort    10.12.9.165   <none>        80:30001/TCP   92m
kubernetes   ClusterIP   10.12.0.1     <none>        443/TCP        116m
tomcat-np    NodePort    10.12.0.252   <none>        80:30002/TCP   115m
```

default 네임스페이스에 있는 톰캣을 사용해서 다른 서비스에 질의해보자.

```yaml
$ kubectl exec tomcat-6d67d95d79-gk2vc -- curl http-go-np -s
Welcome! http-go-669ddfd48-jv2w8

$ kubectl exec tomcat-6d67d95d79-gk2vc -- curl http-go-np.default -s
Welcome! http-go-669ddfd48-jv2w8
```

## DNS 사용 연습문제

1. 네임스페이스 blue에 jenkins 이미지를 사용하는 pod-jenkins 디플로이먼트를 생성하고 이를 위한 서비스 srv-jenkins를 생성하라.

    ```yaml
    # blue-jenkins-deploy.yaml
    # kubectl create -f blue-jenkins-deploy.yaml
    # ns
    apiVersion: v1
    kind: Namespace
    metadata:
      name: blue
    ---
    # deployment
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: pod-jenkins
      name: pod-jenkins
      namespace: blue
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: pod-jenkins
      template:
        metadata:
          labels:
            app: pod-jenkins
        spec:
          containers:
          - image: jenkins/jenkins:lts
            name: jenkins
    ---
    # svc
    apiVersion: v1
    kind: Service
    metadata:
      name: srv-jenkins
      namespace: blue
    spec:
      selector:
        app: pod-jenkins
      ports:
        - protocol: TCP
          port: 80
          targetPort: 8080
    ```

    다음 명령어로 배포됐는지 확인할 수 있다.

    ```yaml
    **$ kubectl get all -n blue**
    NAME                              READY   STATUS    RESTARTS   AGE
    pod/pod-jenkins-ff6f6976c-klznk   1/1     Running   0          26s

    NAME                  TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
    service/srv-jenkins   ClusterIP   10.12.15.191   <none>        80/TCP    26s

    NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/pod-jenkins   1/1     1            1           27s

    NAME                                    DESIRED   CURRENT   READY   AGE
    replicaset.apps/pod-jenkins-ff6f6976c   1         1         1       27s
    ```

    서비스 제거 명령어

    ```yaml
    kubectl delete svc <svc_name>
    ```

2. default 네임스페이스의 http-go 이미지의 curl을 사용하여 pod-jenkins:8080을 요청하라.

    ```yaml
    # http-go-pod.yaml
    # kubectl create -f http-go-pod.yaml
    # 이 디스크립터는 쿠버네티스 API v1를 사용
    apiVersion: v1
    # 리소스 포드에 대한 설명
    kind: Pod
    metadata:
      # 포드의 이름
      name: http-go
    spec:
      containers:
      # 생성할 컨테이너의 컨테이너 이미지
      - image: gasbugs/http-go
        name: http-go
        ports:
        # 응답 대기할 애플리케이션 포트
        - containerPort: 8080
          protocol: TCP
    ```

    이 포드를 생성하고 앞서 생성한 blue 네임스페이스의 jenkins로 요청을 수행한다.

    ```yaml
    kubectl exec http-go -- curl srv-jenkins.blue -s
    ```


-------------------------------------------------------------------------------------------------------------------


# 볼륨

[https://kubernetes.io/docs/concepts/storage/volumes/](https://kubernetes.io/docs/concepts/storage/volumes/)

## EmptyDir

gasbugs/count 이미지와 httpd 이미지를 하나의 포드에 만들어서 각각의 역할을 수행하도록 만들어보자.

- gasbugs/count: 컨텐츠를 10초마다 생성
- httpd: 컨텐츠를 외부사용자에게 서비스

```yaml
# count-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: count
spec:
  containers:
  # 컨텐츠를 10초마다 생성
  - image: gasbugs/count
    name: html-generator
    volumeMounts:
    - mountPath: /var/htdocs
      name: html
  # 컨텐츠를 외부사용자에게 서비스, 80 포트
  - image: httpd
    name: web-server
    volumeMounts:
    - mountPath: /usr/local/apache2/htdocs
      name: html
  volumes:
  - name: html
    emptyDir: {}
```

포드가 정상 동작하는지 확인한다.

```yaml
$ kubectl get pod -w
NAME      READY   STATUS    RESTARTS   AGE
count     2/2     Running   0          11s
```

쿠버네티스 포트포워드 기능을 사용해 가상머신으로 포트를 가져온다.

```yaml
kubectl port-forward count 8888:80
```

클라우드셸 상단의 웹 미리보기 기능을 사용해 웹브라우저로 접속한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/37a1ac50-6396-4793-9a20-cdd629219298/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/37a1ac50-6396-4793-9a20-cdd629219298/Untitled.png)

## hostpath

GKE로 생성한 클러스터에 보면 kube-system에서 fluentbit라는 포드가 떠있는것을 확인할 수 있다.

```yaml
$ kubectl get pod -n kube-system
NAME                                                        READY   STATUS    RESTARTS   AGE
event-exporter-gke-564fb97f9-tflt8                          2/2     Running   0          83m
fluentbit-gke-9g2gz                                         2/2     Running   0          83m
fluentbit-gke-dt4hb                                         2/2     Running   0          83m
fluentbit-gke-q2x2l                                         2/2     Running   0          83m
gke-metrics-agent-66grs                                     1/1     Running   0          83m
```

fluentbit의 세부설정을 확인해보자.

```yaml
$ kubectl get pod fluentbit-gke-9g2gz -o yaml -n kube-system
apiVersion: v1
kind: Pod
metadata:
  ...
spec:
  containers:
    ...
    volumeMounts:
    - mountPath: /var/run/google-fluentbit/pos-files
      name: varrun
    - mountPath: /var/log
      name: varlog
    - mountPath: /var/lib/kubelet/pods
      name: varlibkubeletpods
    - mountPath: /var/lib/docker/containers
      name: varlibdockercontainers
      readOnly: true
    - mountPath: /fluent-bit/etc/
      name: config-volume
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: fluentbit-gke-token-hn6ht
      readOnly: true
  volumes:
  - hostPath:
      path: /var/run/google-fluentbit/pos-files
      type: ""
    name: varrun
  - hostPath:
      path: /var/log
      type: ""
    name: varlog
  - hostPath:
      path: /var/lib/kubelet/pods
      type: ""
    name: varlibkubeletpods
  - hostPath:
      path: /var/lib/docker/containers
      type: ""
    name: varlibdockercontainers
```

# 클라우드 종속적인 디스크 사용

[https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk)

디스크 생성하기

- 각자 자신의 클러스터 가 배포된 'ZONE' 정보를 확인하고 zone 을 변경 후 사용하도록 한다.

```yaml
# 프로젝트를 선택하고 
$ gcloud config set project gasbugs-gke-20210510
Updated property [core/project].

# 디스크 생성
$ gcloud compute disks create --size=100GB --zone=us-east1-b mongodb
```

mongo 컨테이너를 사용하는 pod를 구성해보자.

```yaml
# mongo-pod.yaml
# kubectl create -f mongo-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mongo
spec:
  containers:
  - image: mongo
    name: mongodb
    volumeMounts:
    - mountPath: /data/db
      name: mongo-data
  volumes:
  - name: mongo-data
    gcePersistentDisk:
      pdName: mongodb
      fsType: ext4
```

mongo 컨테이너가 정상동작하는지 확인한다.

```yaml
$ kubectl get pod -w
NAME      READY   STATUS    RESTARTS   AGE
mongo     1/1     Running   0          41s
```

컴퓨트 엔진 - 디스크에 가면 앞서 생성한 디스크의 정보를 확인할 수 있다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/453d4819-3f78-4697-947a-20d194ff82b5/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/453d4819-3f78-4697-947a-20d194ff82b5/Untitled.png)

mongo db 사용 중 표시가 되며 어떤 노드에 어태치도 확인할 수 있다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ccd4fcb3-de08-45a6-81d3-87d2defc7460/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ccd4fcb3-de08-45a6-81d3-87d2defc7460/Untitled.png)

mongo의 디스크가 정말 유지 가능한 디스크인지 확인해보자.

```yaml
$ kubectl exec -it mongo -- mongo # db에 접속

> use mystore; # db 선택
switched to db mystore

> db.foo.insert({name:'test',value:'1234'}); # 데이터 입력
WriteResult({ "nInserted" : 1 })

> db.foo.find() # 데이터 검색
{ "_id" : ObjectId("609c8b8d994854769ebf3687"), "name" : "test", "value" : "1234" }

> exit # 나가기
```

mongo 포드를 삭제하고 다시 생성해보자. 다시 생성한 DB에 데이터가 있는지 확인한다.

```yaml
kubectl delete -f mongo-pod.yaml
kubectl create -f mongo-pod.yaml

$ kubectl exec -it mongo -- mongo # db에 접속

> use mystore; # db 선택

> db.foo.find() # 데이터 검색 (입력 없이 검색만 수행)
```

## PVC와 PV를 활용한 디스크 연결

[https://kubernetes.io/docs/concepts/storage/persistent-volumes/](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)

위 도큐먼트에서 필요한 yaml 파일을 검색해서 가져온다.

```yaml
# mongo-pod-pv-pvc.yaml
# kubectl delete all --all
# kubectl create -f mongo-pod-pv-pvc.yaml
# pod 
apiVersion: v1
kind: Pod
metadata:
  name: mongo
spec:
  containers:
    - name: mongo
      image: mongo
      volumeMounts:
      - mountPath: /data/db
        name: mongo-data
  volumes:
    - name: mongo-data
      persistentVolumeClaim:
        claimName: mongo-pvc
---
# pvc
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongo-pvc
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: "" # 자동화하는 경우에 필요
---
# pv
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mongo-pv
spec:
  capacity:
    storage: 100Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  gcePersistentDisk:
    pdName: mongodb
    fsType: ext4
```

포드가 성공적으로 Running 상태가 되면 모든 동작이 잘 이뤄진다는 의미다.

```yaml
$ kubectl get pod -w
NAME    READY   STATUS    RESTARTS   AGE
mongo   1/1     Running   0          39s

$ kubectl get pvc
NAME        STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mongo-pvc   Bound    mongo-pv   100Gi      RWO                           78s

$ kubectl get pv
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM               STORAGECLASS   REASON   AGE
mongo-pv   100Gi      RWO            Retain           Bound    default/mongo-pvc                           102s

```

디스크 내부 내용이 여전히 있는지 확인

```yaml
$ kubectl exec -it mongo -- mongo # db에 접속

> use mystore; # db 선택

> db.foo.find() # 데이터 검색 (입력 없이 검색만 수행)
```

## 스토리지 클래스

[https://kubernetes.io/docs/concepts/storage/storage-classes/](https://kubernetes.io/docs/concepts/storage/storage-classes/)

GCE PD에 대한 정보 확인

[https://kubernetes.io/docs/concepts/storage/storage-classes/#gce-pd](https://kubernetes.io/docs/concepts/storage/storage-classes/#gce-pd)

GKE에서 기본으로 만들어지는 스토리지 클래스 확인

```yaml
$ kubectl get sc
NAME                 PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
premium-rwo          pd.csi.storage.gke.io   Delete          WaitForFirstConsumer   true                   159m
standard (default)   kubernetes.io/gce-pd    Delete          Immediate              true                   159m
standard-rwo         pd.csi.storage.gke.io   Delete          WaitForFirstConsumer   true                   159m
```

스토리지 클래스 생성 yaml 파일

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: slow
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-standard
  fstype: ext4
  replication-type: none
```

미리 생성된 SC를 사용해서 mongo 포드에 필요한 디스크와 PV를 생성해보자.

```yaml
# mongo-v2-pod-pvc.yaml
# kubectl create -f mongo-v2-pod-pvc.yaml
# pod 
apiVersion: v1
kind: Pod
metadata:
  name: mongo-v2
spec:
  containers:
    - name: mongo
      image: mongo
      volumeMounts:
      - mountPath: /data/db
        name: mongo-data
  volumes:
    - name: mongo-data
      persistentVolumeClaim:
        claimName: mongo-v2-pvc
---
# pvc
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongo-v2-pvc
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
```

```yaml
$ kubectl get pod
NAME       READY   STATUS    RESTARTS   AGE
mongo      1/1     Running   0          15m
mongo-v2   1/1     Running   0          20s

$ kubectl get pvc
NAME           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mongo-pvc      Bound    mongo-pv                                   100Gi      RWO                           15m
mongo-v2-pvc   Bound    pvc-d082d61d-e243-419f-af33-a1f3a45b3188   100Gi      RWO            standard       23s

$ kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                  STORAGECLASS   REASON   AGE
mongo-pv                                   100Gi      RWO            Retain           Bound    default/mongo-pvc                              15m
pvc-d082d61d-e243-419f-af33-a1f3a45b3188   100Gi      RWO            Delete           Bound    default/mongo-v2-pvc   standard                23s

# 프로젝트 선택
$ gcloud config set project gasbugs-gke-20210510

# 디스크 목록 조회
$ gcloud compute disks list
NAME                                                             LOCATION       LOCATION_SCOPE  SIZE_GB  TYPE         STATUS
instance-1                                                       us-central1-a  zone            100      pd-balanced  READY
instance-2                                                       us-central1-a  zone            100      pd-balanced  READY
instance-3                                                       us-central1-a  zone            100      pd-balanced  READY
gke-cluster-1-5413287b-pvc-d082d61d-e243-419f-af33-a1f3a45b3188  us-east1-b     zone            100      pd-standard  READY
gke-cluster-1-default-pool-68d3eed0-4cxr                         us-east1-b     zone            100      pd-standard  READY
gke-cluster-1-default-pool-68d3eed0-d3rw                         us-east1-b     zone            100      pd-standard  READY
gke-cluster-1-default-pool-68d3eed0-jk5t                         us-east1-b     zone            100      pd-standard  READY
mongodb                                                          us-east1-b     zone            100      pd-standard  READ
```

PVC가 사라졌을때 정책에따라 디스크가 삭제되는지 확인해보자.

```yaml
$ kubectl delete -f mongo-v2-pod-pvc.yaml
pod "mongo-v2" deleted
persistentvolumeclaim "mongo-v2-pvc" deleted

$ kubectl get pv
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM               STORAGECLASS   REASON   AGE
mongo-pv   100Gi      RWO            Retain           Bound    default/mongo-pvc                           19m

$ gcloud compute disks list # 디스크 목록에서도 찾을 수 없음
```

# 환경 변수를 활용한 이미지 변수 관리

환경변수가 필요한 예

[https://hub.docker.com/_/mysql](https://hub.docker.com/_/mysql)

[https://hub.docker.com/_/elasticsearch](https://hub.docker.com/_/elasticsearch)

[https://hub.docker.com/r/million12/citadel](https://hub.docker.com/r/million12/citadel) # 웹 메일

## yaml 파일에 하드코딩

다음 yaml 파일 실행하면 컨테이너에 특정한 환경변수를 전달할 수 있다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: envar-demo
  labels:
    purpose: demonstrate-envars
spec:
  containers:
  - name: envar-demo-container
    image: gcr.io/google-samples/node-hello:1.0
    env:
    - name: DEMO_GREETING
      value: "Hello from the environment"
    - name: DEMO_FAREWELL
      value: "Such a sweet sorrow"
```

다음 명령을 실행하고 컨테이너에 실제 환경변수가 잘 선언되는지 확인해보자.

```yaml
kubectl apply -f https://k8s.io/examples/pods/inject/envars.yaml
```

해당 컨테이너에서 printenv를 실행하면 환경변수 목록을 확인할 수 있다.

- printenv <env_name>

```yaml
$ kubectl exec envar-demo -- printenv
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=envar-demo
**DEMO_GREETING=Hello from the environment
DEMO_FAREWELL=Such a sweet sorrow**
KUBERNETES_SERVICE_HOST=10.12.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.12.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.12.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.12.0.1
NPM_CONFIG_LOGLEVEL=info
NODE_VERSION=4.4.2
HOME=/root
```

## configmap 을 사용해 환경 변수 설정하기

파일을 만들어서 configmap을 설정하는것이 가장 쉬운 방법!

- --from-file을 사용해서 configmap에 참조하게 만들면 파일이름은 key로 사용하고 파일 내용은 value로 사용한다.

```yaml
echo -n 1234 > test
kubectl create configmap myconfigmap --from-file=test
```

새롭게 만들어진 configmap을 조회한다.

```yaml
$ kubectl get configmap myconfigmap
NAME          DATA   AGE
myconfigmap   1      16s
```

자세한 내용을 보려면 describe나 yaml 출력을 사용하면 된다.

```yaml
$ kubectl describe configmap myconfigmap
Name:         myconfigmap
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
test:
----
1234
Events:  <none>
```

```yaml
$ kubectl get configmap myconfigmap -o yaml
apiVersion: v1
data:
  test: "1234"
kind: ConfigMap
metadata:
  creationTimestamp: "2021-05-13T04:59:04Z"
  name: myconfigmap
  namespace: default
  resourceVersion: "99379"
  selfLink: /api/v1/namespaces/default/configmaps/myconfigmap
  uid: 8c7fe774-bcdb-4035-9973-a2cc27ff5e6c
```

잘 만들어진 configmap을 컨테이너에서 참조하도록 구성해보자.

```yaml
# envar-configmap-pod.yaml
# kubectl create -f envar-configmap-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: envar-configmap
spec:
  containers:
  - name: envar-demo-container
    image: gcr.io/google-samples/node-hello:1.0
    env:
    - name: DEMO_GREETING
      valueFrom:
        configMapKeyRef:
          name: myconfigmap
          key: test
```

configmap으로부터 환경변수를 잘 받아오는지를 확인해보자.

```yaml
$ kubectl exec envar-configmap -- printenv DEMO_GREETING
1234
```

## secret을 사용해 환경 변수 설정하기

secret으로 만들 민감한 정보를 생성한다.

```yaml
echo -n admin > username
echo -n 1q2w3e4r > password
kubectl create secret generic mysecret --from-file=username --from-file=password
```

저장된 secret을 yaml 파일 형식으로 확인

```yaml
$ kubectl get secret mysecret -o yaml
apiVersion: v1
data:
  password: MXEydzNlNHI=
  username: YWRtaW4=
kind: Secret
metadata:
  creationTimestamp: "2021-05-13T05:10:10Z"
  name: mysecret
  namespace: default
  resourceVersion: "103334"
  selfLink: /api/v1/namespaces/default/secrets/mysecret
  uid: 3970fdf0-9973-4453-9f5d-da777f65f16b
type: Opaque
```

base64 디코딩하기

```yaml
echo MXEydzNlNHI= | base64 -d
```

잘 만들어진 secret을 컨테이너에서 참조하도록 구성해보자.

```yaml
# envar-secret-pod.yaml
# kubectl create -f envar-secret-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: envar-secret
spec:
  containers:
  - name: envar-demo-container
    image: gcr.io/google-samples/node-hello:1.0
    env:
    - name: user
      valueFrom:
        secretKeyRef:
          name: mysecret
          key: username
    - name: pw
      valueFrom:
        secretKeyRef:
          name: mysecret
          key: password
```

변수가 컨테이너에 잘 전달됐는지 확인해보자.

```yaml
$ kubectl exec envar-secret -- printenv user pw
admin
1q2w3e4r
```

## 연습문제

다음과 같이 Mysql 서버를 지원하는 secret-mysql.yaml을 생성하자.

- Secret Name: db-secret
- Secret Data 1: DB_Password=Passw0rd!0

```bash
echo -n 'Passw0rd!0' > DB_Password
kubectl create secret generic db-secret --from-file=DB_Password --dry-run=client -o yaml > secret-mysql.yaml
```

```bash
apiVersion: v1
data:
  DB_Password: UGFzc3cwcmQhMA==
kind: Secret
metadata:
  creationTimestamp: null
  name: db-secret
```

Mysql 이미지를 하나 생성하고 앞서 만든 secret을 환경 변수 이름과 연결하자.

- Image: mysql:5.6
- Port 번호: 3306
- 환경 변수 name: MYSQL_ROOT_PASSWORD

    ```bash
    # mysql-pod.yaml
    # kubectl create -f mysql-pod.yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.6
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD # env name
          valueFrom:
            secretKeyRef:
              name: db-secret # secret name
              key: DB_Password # file name
    ```

포드에  패스워드가 잘 적용됐는지 확인할 수 있는 명령어:

```bash
kubectl exec -it mysql -- mysql -u root -p
password: Passw0rd!0
```

# LimitRanges

[https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/cpu-constraint-namespace/](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/cpu-constraint-namespace/)

```bash
# cpu-ram-min-max-reqeuets-lr.yaml
# kubectl create -f cpu-ram-min-max-reqeuets-lr.yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-ram-min-max-reqeuets-lr
spec:
  limits:
  - max:
      cpu: "800m"
      memory: "1Gi"
    min:
      cpu: "100m"
      memory: "99Mi"
    default:
      cpu: "700m"
      memory: "900Mi"
    defaultRequest:
      cpu: "110m"
      memory: "111Mi"
    type: Container

```

리미트 레인지 조회하기

```bash
$ kubectl get limitranges
NAME                          CREATED AT
cpu-ram-min-max-reqeuets-lr   2021-05-13T06:20:51Z
```

디플로이먼트 배포하기

```bash
kubectl create deploy nginx --image=nginx 
```

디플로이먼트가 배포된 노드 확인

```bash
$ kubectl get pod -o wide nginx-f89759699-8cfr6
NAME                    READY   STATUS    RESTARTS   AGE   IP         NODE                                       NOMINATED NODE   READINESS GATES
nginx-f89759699-8cfr6   1/1     Running   0          19s   10.8.2.6   gke-cluster-1-default-pool-68d3eed0-d3rw   <none>           <none>
```

노드를 describe 명령으로 확인하면 자원할당에 대한 정보를 확인할 수 있다.

```bash
$ kubectl describe nodes gke-cluster-1-default-pool-68d3eed0-d3rw
...
Non-terminated Pods:          (8 in total)
  Namespace                   Name                                                   CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                                   ------------  ----------  ---------------  -------------  ---
  default                     nginx-f89759699-8cfr6                                  110m (11%)    700m (74%)  111Mi (3%)       900Mi (31%)    110s
  kube-system                 event-exporter-gke-564fb97f9-tflt8                     0 (0%)        0 (0%)      0 (0%)           0 (0%)         6h
  kube-system                 fluentbit-gke-9g2gz                                    100m (10%)    0 (0%)      200Mi (7%)       500Mi (17%)    6h
  kube-system                 gke-metrics-agent-66grs                                3m (0%)       0 (0%)      50Mi (1%)        50Mi (1%)      6h
  kube-system                 kube-dns-6465f78586-9qfmz                              260m (27%)    0 (0%)      110Mi (3%)       210Mi (7%)     6h
  kube-system                 kube-dns-autoscaler-7f89fb6b79-svjv2                   20m (2%)      0 (0%)      10Mi (0%)        0 (0%)         6h
  kube-system                 kube-proxy-gke-cluster-1-default-pool-68d3eed0-d3rw    100m (10%)    0 (0%)      0 (0%)           0 (0%)         5h59m
  kube-system                 pdcsi-node-445ln                                       0 (0%)        0 (0%)      0 (0%)           0 (0%)         6h
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource                   Requests     Limits
  --------                   --------     ------
  cpu                        593m (63%)   700m (74%)
  memory                     481Mi (17%)  1660Mi (58%)
  ephemeral-storage          0 (0%)       0 (0%)
  hugepages-2Mi              0 (0%)       0 (0%)
  attachable-volumes-gce-pd  0            0
Events:
  Type     Reason            Age               From            Message
  ----     ------            ----              ----            -------
  Warning  NodeSysctlChange  27s (x2 over 6h)  sysctl-monitor
```

# 데몬셋

```bash
$ kubectl get ds -n kube-system
NAME                        DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR                                                        AGE
fluentbit-gke               3         3         3       3            3           kubernetes.io/os=linux                                               6h20mgke-metrics-agent           3         3         3       3            3           kubernetes.io/os=linux                                               6h20m
gke-metrics-agent-windows   0         0         0       0            0           kubernetes.io/os=windows                                             6h20m
kube-proxy                  0         0         0       0            0           kubernetes.io/os=linux,node.kubernetes.io/kube-proxy-ds-ready=true   6h20m
metadata-proxy-v0.1         0         0         0       0            0           cloud.google.com/metadata-proxy-ready=true,kubernetes.io/os=linux    6h20m
nvidia-gpu-device-plugin    0         0         0       0            0           <none>                                                               6h20m
pdcsi-node                  3         3         3       3            3           kubernetes.io/os=linux
```

# 노드 셀렉터를 활용한 포드 스케줄링

## 노드 레이블

노드 목록을 확인한다.

```bash
$ kubectl get nodes
NAME                                       STATUS   ROLES    AGE     VERSION
gke-cluster-1-default-pool-68d3eed0-4cxr   Ready    <none>   6h33m   v1.18.17-gke.100
gke-cluster-1-default-pool-68d3eed0-d3rw   Ready    <none>   6h33m   v1.18.17-gke.100
gke-cluster-1-default-pool-68d3eed0-jk5t   Ready    <none>   6h33m   v1.18.17-gke.100
```

원하는 노드에 레이블을 수행한다.

```bash
kubectl label nodes gke-cluster-1-default-pool-68d3eed0-d3rw gpu="true"
```

레이블이 잘 됐는지 확인해본다.

```bash
$ kubectl get nodes --show-labels | grep gpu
gke-cluster-1-default-pool-68d3eed0-d3rw   Ready    <none>   6h35m   v1.18.17-gke.100   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=e2-medium,beta.kubernetes.io/os=linux,cloud.google.com/gke-boot-disk=pd-standard,cloud.google.com/gke-container-runtime=docker,cloud.google.com/gke-nodepool=default-pool,cloud.google.com/gke-os-distribution=cos,cloud.google.com/machine-family=e2,failure-domain.beta.kubernetes.io/region=us-east1,failure-domain.beta.kubernetes.io/zone=us-east1-b,gpu=true,kubernetes.io/arch=amd64,kubernetes.io/hostname=gke-cluster-1-default-pool-68d3eed0-d3rw,kubernetes.io/os=linux,node.kubernetes.io/instance-type=e2-medium,topology.gke.io/zone=us-east1-b,topology.kubernetes.io/region=us-east1,topology.kubernetes.io/zone=us-east1-b
```

## 노드 셀렉터를 활용한 포드 배포

노드 셀렉터를 gpu=true로 설정하고 포드를 배포해보자.

```yaml
# http-go-gpu-pod.yaml
# kubectl create -f http-go-gpu-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: http-go-gpu
spec:
  containers:
  # 컨텐츠를 10초마다 생성
  - image: gasbugs/http-go
    name: http-go
  nodeSelector:
    gpu: "true" 
```

정확히 의도한 노드에 배치됐는지 확인한다.

```yaml
$ kubectl get pod/http-go-gpu -o wide
NAME          READY   STATUS              RESTARTS   AGE   IP       NODE                                       NOMINATED NODE   READINESS GATES
http-go-gpu   0/1     ContainerCreating   0          16s   <none>   gke-cluster-1-default-pool-68d3eed0-**d3rw**   <none>           <none>
```

# HPA 테스트 - Cloud

[https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/](https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/)

## 서비스 및 HPA 배포

기존의 올라와있는 서비스를 정리하고 php-apahce를 배포한다. 이 서버는 스스로 CPU를 마구 소비하는 코드가 들어있다. 

디플로이먼트와 서비스만 배포됨

```yaml
kubectl delete ns blue
kubectl delete all --all
kubectl apply -f https://k8s.io/examples/application/php-apache.yaml
```

HPA를 생성해서 디플로이먼트를 감시하자.

- CPU 사용량이 50%가 넘으면 스케일링 수행
- 최소 1개를 유지하며 최대 10개까지 구성된다.

```yaml
kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
```

## CPU 부하 증가

모니터링을 시작하자.

```yaml
kubectl get hpa -w
```

busybox 리눅스 애플리케이션으로 굉장히 가벼운 라이브러리를 가짐

- 안드로이드 루팅하면 들어가는 애플리케이션

```yaml
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
```

## 모니터링 후 결과 확인

```yaml
$ kubectl get hpa -w
NAME         REFERENCE               TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%     1         10        1          3m28s # 부하 없음
php-apache   Deployment/php-apache   249%/50%   1         10        1          4m23s # 리미트까지 CPU가 치솟음
php-apache   Deployment/php-apache   249%/50%   1         10        4          4m23s
php-apache   Deployment/php-apache   249%/50%   1         10        5          4m39s
php-apache   Deployment/php-apache   159%/50%   1         10        5          4m53s
php-apache   Deployment/php-apache   81%/50%    1         10        5          5m24s
php-apache   Deployment/php-apache   58%/50%    1         10        5          5m55s # 변화가 없가 추가 배포
php-apache   Deployment/php-apache   58%/50%    1         10        6          6m11s
php-apache   Deployment/php-apache   58%/50%    1         10        6          6m26s
php-apache   Deployment/php-apache   51%/50%    1         10        6          6m57s
php-apache   Deployment/php-apache   55%/50%    1         10        6          7m28s
php-apache   Deployment/php-apache   55%/50%    1         10        7          7m28s
php-apache   Deployment/php-apache   52%/50%    1         10        7          7m44s
php-apache   Deployment/php-apache   46%/50%    1         10        7          8m45s # 꺾임, 공격 중지
php-apache   Deployment/php-apache   9%/50%     1         10        7          9m16s
php-apache   Deployment/php-apache   0%/50%     1         10        7          9m47s
php-apache   Deployment/php-apache   0%/50%     1         10        7          14m
php-apache   Deployment/php-apache   0%/50%     1         10        2          14m # 쿨링 타임 후 스케일 다운
php-apache   Deployment/php-apache   0%/50%     1         10        2          14m
php-apache   Deployment/php-apache   0%/50%     1         10        1          14m
```

## 연습문제

이미지 nginx를 생성하고 HPA가 적용하라.

- 최대 스케일링 개 수: 10
- 최소 스케일링 개 수: 2
- 스케일링을 수행할 CPU 활동량: 30%

```yaml
kubectl create deploy nx --image=nginx
kubectl autoscale deploy nx --cpu-percent=30 --min=2 --max=10
```

다음과 같이 yaml 파일을 구성하는 방법도 있다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nx
  name: nx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nx
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}
---
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  creationTimestamp: null
  name: nx
spec:
  maxReplicas: 10
  minReplicas: 2
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nx
  targetCPUUtilizationPercentage: 30
status:
  currentReplicas: 0
  desiredReplicas: 0
```

# 모니터링 툴 구성

top 명령으로 현재 클러스터 사용량 확인

```yaml
$ kubectl top nodes
NAME                                       CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
gke-cluster-1-default-pool-68d3eed0-4cxr   148m         15%    890Mi           31%
gke-cluster-1-default-pool-68d3eed0-d3rw   76m          8%     669Mi           23%
gke-cluster-1-default-pool-68d3eed0-jk5t   77m          8%     688Mi           24%

$ kubectl top pods
NAME                          CPU(cores)   MEMORY(bytes)
nx-86dc5b9869-6qzr6           0m           2Mi
nx-86dc5b9869-rn86b           0m           6Mi
php-apache-5c4f475bf5-qv87d   1m           12Mi
```

## 메트릭스 서버 구성하기

[https://blog.naver.com/isc0304/221860790762](https://blog.naver.com/isc0304/221860790762)

메트릭스 서버 배포

```yaml
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.7/components.yaml
```

디플로이먼트 수정하기

```yaml
kubectl edit deployments.apps -n kube-system metrics-server

커맨드 명령을 찾아서 다음 옵션 추가
- --kubelet-insecure-tls # 추가된 옵션
- --kubelet-preferred-address-types=InternalIP # 추가된 옵션

(:wq로 저장한다)
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b0f45c61-9410-4c70-a90e-4c0334affed2/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b0f45c61-9410-4c70-a90e-4c0334affed2/Untitled.png)

저장후에 수정된 포드로 다시 시작했는지 get pod 확인한다.

부팅된 것이 확인되면 kubectl top pods 명령으로 시스템 메트릭스 조회한다.

## 도커 로그 저장소 확인

```bash
$ sudo -i
# cd /var/log/containers/
# ls
etcd-instance-1_kube-system_etcd-a781ac13432a9b0be07d77157e626392d9744343803d4728a606f10032d3c6f7.log
etcd-instance-1_kube-system_etcd-c94ee8bf072135bd00c7f49b5df193ff646a755f33319ac911b0599f9b9c18cf.log
kube-apiserver-instance-1_kube-system_kube-apiserver-8ce79d296873714259a0700c2f24844c1a8f5c2e4b5bfd5204434c14ca32bb5f.log
kube-apiserver-instance-1_kube-system_kube-apiserver-b1abfc17478fa2f50b64dbe1705457cfe87b1a137d88c0ad359e2bbd6b771c71.log
kube-controller-manager-instance-1_kube-system_kube-controller-manager-879c62521d1dc9d02f3d707800c1e82516e5338ab85f52b26c1c065a835d1b31.log
kube-controller-manager-instance-1_kube-system_kube-controller-manager-89da1c7f4019f9297850602a5778877cbfbe733bb34893ffef66a5beac683b4b.log
kube-proxy-nsfph_kube-system_kube-proxy-5dac246a40d8475bd75b798203b722fd7b833e19f680dd90a7daf172ba0d0b98.log
kube-scheduler-instance-1_kube-system_kube-scheduler-112847e538b5822e39ba9d665072f4931d91eba9037ffff75a462381534fe259.log
kube-scheduler-instance-1_kube-system_kube-scheduler-21e6d9798252a867c16cf1a5e38dcde5133313c5a01a195b4ec0bda6210c7333.log
weave-net-ctlp5_kube-system_weave-4aea6c34f72a7e6cfe4e60d824252482293c71caf5e203335ca45040c62f3c43.log
weave-net-ctlp5_kube-system_weave-b773b453c753e836b225d366b1036f6f46628d87b703469abf33fb6ca0e5be1c.log
weave-net-ctlp5_kube-system_weave-init-cfbf5049c887c23de4be1407d86d9f3b966710a1835c302b6fb908873511576e.log
weave-net-ctlp5_kube-system_weave-npc-874556a794143486019d68437b601709744bf8209183841db1d6340bdc8e6fc1.log
weave-net-ctlp5_kube-system_weave-npc-a3fee8a4e5808447cf35a9d8ad69c031d5b0bafc485c2d157173f081b19e87d5.log
```

로그의 정보는 json 형태로 구성돼있고 log와 stream 종류 시간이 구성돼 있다.

```bash
tail etcd-instance-1_kube-system_etcd-a781ac13432a9b0be07d77157e626392d9744343803d4728a606f10032d3c6f7.log

{"log":"2021-05-12 08:28:02.008722 I | etcdserver/api/etcdhttp: /health OK (status code 200)\n","stream":"stderr","time":"2021-05-12T08:28:02.008856926Z"}
{"log":"2021-05-12 08:28:12.008898 I | etcdserver/api/etcdhttp: /health OK (status code 200)\n","stream":"stderr","time":"2021-05-12T08:28:12.009093578Z"}
{"log":"2021-05-12 08:28:22.008689 I | etcdserver/api/etcdhttp: /health OK (status code 200)\n","stream":"stderr","time":"2021-05-12T08:28:22.008830974Z"}
{"log":"2021-05-12 08:28:32.008379 I | etcdserver/api/etcdhttp: /health OK (status code 200)\n","stream":"stderr","time":"2021-05-12T08:28:32.008565579Z"}
{"log":"2021-05-12 08:28:42.009698 I | etcdserver/api/etcdhttp: /health OK (status code 200)\n","stream":"stderr","time":"2021-05-12T08:28:42.010792789Z"}
{"log":"2021-05-12 08:28:52.008233 I | etcdserver/api/etcdhttp: /health OK (status code 200)\n","stream":"stderr","time":"2021-05-12T08:28:52.008407615Z"}
{"log":"2021-05-12 08:28:53.829523 N | pkg/osutil: received terminated signal, shutting down...\n","stream":"stderr","time":"2021-05-12T08:28:53.84146267Z"}
{"log":"WARNING: 2021/05/12 08:28:53 grpc: addrConn.createTransport failed to connect to {10.128.0.2:2379  \u003cnil\u003e 0 \u003cnil\u003e}. Err :connection error: desc = \"transport: Error while dialing dial tcp 10.128.0.2:2379: connect: connection refused\". Reconnecting...\n","stream":"stderr","time":"2021-05-12T08:28:53.866396249Z"}
{"log":"WARNING: 2021/05/12 08:28:53 grpc: addrConn.createTransport failed to connect to {127.0.0.1:2379  \u003cnil\u003e 0 \u003cnil\u003e}. Err :connection error: desc = \"transport: Error while dialing dial tcp 127.0.0.1:2379: connect: connection refused\". Reconnecting...\n","stream":"stderr","time":"2021-05-12T08:28:53.866417624Z"}
{"log":"2021-05-12 08:28:53.892462 I | etcdserver: skipped leadership transfer for single voting member cluster\n","stream":"stderr","time":"2021-05-12T08:28:53.904026562Z"}
```


---------------------------------------------------------------------------------------------------------------------------------------

- 목차

# 클러스터 구성 안내

5일차에는 모니터링 서비스를 테스트하기 위해 다음과 같이 사양을 정의하고 쿠버네티스 클러스터를 구성한다. 이때 리전을 변경하는 것도 잊지말자.

- 메모리 구성을 8GB
- 리전은 Instance와 다른 곳

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d58702ce-ac3d-4d9e-ab55-2dc1078e13cc/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d58702ce-ac3d-4d9e-ab55-2dc1078e13cc/Untitled.png)

# 쿠버네티스 대시보드

[https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

큐브대시보드를 배포해보자. top 명령이 수행 가능한 곳에서만 정상 동작한다.

```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml

namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard createdsecret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created
```

배포된 자원이 잘 보이는지 확인한다.

```bash
$ kubectl get all -n kubernetes-dashboard

NAME                                             READY   STATUS    RESTARTS   AGE
pod/dashboard-metrics-scraper-78f5d9f487-j24lg   1/1     Running   0          113s
pod/kubernetes-dashboard-577bd97bc-szb9w         1/1     Running   0          114s

NAME                                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/dashboard-metrics-scraper   ClusterIP   10.12.12.139   <none>        8000/TCP   115s
service/kubernetes-dashboard        ClusterIP   10.12.2.119    <none>        443/TCP    2m1s

NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/dashboard-metrics-scraper   1/1     1            1           114s
deployment.apps/kubernetes-dashboard        1/1     1            1           116s

NAME                                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/dashboard-metrics-scraper-78f5d9f487   1         1         1       115s
replicaset.apps/kubernetes-dashboard-577bd97bc         1         1         1       116s
```

edit 명령을 수행해서 service/kubernetes-dashboard 의 type을 LoadBalancer로 변경해 외부 부하분산기를 배치한다.

```bash
kubectl edit service/kubernetes-dashboard -n kubernetes-dashboard

(vim이 열리면 i 버튼을 눌러서 인서트 모드로 변경,
type을 찾아서 LoadBalancer로 변경하고,
ESC를 눌러서 인서트모드에서 나간 후 :wq를 사용해 저장한다.)
```

로드밸런서가 생성되고 IP를 받아온다.

```bash
$ kubectl get svc -n kubernetes-dashboard -w
NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
dashboard-metrics-scraper   ClusterIP      10.12.12.139   <none>          8000/TCP        5m30s
kubernetes-dashboard        LoadBalancer   10.12.2.119    35.243.181.34   443:32674/TCP   6m16s
```

[https://35.243.181.34](https://35.243.181.34) 로 접속한다. 비공개 연결을 무시하고 안전하지 않음을 계속 진행한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f4b28398-1feb-4100-b8aa-509bd2150d06/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f4b28398-1feb-4100-b8aa-509bd2150d06/Untitled.png)

토큰 발급을 위해 샘플 유저를 생성해보자.

[https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md](https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md)

```bash
# SA를 생성한다. (애플리케이션용 유저)
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
EOF

# 앞서 생성한 sa에 cluster-admin 권한 부여 
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF

# 토큰 발급
kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"
```

토큰을 발급받으면 클라우드 셸의 기능을 사용해서 복사한다.(Ctrl + C를 사용하지 말자).

토큰을 Web UI에 입력하고 로그인하면 정상적으로 관리페이지로 접속할 수 있다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a1b5abde-d69e-487a-af07-4eb02d961a0d/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a1b5abde-d69e-487a-af07-4eb02d961a0d/Untitled.png)

내부로 접속해서 사용해보자.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/77c91b12-29f8-4958-9b20-eb528fc477f6/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/77c91b12-29f8-4958-9b20-eb528fc477f6/Untitled.png)

# 프로메테우스를 활용한 쿠버네티스 모니터링

GKE에서 마켓플레이스로 접근한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/124e0a80-d03f-4ff7-9d84-45d43ec26409/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/124e0a80-d03f-4ff7-9d84-45d43ec26409/Untitled.png)

마켓플레이스에서는 GCP에서 준비한 다양한 애플리케이션을 쉽게 설치해서 사용이 가능하다. prometheus와 grafana를 검색해서 설치를 해보자. "구성" 버튼을 누른다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9ca18baa-2ba8-40d6-a72f-856397916806/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9ca18baa-2ba8-40d6-a72f-856397916806/Untitled.png)

네임스페이스를 새로 생성하고 "배포" 버튼을 누른다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d4924a61-b941-4a8b-a6bc-44e9cd4aa947/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d4924a61-b941-4a8b-a6bc-44e9cd4aa947/Untitled.png)

프로메테우스 설치가 완료되면 애플리케이션 페이지로 리다이렉션된다. 거기서 그라파나에 대한 username과 password를 확인할 수 있다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4157abde-2f37-44ea-a788-11796f7357a1/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4157abde-2f37-44ea-a788-11796f7357a1/Untitled.png)

서비스 및 수신에 가서 prometheus-1-grafana 서비스를 수정한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c9570774-c7c0-485b-bae7-6f2ce6eb6dbb/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c9570774-c7c0-485b-bae7-6f2ce6eb6dbb/Untitled.png)

ClusterIP를 로드밸런서로 수정한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4a052b18-8a9e-4e63-87db-d22e9469d210/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4a052b18-8a9e-4e63-87db-d22e9469d210/Untitled.png)

잠시 후 외부 로드밸런서가 생성되고 IP를 받아온다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2ccd265f-9e55-4f06-a3f3-54e38e91fdbe/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2ccd265f-9e55-4f06-a3f3-54e38e91fdbe/Untitled.png)

## 엘라스틱 스택을 활용한 로그 수집과 시각화

[https://blog.naver.com/isc0304/221860255105](https://blog.naver.com/isc0304/221860255105)

## 이스티오를 활용한 네트워크 메시 환경 모니터링

[https://blog.naver.com/isc0304/221892105612](https://blog.naver.com/isc0304/221892105612)

이스티오 설치하기

```bash
curl -L https://istio.io/downloadIstio | sh -
cd istio-1.9.5
export PATH=$PWD/bin:$PATH
istioctl
```

이스티오를 사용해 데모 버전으로 설치

```yaml
istioctl install --set profile=demo --skip-confirmation
```

네임스페이스 레이블을 이스티오 인젝션이 수행되도록 수정

```yaml
kubectl label namespace default istio-injection=enabled
```

북 인포에 대한 프로젝트 배포

```yaml
kubectl delete all --all # 잘못 설치한 경우 삭제
kubectl delete limitrange default-limit-range # 잘못 설치한 경우 삭제
kubectl delete -f samples/bookinfo/platform/kube/bookinfo.yaml # 잘못 설치한 경우 삭제
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```

북인포를 외부로 서비스할 수 있도록 게이트웨이 생성

```yaml
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
```

북인포에 대한 MSA 아키텍처

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c5b8c9b3-4be1-49e6-b7d7-634a3ab7b945/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c5b8c9b3-4be1-49e6-b7d7-634a3ab7b945/Untitled.png)

게이트를 생성하면서 만들어진 서비스를 화인하자. 이 서비스는 로드밸런서로 만들어져서 외부 IP를 받아올 수 있다.

```yaml
$ kubectl get svc -n istio-system -l istio=ingressgateway --all-namespaces
NAMESPACE      NAME                   TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                                                                      AGE
istio-system   istio-ingressgateway   LoadBalancer   10.8.11.193   35.226.15.253   15021:31928/TCP,80:30936/TCP,443:30609/TCP,31400:32281/TCP,15443:30857/TCP   10m
```

배포한 북인포 프로젝트 페이지에 접속해보자. [http://35.226.15.253/productpage](http://35.226.15.253/productpage)

반드시 productpage라는 곳으로 접속해야 한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/407abbe7-260e-4cae-855b-f22cee9bc0fd/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/407abbe7-260e-4cae-855b-f22cee9bc0fd/Untitled.png)

배포된 포드를 확인하면 사이드카 컨테이너가 추가로 배치되어 2/2를 보여주는 모습이 확인된다.

```bash
$ kubectl get pod
NAME                              READY   STATUS    RESTARTS   AGE
details-v1-66b6955995-jc9h4       2/2     Running   0          3m44s
productpage-v1-5d9b4c9849-4szhp   2/2     Running   0          3m40s
ratings-v1-fd78f799f-dxp28        2/2     Running   0          3m43s
reviews-v1-6549ddccc5-rtlkp       2/2     Running   0          3m42s
reviews-v2-76c4865449-cc774       2/2     Running   0          3m41s
reviews-v3-6b554c875-5x6lx        2/2     Running   0          3m41s
```

Kiali 대시보드와 데이터베이스역할을 하는 프로메테우스를 띄우고서 대시보드를 접근할수 있도록 명령을 실행한다.

```yaml
kubectl apply -f samples/addons/kiali.yaml
kubectl apply -f samples/addons/prometheus.yaml
istioctl dashboard kiali # localhost:20001 서비스를 오픈
```

로컬 호스트의 20001번 포트로 접근하고 그래프 쪽 내용을 보면 다음과 같은 그래프를 확인할 수 있다. 그래프가 비어있는 경우에는 위에서 접속한 사이트 다시 여러번 접근하고 새로고침하면 그래프를 확인할 수 있다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ec99aaa4-3dbe-4658-b954-5450fbec595f/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ec99aaa4-3dbe-4658-b954-5450fbec595f/Untitled.png)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/98dc72cb-4e3e-42d0-aed1-57327801861d/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/98dc72cb-4e3e-42d0-aed1-57327801861d/Untitled.png)

# 노드 업그레이드 절차

drain 명령을 사용해 우선 노드에 배치돼 있는 포드를 다른 노드로 안전하게 옮긴다. 어떤 노드를 업데이트할지 결정하고 그 노드에 drain 명령을 실행한다.

```bash
$ kubectl get nodes
NAME                                       STATUS   ROLES    AGE    VERSION
gke-cluster-1-default-pool-b1c4bf8d-94c7   Ready    <none>   144m   v1.18.17-gke.100
gke-cluster-1-default-pool-b1c4bf8d-tkfb   Ready    <none>   144m   v1.18.17-gke.100
gke-cluster-1-default-pool-b1c4bf8d-xr3t   Ready    <none>   144m   v1.18.17-gke.100

$ kubectl drain gke-cluster-1-default-pool-b1c4bf8d-xr3t
```

오류 메시지가 나오면 옵션을 확인하고 정말 노드를 분리해도 되는지 점검 후에 drain을 진행한다.

```bash
kubectl drain gke-cluster-1-default-pool-b1c4bf8d-xr3t --delete-emptydir-data --ignore-daemonsets
```

이 노드에 포드가 여전히 남이있는지 확인해보자. 데몬셋 관련된 기능만 남아있다.

```bash
$ kubectl get pod -o wide -A | grep xr3t
kube-system            fluentbit-gke-4zrq2                                         2/2     Running     0          151m    10.142.0.11   gke-cluster-1-default-pool-b1c4bf8d-xr3t   <none>           <none>
kube-system            gke-metrics-agent-sn6mb                                     1/1     Running     1          151m    10.142.0.11   gke-cluster-1-default-pool-b1c4bf8d-xr3t   <none>           <none>
kube-system            kube-proxy-gke-cluster-1-default-pool-b1c4bf8d-xr3t         1/1     Running     0          150m    10.142.0.11   gke-cluster-1-default-pool-b1c4bf8d-xr3t   <none>           <none>
kube-system            pdcsi-node-9b9ng                                            2/2     Running     0          151m    10.142.0.11   gke-cluster-1-default-pool-b1c4bf8d-xr3t   <none>           <none>
prometheus-1           prometheus-1-node-exporter-l27bj                            1/1     Running     0          107m    10.142.0.11   gke-cluster-1-default-pool-b1c4bf8d-xr3t   <none>           <none>
```

드레인된 노드에 정보를 확인해보자.

```bash
$ kubectl get nodes
NAME                                       STATUS                     ROLES    AGE    VERSION
gke-cluster-1-default-pool-b1c4bf8d-94c7   Ready                      <none>   153m   v1.18.17-gke.100
gke-cluster-1-default-pool-b1c4bf8d-tkfb   Ready                      <none>   153m   v1.18.17-gke.100
gke-cluster-1-default-pool-b1c4bf8d-xr3t   Ready,***SchedulingDisabled***   <none>   153m   v1.18.17-gke.100
```

업데이트를 모두 진행하고 난 뒤에는 다음 명령으로 다시 스케줄링 가능하도록 만들 수 있다.

```bash
kubectl uncordon gke-cluster-1-default-pool-b1c4bf8d-xr3t

$ kubectl get nodes
NAME                                       STATUS   ROLES    AGE    VERSION
gke-cluster-1-default-pool-b1c4bf8d-94c7   Ready    <none>   155m   v1.18.17-gke.100
gke-cluster-1-default-pool-b1c4bf8d-tkfb   Ready    <none>   155m   v1.18.17-gke.100
gke-cluster-1-default-pool-b1c4bf8d-xr3t   Ready    <none>   155m   v1.18.17-gke.100
```

# 클러스터 백업과 복원

## 포드 정보 백업과 복원

주로 포도와 관련된 정보를 백업

포드 정보 백업하기

```bash
kubectl get all -A -o yaml > cluster-backup-20210514.yaml
```

포드 정보 복원하기

```bash
kubectl apply -f cluster-backup-20210514.yaml
```

## etcd 데이터 백업과 복원

클러스터 설정 관련된 정보를 담는다. 주로 configmap, role, user, secret 등 정보를 담는다.

온프레미스 환경에서 테스트하자.

etcd 정보 백업하기

```bash
cd etcd-v3.2.32-linux-amd64

# 백업 명령어
sudo ETCDCTL_API=3 ./etcdctl --endpoints 127.0.0.1:2379 \
                             --cacert /etc/kubernetes/pki/etcd/ca.crt \
                             --cert   /etc/kubernetes/pki/etcd/server.crt \
                             --key    /etc/kubernetes/pki/etcd/server.key \
                             snapshot save etcd-snapshot-20210514.dat

# 백업된 데이터의 정보 확인
sudo ETCDCTL_API=3 ./etcdctl --endpoints 127.0.0.1:2379 \
                             --cacert /etc/kubernetes/pki/etcd/ca.crt \
                             --cert   /etc/kubernetes/pki/etcd/server.crt \
                             --key    /etc/kubernetes/pki/etcd/server.key \
                             --write-out=table snapshot status etcd-snapshot-20210514.dat

# output
+----------+----------+------------+------------+
|   HASH   | REVISION | TOTAL KEYS | TOTAL SIZE |
+----------+----------+------------+------------+
| 5c2a2c66 |   120281 |       1459 |     2.7 MB |
+----------+----------+------------+------------+
```

etcd 정보 복원하기

다음 명령으로 별도의 디렉토리에 etcd 정보를 복원하고 포드의 정보를 수정해 모든 디렉토리와 마운트 정보를 새로 생성된 디렉토리로 옮기는 작업이 필요하다.

```bash
sudo ETCDCTL_API=3 ./etcdctl --endpoints=127.0.0.1:2379 \
--cacert /etc/kubernetes/pki/etcd/ca.crt \
--cert /etc/kubernetes/pki/etcd/server.crt \
--key /etc/kubernetes/pki/etcd/server.key  \
--data-dir /var/lib/etcd-restore \
--initial-cluster='master=https://127.0.0.1:2380' \
--name=master \
--initial-cluster-token this-is-token \
--initial-advertise-peer-urls https://127.0.0.1:2380 \
snapshot restore ~/yaml/snapshotdb
```

# 쿠버네티스 클러스터에서 공개키 기반 통신 이해

수월한 실습을 위해 관리자 권한으로 상승한다.

```bash
sudo -i
```

쿠버네티스는 통신에 필요한 인증서를 /etc/kubernetes/pki/에 저장한다.

```bash
# cd /etc/kubernetes/pki/
# ls
apiserver-etcd-client.crt     apiserver.crt  etcd                    front-proxy-client.key
apiserver-etcd-client.key     apiserver.key  front-proxy-ca.crt      sa.key
apiserver-kubelet-client.crt  ca.crt         front-proxy-ca.key      sa.pub
apiserver-kubelet-client.key  ca.key         front-proxy-client.crt
```

인증서 정보 확인하기

```bash
openssl x509 -in <cert_path> -text

$ openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text
# output
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 7909249586033780566 (0x6dc34c97ef72e756)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes # common name
        Validity
            Not Before: May 11 01:11:48 2021 GMT
            Not After : May 11 01:11:48 2022 GMT
        Subject: CN = kube-apiserver
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
                    00:d6:e5:e9:b9:7f:17:c7:bc:be:25:6a:b9:52:80:
                    8e:ab:fe:c9:0f:1d:25:29:b9:42:e3:47:85:10:49:
                    c0:35:a3:bb:cb:6c:18:16:26:8f:90:7a:67:b3:c5:
                    14:dd:e8:5a:5c:37:f9:21:6b:aa:48:d1:de:30:92:
                    59:1d:3c:5a:42:5c:25:ce:e2:42:e5:14:13:dd:7e:
                    80:12:dd:4f:49:75:b2:a6:79:16:fc:18:da:45:5b:
                    26:fd:6b:ab:a2:df:57:c0:91:28:c8:b8:8a:9f:bd:
                    f3:11:03:82:7b:28:36:95:59:44:22:00:94:7d:fb:
                    60:6d:f0:13:6b:b1:ae:21:1e:81:81:c6:0e:59:21:
                    e6:11:d9:62:03:7d:21:ed:1d:77:f3:55:35:f6:9a:
                    3a:fe:9d:cd:e5:29:1c:9c:e7:b1:0a:5b:0d:49:ca:
                    c9:53:8d:1b:9e:5f:21:98:0b:ad:23:b8:c9:01:12:
                    02:f6:08:dd:9d:dc:70:48:9a:c4:84:ed:19:71:b8:
                    68:12:8c:f2:df:50:9e:18:1f:52:d0:5e:3e:51:e9:
                    bb:eb:2a:3e:dd:c9:e6:b9:e0:d9:45:b4:19:34:2b:
                    2b:e9:5c:12:a1:92:36:aa:63:29:d6:31:a7:1d:7c:
                    e3:e1:14:e7:1b:4a:43:da:4c:04:f4:8f:43:aa:c7:
                    1e:89
                Exponent: 65537 (0x10001)
...
```

## kubeadm 인증서 관리하기

인증서 만료일자 확인하기

```bash
kubeadm certs check-expiration
```

자동 업데이트 방법: 쿠버네티스 버전을 업데이트하면 자동으로 업데이트됨
