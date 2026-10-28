# MoaMoa Backend (fork)

Fork of [KUIT-MoaMoa/MoaMoa-Backend](https://github.com/KUIT-MoaMoa/MoaMoa-Backend), the backend of MoaMoa, a web service built by a team from the KUIT developer club. I was the backend lead and the top contributor to this repository.

Period: Dec 2024 – Mar 2025

## My role

- **Backend lead.** Designed the API and the package structure, reviewed pull requests, and split work between the backend developers.
- **Authentication.** Implemented login with Spring Security, an OAuth2 client, and JWT. The frontend team first planned to keep tokens in local storage. Because of the XSS risk, we reviewed their code and the remaining schedule together and agreed on HttpOnly cookies with `Secure` and `SameSite=None`, plus CORS. I submitted the frontend request changes myself as a pull request.
- **Production cookie bug.** Just before release, the login cookie was not stored in production even though it worked locally. I checked the difference between the two environments in order (HTTPS, then CORS, then `SameSite`) and fixed it before the planned release.
- **Deployment and CI/CD.** Wrote the GitHub Actions pipelines:
  - CI on pushes and pull requests to `main` and `dev`: Gradle build, tests, and a test report on the pull request
  - CD: build a Docker image, push it to the registry, then connect to the server over SSH, pull the new image, and restart the container
- **Logging.** Set up centralized logging with Promtail, Loki, and Grafana because debugging the frontend-backend connection on the deployed servers was slow without logs.

## Stack

- Java 17, Spring Boot 3.4, Spring Data JPA, Spring Security, OAuth2 client, JWT (jjwt)
- MySQL (H2 for local development)
- WebSocket with STOMP, Spring Mail, springdoc OpenAPI
- Docker, Nginx with Let's Encrypt (certbot), GitHub Actions

## Architecture

```
GitHub Actions ──build & push──▶ Docker registry
       │ SSH
       ▼
App server:  Nginx (HTTPS) ──▶ Spring Boot container ──▶ MySQL
                                  │ /var/log/springboot
                              Promtail
                                  │ push
Monitor server:              Loki ──▶ Grafana
```

---

## Team conventions (original README)

### 📢 브랜치 전략
| 태그이름       | 내용                                          |
|------------|---------------------------------------------|
| `main`     | 실제 서비스에 배포될 브랜치                              |
| `dev `     | 테스트용 서버에 올라갈 브랜치                                   |

- 그 외의 브랜치들에 대해서는 각 수행할 역할에 맞게 브랜치 이름을 설정
- merge 이후에는 각 브랜치들은 삭제된다.
```
feat/기능명
```

### 📢 커밋 컨벤션

| 태그이름       | 내용                                          |
|------------|---------------------------------------------|
| `feat`     | 새로운 기능을 추가할 경우                              |
| `fix `     | 버그를 고친 경우                                   |
| `!hotfix`  | 급하게 치명적인 버그를 고쳐야하는 경우                       |
| `style`    | 코드 포맷 변경, 세미 콜론 누락, 코드 수정이 없는 경우            |
| `refactor` | 코드 리팩토링                                     |
| `comment`  | 필요한 주석 추가 및 변경                              |
| `docs`	    | 문서, Swagger 를 수정한 경우                        |
| `test`     | 테스트 추가, 테스트 리팩토링(프로덕션 코드 변경 X)              |
| `chore`	   | 빌드 태스트 업데이트, 패키지 매니저를 설정하는 경우(프로덕션 코드 변경 X) |
| `rename`   | 파일 혹은 폴더명을 수정하거나 옮기는 작업만인 경우                |
| `remove`   | 파일을 삭제하는 작업만 수행한 경우                         |
| `ci`       | 배포 방식 수정 및 새로 추가 / 기존 배포 스크립트 수정            |
```
feat: 프롬프트 출력 기능 추가
```
Service
---
[모아모아.pdf](doc%2F%EB%AA%A8%EC%95%84%EB%AA%A8%EC%95%84.pdf)

Infrastructure
---
- Application Server와 MySQL은 동일 vpc에 속해 있습니다!
![final-infra-real](https://github.com/user-attachments/assets/d702f7e2-d245-4a93-b3fb-f964a01ce3e4)

ERD
---
![erd](https://github.com/user-attachments/assets/d99709e1-15ae-4ae2-bd94-c02cf5864d6b)

Api Doc
---
https://www.notion.so/api-1969a947c0c48012bad6cd3c5560d4fc?pvs=4
