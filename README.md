# Hacking-rice-eating-v1-bandit

bandit 1~20 mock tester

## 병합이 안 될 때 해결 방법

이 프로젝트는 실제 앱 코드가 `index.html` 한 파일에 거의 모두 들어 있습니다. 그래서 기존 `index.html`과 새 `index.html`의 같은 위치가 동시에 바뀌면 Git 또는 PR 화면에서 자동 병합이 실패할 수 있습니다.

### 가장 쉬운 방법: 새 파일로 통째로 교체

기존 파일에 꼭 유지해야 하는 별도 수정이 없다면, 현재 브랜치의 `index.html`을 그대로 사용해서 기존 파일을 교체하는 방식이 가장 안전합니다.

```bash
git checkout <이-PR-브랜치명> -- index.html README.md
git add index.html README.md
git commit -m "Apply Bandit simulator 1-20 update"
```

### 기존 수정도 같이 살려야 하는 경우

기존 `index.html`에 보존해야 할 수정이 있다면 아래 순서로 충돌 부분만 직접 정리하세요.

1. `<<<<<<<`, `=======`, `>>>>>>>` 표시가 있는 충돌 구간을 찾습니다.
2. 유지할 기존 코드와 새 Bandit 1~20 코드를 비교합니다.
3. 최종 파일에는 충돌 표시가 하나도 남지 않게 삭제합니다.
4. 아래 명령으로 검사합니다.

```bash
rg '^(<<<<<<<|=======|>>>>>>>)' index.html
python3 - <<'PY_SCRIPT'
from pathlib import Path
s = Path('index.html').read_text()
Path('/tmp/banditsim.js').write_text(s[s.index('<script>') + len('<script>'):s.index('</script>')])
PY_SCRIPT
node --check /tmp/banditsim.js
```

위 검사에서 `rg`가 아무것도 출력하지 않고 `node --check`가 조용히 종료되면 충돌 표시와 JavaScript 문법 오류가 없는 상태입니다.
