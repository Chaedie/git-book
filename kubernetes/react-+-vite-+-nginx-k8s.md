# React + Vite + Nginx K8S 배포

사내 프로젝트의 v2 마이그레이션 작업을 진행중이다.  v1의 경우 centOS 환경에서 Nginx 로 react 앱을 서빙했다고 한다. v2에서는 모든 인프라를 docker swarm 으로 변경한다고 한다. 그런데 docker 로 배포한 react app 에서 api routing 문제 해결을 요청 받았다. 아쉽게도 인프라 접근 권한이 없는 상황이라 우선적으로 local에서 k8s, minikube로 react 배포를 진행해보았다.&#x20;

아래는 배포하며 기록한 내용이다.

#### 1. nginx - react app

* 엔진엑스 react vite ts

```bash
FROM nginx:1.17
COPY dist/ /usr/share/nginx/html
```

* 엔진엑스 react vite ts - node build 같이 (느림)

```bash
# STEP1: node build

FROM node:20 AS builder
WORKDIR /app

COPY package.json yarn.lock ./
RUN yarn --frozen-lockfile

COPY . .
RUN yarn build

# STEP2: nginx 배포
FROM nginx:alpine
COPY --from=builder /app/dist/ /usr/share/nginx/html
```

#### 2. nginx - react - 404 not found error 대응

```bash
server {
	listen 80;
	server_name localhost;
	
	location / {
		root /usr/share/nginx/html;
		index index.html index.htm;
		try_files $uri $uri/ /index.html;
	}
	
	error_page 500 502 503 504 /50x.html;
	location = /50x.html {
		root /usr/share/nginx/html;
	}
}
```

```bash
# STEP 1: nginx 배포

FROM nginx:1.17

RUN rm /etc/nginx/conf.d/default.conf
COPY ./front-app.conf /etc/nginx/conf.d/default.conf

COPY packages/web/dist/ /usr/share/nginx/html
```

#### 3. K8S - Deploy, Service YAML 작성

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: react-test
spec:
  replicas: 3
  selector:
    matchLabels:
      app: react-test
  template:
    metadata:
      labels:
        app: react-test
    spec:
      containers:
      - name: react-test
        image: <image-name>

---
apiVersion: v1
kind: Service
metadata:
  name: react-test-service
spec:
  type: NodePort
  ports:
    - port: 80
  selector:
    app: react-test
```
