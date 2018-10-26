# DNS 셑업

다음은 named 사용을 가정한 DNS 셑업 방법이다.

DNS 서버를 셑업할 때는 마스터와 슬레이브로 나누어 셑업한다.

만일 도메인이 2lfilm.com 이면 보통 아래와 같이 설정한다.

* 마스터: ns1.2lfilm.com / 10.0.0.2
* 슬레이브: ns2.2lfilm.com / 10.0.0.3

슬레이브는 마스터 서버의 zone 데이터를 받아서 캐시화한 후
마스터 서버가 죽었을 때 그 역할을 대신 하게 된다.

#### 마스터

/etc/named.conf 에 다음 줄을 추가한다.

```
zone "2lfilm.com" IN {
	type master;
	file "2lfilm.com.zone";
	allow-transfer { 10.0.0.3; };
	also-notify { 10.0.0.3; };
};
```

다음은 각 옵션에 대한 간략한 설명이다.

* allow-transfer: 10.0.0.3가 2lfilm.com 존 데이터를 받아가는 것을 허락한다.
* also-notify: 2lfilm.com 존이 업데이트 되었을 때 10.0.0.3에 업데이트 사실을 알린다.

이 때 2lfilm.com.zone 파일은 /var/named 아래에 존재하게 된다.
만일 해당 디렉토리가 없다면 named.conf에서 options { directory } 경로를 확인한다.

#### 슬레이브

/etc/named.conf 에 다음 줄을 추가한다.

```
zone "2lfilm.com" IN {
	type slave;
	file "2lfilm.com.zone";
	masters { 10.0.0.2; };
};
```

#### 셑업시 문제점과 해결방법

##### 다음과 같은 에러가 나며 존 파일이 생성되지 않는다: dumping master file: tmp-w44pgkdsGd: open: permission denied

/var/named의 권한 문제인 줄 알았으나 selinux의 보안규칙 때문에 생긴 문제였다.

셸에서 다음 명령을 쳐서 named가 master_zone 파일을 고칠수 있도록 한다.

```
setsebool named_write_master_zones 1
```

