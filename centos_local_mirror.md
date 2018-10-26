# CentOS 내부 미러 레포지터리 생성

VFX 스튜디오 내의 대부분의 서버는 인터넷과 연결되어 있지 않다.

하지만 CentOS 서버를 관리하다 보면 종종 프로그램을 레포지터리에서
다운로드 받아야 할 때가 있다.

다음은 CentOS 미러 레포지터리를 로컬 서버에 만드는 방법이다.

### 서버 셑업

서버의 도메인 네임은 centos-mirror.2lfilm.com으로 가정한다.

서버 자체는 CentOS가 아니어도 상관없다.

```
# 현재 디렉토리 아래에 CentOS 레포지터리를 복제한다.
rsync -avz --delete rsync://ftp.kaist.ac.kr/CentOS/7/os/x86_64 CentOS/7/os
cd CentOS
# 우선 github.com/studio2l/file-server 에서 프로그램을 받은 후
file-server -addr :80
```

웹 브라우저로 localhost에 들어가 본 후 정적 서버가 잘 생성되었다면 서비스에 추가한다.
(서비스 추가는 linux_systemd_service.md 문서 참고)

### 클라이언트 셑업

일단 모든 *.repo 파일을 지운다. 이 파일들에는 다운로드 받을 수 있는 레포지터리의
위치가 기록되어 있지만 인터넷이 연결되어있지 않으면 쓸 수 없다.

```
cd /etc/yum.repos.d/
mkdir backup
mv *.repo backup
vi local.repo
```

아래는 local.repo의 예이다.

```
[CentOS-7-Local-Mirror]
name=CentOS 7 Local Mirror
baseurl=http://centos-mirror.2lfilm.com/7/os/x86_64
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

