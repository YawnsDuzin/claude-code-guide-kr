# 인프라 기술스택 가이드

> Claude Code를 활용한 인프라 관리 가이드입니다.
> Docker, Kubernetes, Terraform, CI/CD를 다룹니다.

---

## 1. Docker

### CLAUDE.md 설정 예시

```markdown
# Docker 프로젝트

## 환경
- Docker 24+ / Docker Compose v2
- 베이스 이미지: node:20-alpine (Node), python:3.12-slim (Python)
- 프라이빗 레지스트리: ghcr.io/myorg

## 규칙
- 멀티스테이지 빌드 필수
- non-root 유저로 실행
- .dockerignore 유지
- 이미지 크기 최소화 (alpine/slim 베이스)
- HEALTHCHECK 포함
```

### Dockerfile 생성

**프롬프트:**
```
이 Node.js 프로젝트의 프로덕션 Dockerfile을 만들어줘.
- 멀티스테이지 빌드 (빌드 → 런타임)
- non-root 유저
- 헬스체크
- 이미지 크기 최소화
```

**생성되는 코드:**

```dockerfile
# Build stage
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production && cp -R node_modules /prod_modules
RUN npm ci
COPY . .
RUN npm run build

# Runtime stage
FROM node:20-alpine AS runner
RUN addgroup -g 1001 -S appgroup && adduser -S appuser -u 1001 -G appgroup
WORKDIR /app

COPY --from=builder /prod_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package.json ./

USER appuser
EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s \
  CMD wget --no-verbose --tries=1 --spider http://localhost:3000/health || exit 1

CMD ["node", "dist/main.js"]
```

### Docker Compose 생성

**프롬프트:**
```
개발 환경용 docker-compose.yml을 만들어줘.
- API 서버 (Node.js)
- PostgreSQL
- Redis
- 볼륨 마운트로 핫 리로드
```

```yaml
# docker-compose.yml
services:
  api:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - ./src:/app/src
      - /app/node_modules
    environment:
      DATABASE_URL: postgresql://postgres:password@db:5432/myapp
      REDIS_URL: redis://redis:6379
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started

  db:
    image: postgres:16-alpine
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: myapp
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 3s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  postgres_data:
```

---

## 2. Kubernetes

### CLAUDE.md 설정 예시

```markdown
# Kubernetes 프로젝트

## 환경
- K8s 1.29+
- Helm 3
- 네임스페이스: dev, staging, prod
- Ingress: nginx-ingress
- 시크릿 관리: Sealed Secrets

## 규칙
- 리소스 limits/requests 필수 설정
- 라이브니스/레디니스 프로브 필수
- PDB (PodDisruptionBudget) 설정
- 환경별 values 파일 분리
```

### K8s 매니페스트 생성

**프롬프트:**
```
이 API 서버의 Kubernetes 매니페스트를 작성해줘.
- Deployment (3 replicas)
- Service (ClusterIP)
- Ingress
- HPA (CPU 70% 기준, 3~10 replicas)
- ConfigMap, Secret
```

**생성되는 코드:**

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-server
  labels:
    app: api-server
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api-server
  template:
    metadata:
      labels:
        app: api-server
    spec:
      containers:
        - name: api
          image: ghcr.io/myorg/api-server:latest
          ports:
            - containerPort: 3000
          resources:
            requests:
              cpu: 100m
              memory: 128Mi
            limits:
              cpu: 500m
              memory: 512Mi
          livenessProbe:
            httpGet:
              path: /health
              port: 3000
            initialDelaySeconds: 10
            periodSeconds: 30
          readinessProbe:
            httpGet:
              path: /ready
              port: 3000
            initialDelaySeconds: 5
            periodSeconds: 10
          envFrom:
            - configMapRef:
                name: api-config
            - secretRef:
                name: api-secrets
---
# hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-server-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api-server
  minReplicas: 3
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

### Helm 차트 생성

**프롬프트:**
```
이 매니페스트들을 Helm 차트로 변환해줘.
- values.yaml로 환경별 설정 분리
- dev, staging, prod 값 파일
- _helpers.tpl로 공통 라벨/이름
```

### 디버깅

**프롬프트:**
```
Pod가 CrashLoopBackOff 상태야. 디버깅 방법을 알려줘.
kubectl logs, describe, exec 명령어 포함해서.
```

---

## 3. Terraform

### CLAUDE.md 설정 예시

```markdown
# Terraform 프로젝트

## 환경
- Terraform 1.7+
- Provider: AWS (ap-northeast-2)
- Backend: S3 + DynamoDB (상태 관리)
- 모듈 구조 사용

## 프로젝트 구조
terraform/
├── modules/
│   ├── vpc/
│   ├── ecs/
│   ├── rds/
│   └── cdn/
├── environments/
│   ├── dev/
│   ├── staging/
│   └── prod/
└── shared/          # 공통 데이터 소스

## 규칙
- 모듈화 필수 (재사용 가능한 단위)
- 변수에 description, type, validation 포함
- output으로 다른 모듈에 값 전달
- terraform fmt, validate 통과 필수

## 명령어
- 초기화: terraform init
- 계획: terraform plan -var-file=dev.tfvars
- 적용: terraform apply -var-file=dev.tfvars
- 검증: terraform validate
```

