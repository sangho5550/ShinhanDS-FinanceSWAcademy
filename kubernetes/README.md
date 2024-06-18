#KuberNetes 설정 파일 폴더
https://miro.com/welcomeonboard/MWVBWElYczFtMEZsRmdleFlJdHA3MGNVNW1sRDcwS054RGJoUFlRTVUzZjlaRms1a0xNSTVaaU9uaEo5SlpjQ3wzNDU4NzY0NTg1NjQ5MDkwMzU0fDI=?share_link_id=716260514452
https://learn.microsoft.com/ko-kr/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package

#환경 준비
1. 포드만 설치
dnf search podman  ## 포드만 검색
dnf install -y podman podman-docker  ## 포드만, 포드만 도커 설치

2. 포드만 이미지 다운 & 실행
podman search httpd  ## 포드만 httpd 관련 이미지 검색

podman pull quay.io/fedora/httpd-24  ## 포드만 이미지 다운로드

podman images  ## 포드만 이미지 확인

podman run -d --name tang-httpd quay.io/fedora/httpd-24  ## 포드만 컨테이너 실행
## run : 실행, -d : 백그라운드, --name : 이름 지정, quay.io/fedora/httpd-24 : 포드만 이미지

podman container ls          ## 포드만 컨테이너 목록 확인
podman container stop --all  ## 포드만 컨테이너 전제 중지
podman container rm --all    ## 포드만 컨테이너 전체 삭제
podman container ls --all    ## 포드만 컨테이너 전체 목록 확인

dnf install httpd -y   ## httpd 설치

systemctl enable --now httpd.service  ## 재부팅 시 httpd 자동 실행, --now : 지금 실행

echo "hello apache" > /var/www/html/index.html  ## 웹페이지 생성

systemctl status httpd    ## httpd 현재 상태 확인

curl localhost/index.html  ## Client Url 이란 의미로 클라이언트에서 url을 사용해서 서버와 데이터를 송수신하는 명령어
                            ## Linux, MacOS, Window 등 다양한 환경에서 HTTP, HTTPS, SMTP, TELNET, FTP, LDAP 등 다양한 프로토콜을 지원

systemctl is-active httpd  ## httpd 실행 상태 확인

mkdir htdocs
echo "hello apache from container" > htdocs/index.html
ls -l htdocs/
cat htdocs/index.html

setenforce 0              ## seLinux 비활성화(꺼야함)
setenforce 1              ## seLinux 활성화
getenforce
> Permissive

systemctl stop firewalld      ## 방화벽 비활성화
systemctl is-active firewalld
> inactive

podman run -d --name tang-httpd -p8080:8080 -v /root/htdocs/:/var/www/html  quay.io/fedora/httpd-24
podman container ls
curl localhost:8080

# 컨테이너는 기본적으로 이미지 기반으로 프로그램을 실행.
- hub.docker.io
- quay.io

podman run -d --pod apace -p 8080:8080 -v /root/container2/:/var/www/html --name container2 quay.io/fedora/httpd-24 ## --pod : 포드(컨테이너), -p : 포트 지정, -v : 바인딩(경로) 지정
getenforce 
> Permissive

# 포드만 명령어 구조
podman [____]  [_____]  [_____]   [____]
       명령어         옵션들           이미지
       verbe        options        target


# 컨테이너 실행 에러 시
podman run -d --pod apace -p 8080:8080 -v /root/container2/:/var/www/html --name container2 quay.io/fedora/httpd-24

# 컨테이너 이미지 고정 지정
nano /etc/containers/regsitries.conf
> unqualified-search-registries = ["quay.io"]

podman search centos
> quay.io/centos/centos              

# 이미지 태그 확인하기
- URI: http, https
- URL: 주소정보를 가지고 있음

dnf search container-tools      ## 컨테이너 툴 검색
dnf install container-tools -y  ## 컨테이너 룰 설치

