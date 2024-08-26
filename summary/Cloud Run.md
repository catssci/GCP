> 🎯 Cloud Run이란?
> 컨테이너화된 애플리케이션을 관리형 서버리스 환경에서 실행할 수 있게 해주는 서비스

- 이 서비스는 확장성, 유연성, 관리 편의성 측면에서 많은 장점을 제공합니다.

## Cloud Run에 대한 주요 내용

### **1. 서버리스 아키텍처**

- **서버 관리 불필요**: 개발자는 서버를 직접 관리하지 않고, 애플리케이션 코드와 컨테이너 이미지를 배포하면 됩니다. 인프라 관리는 GCP가 자동으로 처리합니다.
- **자동 확장**: 트래픽이 증가할 때 자동으로 인스턴스를 확장하고, 트래픽이 감소하면 자동으로 인스턴스를 축소하여 비용을 절감할 수 있습니다.

### **2. 컨테이너 기반**

- **Docker 지원**: Cloud Run은 Docker 컨테이너를 실행합니다. 따라서 개발자는 언어나 프레임워크에 구애받지 않고 자신이 원하는 환경에서 코드를 작성할 수 있습니다.
- **이식성**: 컨테이너화된 애플리케이션은 로컬 환경, GKE(Google Kubernetes Engine), 또는 다른 클라우드 환경에서도 쉽게 배포할 수 있습니다.

### **3. 유연한 배포 옵션**

- **완전 관리형**: Cloud Run은 완전 관리형 환경에서 작동하므로, 개발자가 인프라 관리에 신경 쓸 필요 없이 애플리케이션 배포와 운영에 집중할 수 있습니다.
- **GKE 기반 Cloud Run**: Kubernetes 클러스터에서 실행되는 Cloud Run for Anthos를 선택할 수도 있습니다. 이 경우, GKE의 네트워크 정책 및 Kubernetes의 확장성과 같은 기능을 활용할 수 있습니다.

### **4. 간단한 배포 및 운영**

- **간단한 배포**: 컨테이너 이미지를 빌드하고, Cloud Run에 URL을 제공하여 배포하면 됩니다.
- **빠른 스타트업**: Cloud Run의 애플리케이션은 요청을 받으면 매우 빠르게 시작될 수 있도록 설계되어 있습니다.
- **HTTP 요청 처리**: Cloud Run은 기본적으로 HTTP 기반 요청을 처리하며, HTTP(S) 트리거를 사용하여 다양한 서비스를 연결할 수 있습니다.

### **5. 비용 효율성**

- **사용한 만큼 지불**: Cloud Run은 사용한 시간과 처리된 요청에 따라 비용이 부과되므로, 유휴 상태일 때 비용이 발생하지 않습니다.
- **정밀한 요금 책정**: 초 단위로 과금되며, 인스턴스가 사용 중인 리소스에 따라 비용이 산정됩니다.

### **6. 보안 및 관리 기능**

- **IAM 통합**: Cloud Run은 GCP의 Identity and Access Management(IAM)와 통합되어, 세밀한 액세스 제어를 할 수 있습니다.
- **비공개 서비스 지원**: Cloud Run 서비스를 비공개로 설정하여 내부 네트워크에서만 접근하도록 할 수 있습니다.
- **TLS/SSL 지원**: 기본적으로 HTTPS를 지원하여, 트래픽의 보안을 강화할 수 있습니다.

### **7. 개발자 도구와 통합**

- **Cloud Build 통합**: Cloud Run은 GCP의 Cloud Build와 통합되어 CI/CD 파이프라인에서 자동화된 배포가 가능합니다.
- **로깅 및 모니터링**: Cloud Logging 및 Cloud Monitoring을 통해 애플리케이션의 성능과 상태를 모니터링할 수 있습니다.

### **8. 사용 사례**

- **웹 애플리케이션**: 사용자 요청에 따라 확장 가능한 웹 애플리케이션 배포.
- **백엔드 API**: RESTful API 또는 GraphQL API 서버로 활용.
- **이벤트 기반 처리**: Pub/Sub와 같은 이벤트 기반 트리거로 작업을 자동화.

> Cloud Run은 특히 애플리케이션을 빠르게 배포하고 자동으로 확장 및 축소하는 기능이 필요한 상황에서 매우 유용 
> 
> 서버 관리의 복잡성을 줄이고, 컨테이너 기반의 이식성을 최대한 활용하면서도, 비용 효율적으로 애플리케이션을 운영할 수 있는 환경을 제공

