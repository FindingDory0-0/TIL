# HAProxy HTTP 헬스체크로 백엔드 장애 정확히 감지하기

### Context

HAProxy에서 백엔드 서버의 상태를 확인할 때, 기본 TCP 체크만 사용하면 **포트가 열려 있는지만 확인**한다. 이 경우 애플리케이션이 실제로는 502 에러를 반환하고 있어도 TCP 연결 자체는 성공하므로, HAProxy는 해당 서버를 정상으로 판단하고 트래픽을 계속 전달한다. 이는 장애 상황에서 사용자에게 에러 응답이 그대로 노출되는 심각한 문제로 이어진다.

### Core

`option httpchk`를 사용하면 TCP 레벨이 아닌 HTTP 레벨에서 헬스체크를 수행한다. HAProxy가 지정된 URI로 HTTP 요청을 보내고, 응답 상태 코드가 2xx 또는 3xx 범위일 때만 서버를 정상으로 판단한다.

```haproxy
backend app_servers
    balance roundrobin
    option httpchk GET /health

    server app1 192.168.1.11:8080 check inter 3s fall 3 rise 2
    server app2 192.168.1.12:8080 check inter 3s fall 3 rise 2
```

**각 파라미터 설명:**

* `option httpchk GET /health` — HTTP GET 메서드로 `/health` 엔드포인트에 요청을 보내 상태를 확인한다.
* `check` — 해당 서버에 헬스체크를 활성화한다.
* `inter 3s` — 헬스체크 주기를 3초로 설정한다. (기본값: 2초)
* `fall 3` — 연속 3회 실패 시 서버를 비정상(DOWN)으로 전환한다. (기본값: 3)
* `rise 2` — 비정상 상태의 서버가 연속 2회 성공하면 정상(UP)으로 복귀시킨다. (기본값: 2)

**TCP 체크 vs HTTP 체크 비교:**

| 구분 | TCP 체크 | HTTP 체크 |
|---|---|---|
| 확인 수준 | 전송 계층(L4) | 애플리케이션 계층(L7) |
| 판단 기준 | 포트 연결 성공 여부 | HTTP 응답 코드 (2xx/3xx) |
| 앱 장애 감지 | 불가능 | 가능 |
| 설정 방법 | `check` (기본) | `option httpchk` 추가 |

### Insight

* TCP 체크는 단순히 포트가 열려있는지만 확인하므로, 웹 애플리케이션 환경에서는 **반드시 `option httpchk`를 사용해야** 실제 서비스 가용성을 정확히 판단할 수 있다.
* `/health` 엔드포인트는 애플리케이션에서 별도로 구현해야 한다. DB 연결, 외부 의존성 상태 등을 함께 확인하도록 설계하면 더 정밀한 헬스체크가 가능하다.
* `inter`, `fall`, `rise` 값은 트레이드오프가 존재한다. `inter`를 짧게, `fall`을 낮게 설정하면 장애 감지는 빨라지지만 일시적 지연(Transient Failure)에도 서버가 제외될 수 있다. 일반적으로 `inter 3~5s`, `fall 3`, `rise 2`가 권장되는 기본값이다.
* 장애 감지까지 걸리는 최대 시간은 `inter × fall`로 계산된다. 위 설정 기준으로는 3s × 3 = **최대 9초** 내에 비정상 서버가 로드밸런싱 풀에서 제외된다.

**출처:** [Health checks | HAProxy config tutorials](https://www.haproxy.com/documentation/haproxy-configuration-tutorials/reliability/health-checks/), [A Guide to HAProxy Health Checks for High Availability](https://www.haproxy.com/blog/how-to-enable-health-checks-in-haproxy)