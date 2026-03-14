### Context
Claude Code는 Anthropic에서 제공하는 CLI 기반의 AI 코딩 에이전트입니다. 기본적으로 Claude Code는 파일 수정, 터미널 명령 실행, 네트워크 요청 등 시스템에 영향을 줄 수 있는 모든 작업에 대해 사용자의 수동 승인(Approval)을 요구합니다. 이는 보안상 안전하지만, 복잡한 리팩토링이나 반복적인 작업을 수행할 때 작업 흐름을 끊는 요소가 됩니다. 신뢰할 수 있는 프로젝트 환경이나 샌드박스 내에서 생산성을 극대화하기 위해 이 승인 절차를 생략하는 방법을 정리합니다.

### Core
Claude Code 실행 시 `--dangerously-skip-permissions` 플래그를 추가하면 모든 도구 호출(Tool Calling)에 대해 사용자에게 묻지 않고 즉시 작업을 수행합니다.

*   **기본 실행 (매번 승인 필요)**
    ```bash
    claude
    ```

*   **무승인 모드 실행 (자동 승인)**
    ```bash
    claude --dangerously-skip-permissions
    ```

*   **특정 작업 시나리오**
    *   대규모 코드베이스의 일괄적인 타입 수정
    *   여러 파일에 걸친 공통 라이브러리 업데이트
    *   연속적인 테스트 실행 및 디버깅 루프 자동화

### Insight
이 플래그는 이름에서 알 수 있듯이 '위험성'을 내포하고 있습니다. 에이전트가 예상치 못한 명령(`rm -rf /` 등)을 실행하거나 중요한 설정 파일을 잘못 수정할 경우 복구가 어려울 수 있습니다. 따라서 다음과 같은 안전 조치를 병행하는 것이 권장됩니다.

*   **Git 커밋 필수**: 작업을 시작하기 전 현재 상태를 반드시 커밋하여 `git reset --hard`로 복구할 수 있는 지점을 확보해야 합니다.
*   **환경 격리**: 로컬 머신보다는 Docker 컨테이너나 VM(가상 머신) 등 격리된 샌드박스 환경에서 사용할 때 가장 유용합니다.
*   **제한적 사용**: 자동화가 반드시 필요한 구간에서만 일시적으로 사용하고, 평상시에는 기본 승인 모드를 유지하는 것이 보안 가이드라인에 부합합니다.

**출처:** [Claude Code Guide - Anthropic Docs](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code)
**출처:** [Claude Code CLI Reference](https://www.npmjs.com/package/@anthropic-ai/claude-code)