## skopeo는 서로 다른 유형의 컨테이너 저장소 간에 컨테이너 이미지를 이동하는 도구 명령어
## skopeo는 Linux® 시스템, Windows, MacOS에서 컨테이너 이미지와 이미지 리포지토리를 조작, 검사, 서명, 전송하기 위한 툴
skopeo list-tags docker://quay.io/centos/centos  
                 ------ -----------------------
                 URI    URL
                 
podman run centos/centos:stream9
       --- ---------------------
        \   URL+TAG(version)
         \
          `---> subcommand

podman run -it centos/centos:stream9 /bin/bash
           ---
           \
            \
             `---> -i: interactive, -t: tty

podman run -it -p8080:80 centos/centos:stream9 /bin/bash
dnf install httpd -y
wget https://ko.wordpress.org/latest-ko_KR.tar.gz -O /var/www/html/wordpress.tar.gz
tar xzf /var/www/html/wordpress.tar.gz -C /var/www/html
mv /var/www/html/wordpress/* /var/www/html/

dnf install php php-mysqlnd -y  ## php, php-mysqld 설치
httpd -DFOREGROUND

# 아파치 httpd 명령어 옵션
httpd -t  : 아파치 웹 서버 환경 설정 파일의 문법적 오류를 검사한다. = ( service httpd configtest)
httpd -l : 아파치 웹 서버에 컴파일되어 포함된 모듈(static module)의 목록만 출력한다
httpd -S : 아파치 웹 서버에 설정된 가상호스트 정보를 출력한다.
httpd -k graceful : 아파치 웹서버에 연결된 접속을 차단하지 않고, 환경 설정 파일의 정보만 다시 읽어 들인다. = ( service httpd graceful)
httpd -X : 내부 디버깅 목적으로 하나의 프로세스로 실행되는 모드이다.
httpd -d serverroot : ServerRoot 변수를 초기값으로 지정한다.
httpd -D name : lfDefine 선언에서 사용되는 이름을 정의한다.
httpd -f [설정 파일명] : 설정 파일을 지정해서 아파치 실행한다.
httpd -v : 버전과 빌드 정보를 출력한다.
httpd -V : 버전과 빌드 정보, 컴파일시 셋팅 출력한다.

podman run -d -p443 -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=wordpress -e MYSQL_DATABASE=wordpress fedora/mariadb-105
## -e : mysql 계정, 패스워드, 데이터베이스 지정

podman container ls
> 42851e76a2e6  quay.io/fedora/httpd-24:latest     /usr/bin/run-http...  28 minutes ago  Up 28 minutes  0.0.0.0:46205->8080/tcp  nostalgic_babbage
> 56e780d92c38  quay.io/fedora/mariadb-105:latest  run-mysqld            27 minutes ago  Up 27 minutes  0.0.0.0:35885->8443/tcp  elegant_yalow
curl localhost:<PORT> | grep head -2
mysql -uwordpress -pwordpress -h <VM_IP> -P <PORT>

## container 1~3
mkdir ~/htdocs/
echo "Hello httpd-test-server" > ~/htdocs/index.html
podman run -d --name httpd-test-1 --rm -p 18081:8080 -v /root/htdocs/:/var/www/html fedora/httpd-24
podman run -d --name httpd-test-2 --rm -p 18082:8080 fedora/httpd-24
podman run -d --name httpd-test-3 --rm -p 18083:8080 fedora/httpd-24

podman exec -it httpd-test-2 ls -l /var/www/html
podman logs httpd-test-2


## container 4
mkdir ~/htdocs/
echo "Hello httpd-test-server" > ~/htdocs/index.html
podman run -d --name httpd-test-1 -p 18082:8080 -v /$USER/htdocs:/var/www/html

curl localhost:18081~18082

# 참고 포드만 사이트
https://developers.redhat.com/blog/2019/01/15/podman-managing-containers-pods

