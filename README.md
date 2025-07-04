# 📦 common-exception

Spring 기반 프로젝트에서 공통 예외 처리를 간편하게 구성할 수 있도록 도와주는 라이브러리입니다.  
`@RestControllerAdvice`를 기반으로 다양한 예외를 처리하고, 일관된 응답 형식을 제공합니다.

---


## ✅ 설치 방법

### 1. `build.gradle`

```groovy
repositories {
    mavenCentral()
    maven {
        url = uri("https://maven.pkg.github.com/terayukee/common-exception")
        credentials {
            username = project.findProperty("gpr.user") ?: System.getenv("USERNAME")
            password = project.findProperty("gpr.key") ?: System.getenv("GITHUB_TOKEN")
        }
    }
}

dependencies {
    implementation 'com.terayukee:common-exception:0.0.1-SNAPSHOT'
}
```

### 2. 인증 토큰 설정
아래를 gradle.properties 파일에 추가하거나, 환경변수로 설정하세요:

``` properties
gpr.user=your_github_username
gpr.key=your_personal_access_token
```
🔐 GitHub PAT는 classic token 형식이어야 하며, write:packages, read:packages 권한이 필요합니다.




## 🛠️ 사용 방법
### 1. ErrorCode 정의
```java
public enum ErrorCode {
    INVALID_REQUEST(400, "잘못된 요청입니다."),
    FORBIDDEN(403, "접근이 거부되었습니다."),
    INTERNAL_ERROR(500, "서버 내부 오류");

    private final int status;
    private final String message;

    ErrorCode(int status, String message) {
        this.status = status;
        this.message = message;
    }

    public int getStatus() { return status; }
    public String getMessage() { return message; }
}
```

### 2. BusinessException 정의
```java
public class BusinessException extends RuntimeException {
    private final ErrorCode errorCode;

    public BusinessException(ErrorCode errorCode) {
        super(errorCode.getMessage());
        this.errorCode = errorCode;
    }

    public ErrorCode getErrorCode() {
        return errorCode;
    }
}
```

### 3. 예외 던지기
```java
@GetMapping("/api/test")
public String test() {
    throw new BusinessException(ErrorCode.INVALID_REQUEST);
}
```

### 4. 예외 응답 예시
에러 발생 시 자동으로 아래와 같은 JSON 응답이 반환됩니다:

```json
{
  "success": false,
  "error": {
    "message": "잘못된 요청입니다.",
    "status": 400
  }
}
```
이 응답 포맷은 common-response 라이브러리를 통해 제공됩니다.


🧰 자동으로 처리되는 예외 목록
GlobalExceptionHandler는 아래 예외들을 처리합니다:
- BusinessException
- MethodArgumentNotValidException
- HttpRequestMethodNotSupportedException
- HttpMediaTypeNotSupportedException
- HttpMediaTypeNotAcceptableException
- MissingServletRequestParameterException
- NoHandlerFoundException
- AccessDeniedException
- Exception (기타 알 수 없는 예외)


🔗 관련 모듈
common-exception은 common-response에 의존합니다. 함께 설치하세요.

```groovy
implementation 'com.terayukee:common-response:0.0.1-SNAPSHOT'
```


## ℹ️ 해당 라이브러리는 GitHub Packages를 통해 배포됩니다.
>
> 패키지를 사용하려면:
> - GitHub 계정에서 PAT를 발급하고 (`read:packages` 권한 필요)
> - 이 repository에 read 권한이 있어야 합니다.
>
> 권한이 없을 경우, 작성자에게 요청해 주세요.
