# Shell Commands for CKA Test

#### 1. Alias for fast typing

```bash
alias k=kubectl
export do='--dry-run=client -o yaml'
```

#### 2. Output Options

```bash
k get pods | cat -n #output with line number
k get pods | grep Image: #find output of "Image:"
```

#### 3. NetworkPolicy Debugging

```bash
# Network Policy 설정 후 통신이 원하는대로 되는지 
# pod 내부에서 netcat 으로 확인 가능하다.
nc -v -z -w 2 secure-wervice 80
```

#### 4. BusyBox Command example

```bash
# busybox 로 sh 작성 시 example
...
command: ["/bin/sh", "-c"]
args:
- while true; do date >> /opt/time/time-check.log; sleep $TIME_FREQ; done
...
```
