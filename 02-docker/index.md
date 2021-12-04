# [Docker] 생활코딩 입문 수업 정리


Docker 기본적인 명령어 정리

<!--more-->

docker hub에서 pull해서 image를 다운받고 run해서 container를 살행한다.

- 도커 이미지 확인
  - `docker images`
  - 그러면 아래처럼 뜬다

```
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
minsoo/flaskapp           latest              ecad9ae45c3d        2 weeks ago         897MB
<none>                    <none>              b36cd113e3f5        2 weeks ago         897MB
```

- container 띄우기
  - `docker run [OPTION] image이름 [COMMAND]`
  - `-d` 옵션주면 background 모드로 실행
- container 시작
  - `docker start container이름`
  - `-a` 옵션 : Attach STDOUT/STDERR and forward signals
  - `-i` 옵션 : Attach container's STDIN
  - `-a` 옵션주면 `exit`해도 container가 꺼지지 않는다..!
- container 멈추기
  - `docker stop container이름`
- 현재 살아있는 container
  - `docker ps`
- 모든 container 확인
  - `docker ps -a`
- 로그 출력
  - `docker logs container이름`
  - 실시간 `docker logs -f container이름`
- container 삭제
  - `docker rm container이름`
- image 삭제
  - `docker rmi image이름`

도커 Host한에 독립적인 Container들이 존재하는 것이다. 따라서 Host의 port와 Container의 port를 연결해줘야 우리가 Container와 연결이 된다. 이를 port forwarding이라고 한다. 
- `docker run -p 80:80 image이름` : docker host 80번과 container 80번 연결

- 실행중인 도커 Container에 명령어 실행
    - `docker exec container이름 [COMMAND]`
