# Systemd 서비스 등록

서비스 정보가 들어있는 `/etc/systemd/system/{서비스이름}.service` 파일을 생성한다.


#### 등록

다음은 prometheus 서비스를 등록하는 예제이다.

```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/home/prometheus/prom/prometheus # ... 추가 인수

[Install]
WantedBy=multi-user.target
```

Wants와 After 옵션을 `network-online.target`으로 하면
네트워크가 연결된 후에 이 서비스가 실행된다.

WantedBy가 `multi-user.target`이면 runlevel이 2 이상일 때 실행된다는 뜻이다.
runlevel 0, 1 일 때는 네트워크가 연결되지 않기 때문에 prometheus의 경우 실행의 의미가 없다.

파일이 수정될 때 마다 아래 명령을 실행한다.

```
sudo systemctl daemon-reload
```

이제 서비스를 시작해 보자.

```
sudo systemctl start prometheus
```

서비스가 시작되었는지 확인하려면 다음과 같이 친다.

```
sudo systemctl status prometheus
```

만일 서비스가 잘 시작되지 않았다면 `journalctl`을 이용하여 로그를 확인한다.

```
sudo journalctl # -u {서비스} 와 -f -n {줄수} 등의 옵션을 사용하여 더 편하게 확인할 수 있다.
```

만일 서비스가 잘 시작되었다면 컴퓨터가 시작할 때마다 서비스를 실행하도록 할 수 있다.

```
sudo systemctl enable prometheus
```


#### 해제

서비스 종료는

```
sudo systemctl stop prometheus
```

부팅시 서비스가 자동 실행되지 않도록 하려면

```
sudo systemctl disable promethues
```

를 실행한다.


#### 자주 접하는 문제들

프로그램이 직접은 실행 되지만 서비스로 실행하면 에러가 난다면,
두가지 문제점 중 하나일 가능성이 크다.

* 실행 권한이 없음
* 실행되는 프로그램이 읽고 쓸 디렉토리가 지정되지 않음

특히 두번째의 경우 필요한 파일을 읽기 못하거나 쓸 디렉토리에 대한 권한이 없어서
서비스가 종료되지만 이유가 잘 설명되지 않는 경우가 많기 때문에
서비스로 프로그램을 실행할 때는 항상 프로그램 옵션을 이용해 데이터 디렉토리를 정의해 주자.
