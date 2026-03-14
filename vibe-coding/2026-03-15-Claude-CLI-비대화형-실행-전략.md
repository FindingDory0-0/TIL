### Context
n8n의 Execute Command 노드와 같은 자동화 환경에서는 대화형 터미널(TTY)이 존재하지 않아, Claude CLI를 비대화형(non-interactive) 방식으로 실행해야 할 필요가 있습니다. 파이프(pipe)를 통해 입력을 전달하고 결과를 파일로 리다이렉트하는 표준적인 CLI 활용 패턴을 정리합니다.

### Core
Claude CLI를 비대화형으로 실행하기 위한 기본 구문은 다음과 같습니다.

```bash
echo "질문 내용" | claude -p --model opus --output-format text image1.png image2.png > output.txt
```

* `-p`: 비대화형(print) 모드를 활성화하여 추가 프롬프트 없이 결과를 즉시 출력합니다.
* `--output-format text`: TTY가 없는 환경에서 출력을 텍스트 형식으로 고정하여 오류를 방지합니다.
* `-c`: 필요한 경우 이전 대화의 컨텍스트를 유지하며 대화를 이어갈 수 있습니다.
* 파일 전달: 프롬프트 뒤, 리다이렉트(`>`) 앞에 파일 경로를 나열합니다. (이미지: PNG, JPG, WebP 및 텍스트 파일 지원, 최대 100개 또는 32MB 제한)

### Insight
Claude CLI를 파이프 방식으로 사용하면 복잡한 API 연동 없이도 쉘 스크립트나 n8n 워크플로우 내에서 강력한 모델을 즉시 호출할 수 있습니다. 특히 `--output-format text` 옵션은 자동화 환경(headless environment)에서 불필요한 ANSI 제어 문자나 대화형 UI 요소가 결과에 섞이는 것을 방지하여 후속 프로세싱을 원활하게 만듭니다. 대규모 컨텍스트가 필요한 작업에서는 `-c` 옵션을 활용하여 이전 실행의 맥락을 연결하는 것이 효과적입니다.

**출처:** [Claude Code Documentation](https://github.com/anthropics/claude-code)