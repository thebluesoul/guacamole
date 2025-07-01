
# Guacamole + Keycloak Docker Compose

Apache Guacamole과 Keycloak을 사용한 통합 인증 시스템입니다.

## 구성 요소

### Keycloak 인증 서버
- **이미지**: `quay.io/keycloak/keycloak:latest`
- **포트**: 8090 (호스트) → 8080 (컨테이너)
- **데이터베이스**: MySQL 8.0
- **관리자 계정**: admin / admin123

### MySQL 데이터베이스
- **이미지**: `mysql:8.0`
- **데이터베이스명**: keycloak
- **사용자**: keycloak / keycloak123
- **문자셋**: utf8mb4

### Guacamole 원격 데스크톱
- **이미지**: `guacamole/guacamole:latest`
- **포트**: 8080 (호스트) → 8080 (컨테이너)
- **인증**: Keycloak OpenID Connect 연동

### Guacd 데몬
- **이미지**: `guacamole/guacd:latest`
- **역할**: 원격 데스크톱 연결 처리

## 실행 방법

### 1. 컨테이너 시작
```bash
docker-compose up -d
```

### 2. Keycloak 설정
1. http://localhost:8090 접속
2. 관리자 계정으로 로그인 (admin/admin123)
3. Realm 생성: `guav2`
4. Client 생성: `guav2-client`
5. Client 설정:
   - Root URL: `http://localhost:8080/guacamole/`
   - Valid redirect URIs: `http://localhost:8080/guacamole/*`

### 3. Guacamole 접속
- URL: http://localhost:8080/guacamole/
- Keycloak을 통한 SSO 로그인

## 환경 변수

### Keycloak
- `KC_DB`: mysql
- `KC_DB_URL`: JDBC 연결 URL
- `KEYCLOAK_ADMIN`: 관리자 계정
- `KC_HOSTNAME`: 호스트명 설정

### Guacamole
- `OPENID_ENABLED`: OpenID Connect 활성화
- `OPENID_AUTHORIZATION_ENDPOINT`: 인증 엔드포인트
- `OPENID_JWKS_ENDPOINT`: JWKS 엔드포인트
- `OPENID_ISSUER`: 토큰 발급자
- `OPENID_CLIENT_ID`: 클라이언트 ID

## 데이터 지속성

MySQL 데이터는 `./keycloak-db` 디렉토리에 저장됩니다.

## 포트 정보

- **8090**: Keycloak 관리 콘솔
- **8080**: Guacamole 웹 인터페이스
- **3306**: MySQL (내부 네트워크만)

## 주의사항

- 개발 환경용 설정입니다
- 프로덕션 환경에서는 SSL/TLS 설정 필요
- 데이터베이스 비밀번호 변경 권장
