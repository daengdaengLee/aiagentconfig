# aiagentconfig

AI 에이전트 설정 모음.

## Pi 설정 예시

`.pi/agent/settings.json.example`은 새 환경에서 Pi를 셋업할 때 쓰는 예시 파일이다.
전역 설정 경로(`~/.pi/agent/settings.json`)를 그대로 미러링한 이름이라, 복사만 하면 된다.

```sh
cp .pi/agent/settings.json.example ~/.pi/agent/settings.json
```

복사한 뒤 Pi를 실행하면 `packages`에 적힌 패키지 중 설치되지 않은 것을 자동으로 설치한다.
수동으로 설치 상태를 맞추려면 다음 명령을 쓴다.

```sh
pi update --extensions
```

### 담긴 설정

| 키             | 설명                                                                                   |
|----------------|----------------------------------------------------------------------------------------|
| `$schema`      | 편집기 자동완성·검증용. 동작에 영향 없음                                               |
| `packages`     | 설치할 패키지 목록. 버전을 비워 뒀으므로 항상 최신을 설치한다                          |
| `defaultTools` | `["+codemode"]`. 기본 도구(`read`/`bash`/`edit`/`write`)를 유지한 채 `codemode`만 추가 |

`codemode`는 기본 비활성이라 이 설정이 없으면 켜지지 않는다.
기본값을 사용하는 설정은 넣지 않았다.

## Agent Skills 백업

`.agents/skills/`는 기본 Agent Skills를 백업해 두는 저장소다.
스킬 하나는 `.agents/skills/<name>/SKILL.md` 형태로 두고, 필요하면 같은 폴더에 참고 자료를 함께 둔다.

| name | description |
|------|-------------|

