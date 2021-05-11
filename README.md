# kubernetes

- 목차

# 1 GCP 실습 환경 구성

## 1.1 GCP 프로젝트 생성하기

프로젝트를 관리할 수 있는 창으로 들어가자.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f4a87f3b-50b3-4d13-94d8-7360860f9fc9/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f4a87f3b-50b3-4d13-94d8-7360860f9fc9/Untitled.png)

프로젝트를 삭제할 때는 관리 창으로가고 새 프로젝트를 생성할 때는 "새 프로젝트"를 선택하면 된다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d1a5974a-2b26-4e93-a3c7-be1fd6197f98/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d1a5974a-2b26-4e93-a3c7-be1fd6197f98/Untitled.png)

프로젝트를 다 사용하고 삭제하면 비용 청구가 되지 않는다.

새프로젝트를 만들어보자. 프로젝트 이름은 고유하게 만드는 것이 좋다. <my id>-gke-<오늘날짜>로 생성하자. 위치는 "조직 없음"으로 그냥 놔두자.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2d64d1a8-5949-4f9e-9195-20280230371e/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2d64d1a8-5949-4f9e-9195-20280230371e/Untitled.png)

프로젝트 선택 화면으로 넘어가면 우리가 생성한 프로젝트를 확인할 수 있다. 

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ae7b545e-b529-47b3-a12b-65a32af58020/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ae7b545e-b529-47b3-a12b-65a32af58020/Untitled.png)

## 1.2 프로젝트에 가상 머신 할당하기

컴퓨트 엔진 메뉴로 접근한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cfc742aa-0b77-43b4-bbf2-aa297b5e338f/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cfc742aa-0b77-43b4-bbf2-aa297b5e338f/Untitled.png)

API 서비스를 활용하도록 "사용" 버튼을 누르자.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dd3186c7-c1bf-4c6f-8b3a-780aba51e615/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dd3186c7-c1bf-4c6f-8b3a-780aba51e615/Untitled.png)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d8f5f8ce-f02e-4055-a423-c0e4733ea50b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d8f5f8ce-f02e-4055-a423-c0e4733ea50b/Untitled.png)

인스턴스 만들 때 부팅 디스크를 다음과 같이 변경한다.

- Ubutu: 20.04 LTS
- 100GB 디스크

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/276bedde-8e64-44b3-b0bd-710473d50f47/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/276bedde-8e64-44b3-b0bd-710473d50f47/Untitled.png)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3278c806-6a5a-4c4c-aaf9-bc1f959e28d9/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3278c806-6a5a-4c4c-aaf9-bc1f959e28d9/Untitled.png)

방화벽 룰을 다음과 체크한다. 

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c89d7ac9-74ea-48dc-a06b-5acc1cc97558/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c89d7ac9-74ea-48dc-a06b-5acc1cc97558/Untitled.png)

디스크와 방화벽 설정을 잘 변경했다면 "만들기" 버튼을 클릭한다.

## 1.3 가상머신에 SSH 접속

가상머신이 잘 만들어졌다면 SSH로 접근할 수 있다. SSH 버튼을 눌러서 접근해보자. 검은색 창이 하나 열리면서 쉘이 확인된다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1391236f-dda2-4b72-8261-166dea797a1f/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1391236f-dda2-4b72-8261-166dea797a1f/Untitled.png)

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

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bca7107e-a9ad-4b2e-821f-5d6051476086/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bca7107e-a9ad-4b2e-821f-5d6051476086/Untitled.png)

2. [hub.docker.com](http://hub.docker.com) 사용하기

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5d3690db-263b-437f-bdc0-1ce872cb9a38/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5d3690db-263b-437f-bdc0-1ce872cb9a38/Untitled.png)

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

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5fafc2cb-2b8a-41fc-8cda-625703d3df4c/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5fafc2cb-2b8a-41fc-8cda-625703d3df4c/Untitled.png)

외부IP를 통해서 들어오면 tomcat이 오픈된것을 확인할 수 있으며 ManagerApp에서 admin//admin 인증정보를 테스트 할 수 있다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d599890d-973b-4201-b724-04e66cdefded/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d599890d-973b-4201-b724-04e66cdefded/Untitled.png)

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

    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/91173a93-8864-4350-aa56-d0ff85cd1bcd/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/91173a93-8864-4350-aa56-d0ff85cd1bcd/Untitled.png)

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

# 4 쿠버네티스 클러스터 구성하기

왼쪽 메뉴에서 쿠버네티스 엔진로 접근하면 클러스터 "만들기"를 클릭할 수 있다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3711d569-acaf-4c52-b76d-be1925893389/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3711d569-acaf-4c52-b76d-be1925893389/Untitled.png)

다수의 노드를 구성해서 클러스터를 만드는게 원칙이지만...

클라우드 제공 업체는 Serverless 방식을 사용해 노드 없이 클러스터 방식을 개발했다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7e34d930-31f5-4176-b521-94c6cd3524de/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7e34d930-31f5-4176-b521-94c6cd3524de/Untitled.png)

구성을 선택하고 "만들기"를 바로 선택한다.

생성이 완료되면 오른쪽에 연결 버튼을 클릭하고

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ddeb2eb7-4e1f-4393-b295-631c3b19dc94/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ddeb2eb7-4e1f-4393-b295-631c3b19dc94/Untitled.png)

클라우드 쉘에서 실행을 선택한다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2adac614-c016-45fb-a066-cb0d3f4c4e06/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2adac614-c016-45fb-a066-cb0d3f4c4e06/Untitled.png)

그러면 클라우드 셸이 하단에 창이 생성되면서 명령어가 자동 입력된다. 마스터 노드에 접근할 수 있는 인증정보를 설정하는 커맨드다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c3c655fa-5626-49fa-b49e-c53f7648e06d/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c3c655fa-5626-49fa-b49e-c53f7648e06d/Untitled.png)

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

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e6fa8070-f70e-43ec-8124-cca2485d907f/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e6fa8070-f70e-43ec-8124-cca2485d907f/Untitled.png)

퇴실할 때 클라우드 정리하기 

1. 클러스터(cluster-1)는 삭제
2. 인스턴스(instance-1)는 중지
