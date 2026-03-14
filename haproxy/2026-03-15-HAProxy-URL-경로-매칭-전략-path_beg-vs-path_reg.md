### Context
HAProxy를 설정할 때 요청 URL의 경로(Path)에 따라 서로 다른 백엔드 서버로 트래픽을 분산해야 하는 상황이 빈번하게 발생합니다. 이때 특정 경로를 기준으로 조건문(ACL)을 작성하게 되는데, 경로 매칭 방식에 따라 `path_beg`와 `path_reg` 중 적절한 것을 선택해야 합니다. 두 방식의 차이를 정확히 이해하지 못하면 의도하지 않은 경로가 매칭되거나 라우팅이 누락될 수 있습니다.

### Core
HAProxy 설정 파일(`haproxy.cfg`)에서 URL 경로를 기반으로 트래픽을 분기하는 예시입니다.

* **path_beg (Path Beginning)**: 특정 문자열로 시작하는지 검사합니다. 간단한 접두사 매칭에 적합하며 성능이 빠릅니다.
* **path_reg (Path Regular Expression)**: 정규 표현식(Regular Expression)을 사용하여 복잡한 패턴을 매칭합니다.

```haproxy
# ACL 설정 예시
acl is_api path_beg /api
acl is_legacy_user path_reg ^/user/[0-9]+/profile

# 백엔드 라우팅
use_backend api_server if is_api
use_backend legacy_server if is_legacy_user
```

### Insight
`path_beg`는 설정이 간편하고 처리 속도가 매우 빠르기 때문에, 단순히 경로의 접두사(Prefix)만 확인하면 되는 경우(예: `/api`, `/static`) 최우선적으로 고려해야 합니다. 반면, `path_reg`는 정규식을 사용하므로 숫자나 특정 패턴이 포함된 동적 경로를 처리할 때 유용하지만, 복잡한 정규식은 연산 비용이 높으므로 성능 최적화를 고려해야 합니다. 따라서 단순 매칭은 `path_beg`를 사용하고, 복잡한 패턴 정의가 필요할 때만 `path_reg`를 사용하는 전략이 필요합니다.

**출처:** [HAProxy Configuration Manual - ACLs and stick-tables](https://docs.haproxy.org/2.8/configuration.html#7.3.1-path_beg)
