# DNS 셑업

다음은 named 사용을 가정한 DNS 셑업 방법이다.

DNS 서버를 셑업할 때는 마스터와 슬레이브로 나누어 셑업한다.

만일 도메인이 2l.com 이면 보통 아래와 같이 설정한다.

* 마스터: ns1.2l.com / 10.0.0.2
* 슬레이브: ns2.2l.com / 10.0.0.3

슬레이브는 마스터 서버의 zone 데이터를 받아서 캐시화한 후
마스터 서버가 죽었을 때 그 역할을 대신 하게 된다.

#### 마스터

/etc/named.conf 에 다음 줄을 추가한다.

```
zone "2l.com" IN {
	type master;
	file "2l.com.zone";
	allow-transfer { 10.0.0.3; };
};
```

이 때 2l.com.zone 파일은 /var/named 아래에 존재하게 된다.
만일 해당 디렉토리가 없다면 named.conf에서 options { directory } 경로를 확인한다.

#### 슬레이브

/etc/named.conf 에 다음 줄을 추가한다.

```
zone "2l.com" IN {
	type slave;
	file "2l.com.zone";
	masters { 10.0.0.2; };
};
```

#### 셑업시 문제점과 해결방법

##### dumping master file: tmp-w44pgkdsGd: open: permission denied

/var/named의 권한 문제인 줄 알았으나 selinux의 보안규칙 때문에 생긴 문제였다.

셸에서 다음 명령을 쳐서 named가 master_zone 파일을 고칠수 있도록 한다.

```
setsebool named_write_master_zones 1
```

