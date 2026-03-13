### Context
터미널 기반 AI 에이전트 도구인 Claude Code(CLI)를 iTerm2와 tmux 조합에서 사용할 때, 멀티라인 입력을 위한 `Option+Enter` 줄바꿈 기능이 작동하지 않고 즉시 실행되는 문제가 발생함. 이는 iTerm2의 기본 Option 키 설정이 tmux에 적절한 메타(Meta) 시퀀스를 전달하지 못하기 때문임.

### Core
*   **iTerm2 프로필 설정 수정**:
    *   경로: `Settings` > `Profiles` > `Keys` > `General`
    *   `Left Option Key` 설정을 `Normal`에서 `Esc+`로 변경하여 Option 키 입력을 Escape 시퀀스로 변환하도록 함.
*   **tmux 구성 파일 설정 (~/.tmux.conf)**:
    *   아래의 바인딩을 추가하여 Meta+Enter 입력을 명시적으로 정의함:
    ```tmux
    # Option+Enter 입력을 Escape와 Enter 시퀀스로 매핑하여 애플리케이션에 전달
    bind-key -n M-Enter send-keys Escape Enter
    ```
*   **설정 적용**:
    *   tmux 세션 내에서 명령 실행: `tmux source-file ~/.tmux.conf`

### Insight
iTerm2의 `Normal` 설정은 macOS 특수 문자를 전송하기 때문에 tmux나 Linux 기반 CLI 도구에서 메타 키로 인식되지 않는 경우가 많음. `Esc+` 설정을 통해 `\e` 접두사를 붙여 전송하면 tmux가 이를 `M-`(Meta) 수식어로 해석할 수 있음. 특히 Claude Code와 같은 최신 CLI 도구들은 복잡한 입력을 처리하기 위해 이러한 시퀀스를 사용하므로, 멀티플렉서(tmux) 환경에서는 명시적인 키 바인딩(`bind-key`)을 통해 TTY 시퀀스 전달의 무결성을 보장해야 함.

**출처:**
*   [Claude Code Documentation - Key Bindings](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code)
*   [iTerm2 Documentation - Profiles Keys](https://iterm2.com/documentation-preferences-profiles-keys.html)
*   [tmux Manual - Key Bindings](https://man7.org/linux/man-pages/man1/tmux.1.html#KEY_BINDINGS)