## **Cloud Run을 사용한 간단한 웹 애플리케이션 설계, 배포 예시**

>🎯 배포 예시: 간단한 Python Flask 웹 애플리케이션을 Cloud Run 배포 
>	- 사용자에게 “Hello, World!” 메시지를 반환하는 단순한 웹 서비스


### **1. 애플리케이션 설계**

- 우선, Python과 Flask를 사용하여 간단한 웹 애플리케이션을 설계합니다.
- 이 코드는 기본적으로 Flask 애플리케이션을 설정하고, 루트 경로(”/”)에 “Hello, World!” 메시지를 반환하는 간단한 API를 제공합니다.

```python
# app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello, World!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
```

### **2. Dockerfile 작성**

- Cloud Run은 컨테이너 이미지를 실행하므로, 애플리케이션을 Docker 컨테이너로 패키징해야 합니다. 이를 위해 Dockerfile을 작성합니다.

```python
# Dockerfile
# 베이스 이미지로 Python 3.9 사용
FROM python:3.9-slim

# 작업 디렉토리 설정
WORKDIR /app

# 종속성 설치
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

# 애플리케이션 코드 복사
COPY . .

# 컨테이너가 실행될 때 실행할 명령어
CMD ["python", "app.py"]
```

- requirements.txt 파일은 Flask 종속성을 포함합니다:

```python
Flask==2.0.3
```

### **3. GCP 프로젝트 설정**

1. **GCP 콘솔에 로그인**하고 새로운 프로젝트를 생성하거나 기존 프로젝트를 사용합니다.
2. **Cloud Run API 활성화**: Cloud Run을 사용하려면 해당 API를 활성화해야 합니다.
3. **Cloud Build 및 Container Registry API 활성화**: 애플리케이션 이미지를 빌드하고 저장하기 위해 필요합니다.
4. **gcloud CLI 설치**: 로컬에서 GCP와 상호작용할 수 있도록 설정합니다.

```bash
gcloud init
```

- 프로젝트를 선택하고, 인증을 완료합니다.

### **4. 컨테이너 이미지 빌드 및 푸시**

- GCP의 Container Registry에 이미지를 빌드하고 푸시합니다.

```bash
gcloud builds submit --tag gcr.io/[PROJECT-ID]/hello-world
```

- [PROJECT-ID]는 GCP 프로젝트 ID로 대체합니다. 이 명령어는 Docker 이미지를 빌드하고 Container Registry에 푸시합니다.

### **5. Cloud Run에 배포**

- 컨테이너 이미지를 Cloud Run에 배포합니다.

```bash
gcloud run deploy --image gcr.io/[PROJECT-ID]/hello-world --platform managed
```

- **서비스 이름**: 배포하는 서비스의 이름을 입력합니다.
- **리전 선택**: 서비스가 배포될 리전을 선택합니다.
- **비공개/공개 설정**: 이 예시에서는 서비스를 외부에서 접근할 수 있도록 설정합니다.
- 이 과정이 완료되면, Cloud Run이 제공하는 URL이 반환됩니다. 이 URL을 통해 서비스에 접근할 수 있습니다.

### **6. 애플리케이션 사용**

- 이제 브라우저나 cURL을 사용하여 배포된 서비스에 접근할 수 있습니다.

```bash
curl https://[YOUR-CLOUD-RUN-URL]
```

- 브라우저에 URL을 입력하거나 위 명령어를 터미널에 입력하면, “Hello, World!” 메시지가 표시됩니다.

### **7. 비용 관리 및 모니터링**

- Cloud Run은 사용한 만큼 비용을 지불하는 방식이므로, 사용하지 않을 때 자동으로 확장/축소가 이루어져 비용을 절감할 수 있습니다.
- GCP 콘솔에서 **Cloud Monitoring**을 통해 애플리케이션의 상태 및 성능을 모니터링하고, **Cloud Logging**을 통해 로그를 분석할 수 있습니다.

> 이 간단한 예시를 통해 Cloud Run을 사용하여 웹 애플리케이션을 빠르게 설계, 배포, 사용하는 방법 이해
> Cloud Run은 특히 확장성 있는 서비스를 쉽게 배포하고, 서버 관리를 최소화하는 데 매우 유용