### 리소스 정의 예제

**프롬프트:**
```
AWS ECS Fargate로 API 서버를 배포하는 Terraform 모듈을 만들어줘.
- VPC, 서브넷, ALB 포함
- ECS 클러스터, 서비스, 태스크 정의
- CloudWatch 로그 설정
- Auto Scaling
```

**생성되는 코드 예시:**

```hcl
# modules/ecs/main.tf
resource "aws_ecs_cluster" "main" {
  name = "${var.project_name}-${var.environment}"

  setting {
    name  = "containerInsights"
    value = "enabled"
  }
}

resource "aws_ecs_task_definition" "api" {
  family                   = "${var.project_name}-api"
  network_mode             = "awsvpc"
  requires_compatibilities = ["FARGATE"]
  cpu                      = var.cpu
  memory                   = var.memory
  execution_role_arn       = aws_iam_role.ecs_execution.arn
  task_role_arn            = aws_iam_role.ecs_task.arn

  container_definitions = jsonencode([
    {
      name  = "api"
      image = "${var.ecr_repo_url}:${var.image_tag}"
      portMappings = [
        {
          containerPort = var.container_port
          protocol      = "tcp"
        }
      ]
      logConfiguration = {
        logDriver = "awslogs"
        options = {
          awslogs-group         = aws_cloudwatch_log_group.api.name
          awslogs-region        = var.aws_region
          awslogs-stream-prefix = "api"
        }
      }
      environment = var.environment_variables
    }
  ])
}

resource "aws_ecs_service" "api" {
  name            = "${var.project_name}-api"
  cluster         = aws_ecs_cluster.main.id
  task_definition = aws_ecs_task_definition.api.arn
  desired_count   = var.desired_count
  launch_type     = "FARGATE"

  network_configuration {
    subnets          = var.private_subnet_ids
    security_groups  = [aws_security_group.ecs.id]
    assign_public_ip = false
  }

  load_balancer {
    target_group_arn = aws_lb_target_group.api.arn
    container_name   = "api"
    container_port   = var.container_port
  }
}
```

```hcl
# modules/ecs/variables.tf
variable "project_name" {
  description = "프로젝트 이름"
  type        = string
}

variable "environment" {
  description = "배포 환경 (dev, staging, prod)"
  type        = string
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "환경은 dev, staging, prod 중 하나여야 합니다."
  }
}

variable "cpu" {
  description = "Fargate 태스크 CPU 단위"
  type        = number
  default     = 256
}

variable "memory" {
  description = "Fargate 태스크 메모리 (MB)"
  type        = number
  default     = 512
}
```

---

## 4. CI/CD (GitHub Actions)

### GitHub Actions 파이프라인 생성

**프롬프트:**
```
GitHub Actions CI/CD 파이프라인을 만들어줘.
- PR: 린트, 테스트, 빌드 검증
- main 머지: Docker 빌드 → ECR 푸시 → ECS 배포
- 환경별 배포 (dev → staging → prod)
- 슬랙 알림
```

**생성되는 코드:**

```yaml
# .github/workflows/ci.yml
name: CI

on:
  pull_request:
    branches: [main, develop]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run lint
      - run: npm run typecheck
      - run: npm test -- --coverage

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: docker/build-push-action@v5
        with:
          context: .
          push: false
          tags: myapp:test
```

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
    steps:
      - uses: actions/checkout@v4

      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          aws-region: ap-northeast-2

      - uses: aws-actions/amazon-ecr-login@v2
        id: ecr

      - uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.ecr.outputs.registry }}/myapp:${{ github.sha }}

      - name: Deploy to ECS
        run: |
          aws ecs update-service \
            --cluster myapp-prod \
            --service myapp-api \
            --force-new-deployment

      - name: Notify Slack
        if: always()
        uses: slackapi/slack-github-action@v1
        with:
          payload: |
            {"text": "배포 ${{ job.status }}: ${{ github.sha }}"}
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

---

## 5. 팁과 주의사항

| 상황 | 팁 |
|------|-----|
| 시크릿 관리 | `.env` 파일은 절대 커밋하지 않도록 CLAUDE.md에 명시 |
| Terraform 상태 | `terraform plan` 결과를 Claude에게 보여주고 검토 요청 |
| K8s 디버깅 | Pod 로그, describe 결과를 Claude에게 전달 |
| Docker 최적화 | "이 Docker 이미지 크기를 줄여줘" 요청 |
| CI/CD 실패 | 에러 로그 전체를 Claude에게 붙여넣기 |

> **다음 단계:** [비교 가이드](../06-comparison/01-vs-github-copilot.md)로 이동하여 다른 도구와의 비교를 확인하세요.
