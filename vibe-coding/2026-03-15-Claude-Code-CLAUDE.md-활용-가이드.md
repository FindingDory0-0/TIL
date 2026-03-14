### Context
Claude Code를 사용하여 프로젝트를 초기화(`/init`)할 때 자동으로 생성되는 `CLAUDE.md` 파일은 에이전트의 동작 방식을 정의하는 핵심 설정 파일입니다. 프로젝트의 기술적 컨벤션, 자주 사용하는 명령어, 특정 비즈니스 로직 등을 기록해두면, Claude Code가 대화 세션을 시작할 때마다 이를 자동으로 컨텍스트로 불러와 에이전트의 응답 품질과 작업 일관성을 크게 향상시킬 수 있습니다.

### Core
`CLAUDE.md` 파일에는 다음과 같은 정보를 구조화하여 작성하는 것이 권장됩니다.

```markdown
# 프로젝트 컨벤션 및 지침

* 기술 스택: React, TypeScript, TailwindCSS
* 코드 스타일: 함수형 컴포넌트 우선, 명시적 타입 정의
* 주요 명령어:
  * 빌드: `npm run build`
  * 테스트: `npm test`
  * 배포: `npm run deploy`
* 도메인 규칙:
  * API 응답은 항상 `ApiResponse<T>` 제네릭 타입을 사용하세요.
  * 에러 핸들링은 전역 에러 바운더리를 활용하세요.
```

`CLAUDE.md` 파일이 존재할 경우, Claude Code는 에이전트가 작업을 수행하기 전 해당 파일의 내용을 먼저 읽고 작업 지침으로 반영합니다.

### Insight
`CLAUDE.md`를 활용하는 것은 LLM 에이전트 환경에서 시스템 프롬프트(System Prompt)를 프로젝트 레벨로 관리하는 것과 유사한 효과를 줍니다. 이를 통해 팀 전체가 공유하는 '에이전트용 문서'를 구축할 수 있으며, 신규 개발자나 에이전트가 프로젝트 환경에 빠르게 적응하도록 돕는 강력한 도구가 됩니다. 특히 복잡한 프로젝트 구조나 팀 특유의 빌드 프로세스가 있는 경우, 이를 문서화하여 에이전트와 동기화하는 것이 오답률을 낮추는 최선의 전략입니다.

**출처:** [Claude Code Documentation](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview)