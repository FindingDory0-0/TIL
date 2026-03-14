### Context
Claude Code에서 `/init` 명령어를 실행하면 프로젝트 루트 디렉토리에 `CLAUDE.md` 파일이 생성됩니다. 이 파일은 Claude Code 에이전트에게 프로젝트의 규칙, 컨벤션, 자주 사용하는 명령어 등을 사전에 학습시키는 핵심 설정 파일로 작동합니다. 대화 세션이 시작될 때마다 에이전트가 이 내용을 우선적으로 참조하므로 컨텍스트 일관성을 유지하는 데 필수적입니다.

### Core
`CLAUDE.md` 파일은 마크다운 형식을 따르며, 다음과 같이 프로젝트의 핵심 정보를 구조화하여 작성하는 것이 효과적입니다.

```markdown
# 프로젝트 규칙 및 지침

* 언어: 모든 코드와 문서는 한국어로 작성한다.
* 스타일 가이드: Airbnb JavaScript 스타일 가이드를 준수한다.
* 명령어 모음:
  * 빌드: `npm run build`
  * 테스트: `npm run test`
  * 인프라 배포: `npm run deploy:prod`

# 기술 스택
* Framework: Next.js 15 (App Router)
* Language: TypeScript
* Styling: Tailwind CSS

# 주요 워크플로우
* 기능을 수정할 때마다 반드시 관련 단위 테스트를 실행할 것.
```

### Insight
`CLAUDE.md`를 활용하면 매번 에이전트에게 동일한 지침을 반복해서 입력할 필요가 없어 토큰 효율성이 높아지며, 팀 단위 협업 시 에이전트가 팀의 공통 컨벤션을 일관되게 적용하도록 강제할 수 있습니다. 이는 특히 대규모 프로젝트에서 에이전트의 '환각(Hallucination)'을 줄이고 프로젝트의 코드 품질을 균일하게 유지하는 매우 강력한 도구입니다.

**출처:** [Claude Code Documentation](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview)