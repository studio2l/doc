# firewall-cmd

CentOS7부터 방화벽에 규칙을 수정할 때 firewall-cmd 명령어를 사용한다. iptables보다 편리한 것 같다.

항상 reload 해주어야 변경된 규칙이 적용된다.

```bash
# 서비스 추가
firewall-cmd --permanent --zone=public --add-service=http
firewall-cmd --permanent --zone=public --add-service=https
firewall-cmd --reload

# 포트 직접 추가
firewall-cmd --permanent --zone=public --add-port=8080/tcp
firewall-cmd --reload
```
