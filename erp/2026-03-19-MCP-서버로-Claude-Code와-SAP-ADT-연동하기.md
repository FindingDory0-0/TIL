### Context

SAP ABAP 개발은 전통적으로 Eclipse 기반 ADT(ABAP Development Tools)나 SAP GUI에서 이루어져 왔다. 하지만 AI 코딩 도구의 발전으로, Claude Code 같은 LLM 기반 도구를 ABAP 개발에 활용하려는 수요가 커지고 있다. 문제는 Claude Code가 SAP 시스템의 소스 코드나 데이터 딕셔너리(Data Dictionary) 정보에 직접 접근할 수 없다는 점이다.

MCP(Model Context Protocol)는 Anthropic이 제안한 개방형 프로토콜로, LLM이 외부 시스템과 표준화된 방식으로 통신할 수 있게 해준다. `mcp-abap-adt` 서버를 활용하면 SAP의 ADT REST API(`/sap/bc/adt`)를 MCP 프로토콜로 감싸서 Claude Code가 ABAP 시스템과 직접 대화할 수 있다.

### Core

**아키텍처 개요**

```
Claude Code ←→ MCP Protocol ←→ mcp-abap-adt 서버 ←→ SAP ADT REST API ←→ SAP 시스템
```

**설치 및 설정 순서**

* Node.js LTS 버전과 Git이 사전 설치되어 있어야 한다
* SAP 시스템에서 트랜잭션 `SICF`를 통해 `/sap/bc/adt` 서비스가 활성화되어 있어야 한다

```bash
# 저장소 클론 및 빌드
git clone https://github.com/mario-andreschak/mcp-abap-adt
cd mcp-abap-adt
npm install
npm run build
```

* `.env.example`을 복사하여 `.env` 파일을 생성하고 SAP 접속 정보를 입력한다

```env
SAP_URL=https://your-sap-system.com:44300
SAP_USERNAME=your_username
SAP_PASSWORD=your_password
SAP_CLIENT=100
SAP_LANGUAGE=EN
```

* 비밀번호에 `#` 문자가 포함된 경우 반드시 따옴표로 감싸야 한다
* `.env` 파일은 `.gitignore`에 포함되어 있으므로 커밋되지 않는다

**Claude Code 연동 설정**

Claude Code의 MCP 설정 파일(`.claude/settings.json` 또는 프로젝트의 `.mcp.json`)에 서버를 등록한다.

```json
{
  "mcpServers": {
    "mcp-abap-adt": {
      "command": "node",
      "args": ["/absolute/path/to/mcp-abap-adt/dist/index.js"]
    }
  }
}
```

* 경로는 반드시 절대 경로로 지정해야 하며, Windows에서는 역슬래시를 이중(`\\`)으로 사용한다

**제공되는 주요 도구(Tools)**

| 도구명 | 기능 | 주요 파라미터 |
|---|---|---|
| `GetClass` | ABAP 클래스 소스 코드 조회 | `class_name` |
| `GetProgram` | ABAP 프로그램 소스 코드 조회 | `program_name` |
| `GetFunction` | 함수 모듈 조회 | `function_name`, `function_group` |
| `GetTable` | 테이블 구조(DDIC) 조회 | `table_name` |
| `GetTableContents` | 테이블 데이터 조회 | `table_name`, `max_rows` |
| `SearchObject` | ABAP 오브젝트 검색 | `query`, `maxResults` |
| `GetInterface` | 인터페이스 소스 코드 조회 | `interface_name` |
| `GetTransaction` | 트랜잭션 상세 정보 조회 | `transaction_name` |

* `GetTableContents`를 사용하려면 SAP 시스템에 커스텀 서비스 `/z_mcp_abap_adt/z_tablecontent`를 구현해야 한다

**디버그 및 검증**

```bash
# MCP Inspector로 서버 동작 확인
npx @modelcontextprotocol/inspector node dist/index.js

# 독립 실행 모드로 서버 기동
npm run start
```

### Insight

MCP 서버 방식의 핵심 장점은 SAP 시스템 접근을 로컬 서버가 중개하므로, 민감한 인증 정보가 LLM에 직접 노출되지 않는다는 점이다. Claude Code는 MCP 프로토콜을 통해 도구를 호출할 뿐, 실제 SAP 비밀번호나 세션 토큰을 알지 못한다.

실무 관점에서 이 도구가 특히 유용한 시나리오는 다음과 같다.

* 레거시 ABAP 코드 분석: `GetClass`나 `GetProgram`으로 소스를 가져온 뒤 Claude Code에게 코드 리뷰나 리팩토링 방향을 요청할 수 있다
* 데이터 딕셔너리 탐색: `GetTable`로 테이블 구조를 확인하면서 데이터 모델을 파악할 수 있다
* 코드 검색: `SearchObject`로 특정 패턴의 오브젝트를 빠르게 찾을 수 있다

다만, 현재 이 MCP 서버는 읽기(Read) 위주의 도구만 제공하며, ABAP 소스 코드를 직접 수정하거나 트랜스포트 요청(Transport Request)을 생성하는 기능은 제한적이다. 쓰기 기능이 필요하다면 `mcp-abap-abap-adt-api` 같은 확장 프로젝트를 검토해볼 수 있다.

**출처:**
* [mario-andreschak/mcp-abap-adt - GitHub](https://github.com/mario-andreschak/mcp-abap-adt)
* [ABAP Development Tools (ADT) MCP Server - Awesome MCP Servers](https://mcpservers.org/servers/mario-andreschak/mcp-abap-adt)
* [Installing and extending an ABAP MCP Server by vibe-coding with Claude Code - SAP Community](https://community.sap.com/t5/artificial-intelligence-blogs-posts/installing-and-extending-an-abap-mcp-server-by-vibe-coding-with-claude-code/ba-p/14343600)
* [mario-andreschak/mcp-abap-abap-adt-api - GitHub](https://github.com/mario-andreschak/mcp-abap-abap-adt-api)