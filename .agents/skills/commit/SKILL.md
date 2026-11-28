---
name: commit
description: Drafts a Conventional Commits message for all current git changes, iterates until the user approves, then commits everything. Use when the user asks to commit changes, write a commit message, or runs /commit.
---

# commit

현재 작업 트리의 모든 변경사항(staged, unstaged, untracked)을 하나의 커밋으로 정리한다.
사용자 승인 없이는 커밋도 push도 하지 않는다.

## 1. 변경사항 수집

```bash
git status --porcelain
git diff HEAD
git log --oneline -10
```

- `git diff HEAD`는 staged와 unstaged를 모두 포함한다. 길면 파일별로 나눠 읽는다(`git diff HEAD -- <path>`).
- untracked 파일은 `git status --porcelain`의 `??` 목록을 보고 직접 읽어 내용을 파악한다.
- `git log`는 기존 커밋 메시지의 언어와 어투를 확인하는 용도다.
- 변경이 없으면 "커밋할 변경사항이 없습니다"만 보고하고 끝낸다.

## 2. 메시지 작성

규격은 Conventional Commits: `<type>(<scope>): <subject>`

- type: feat, fix, docs, style, refactor, perf, test, build, ci, chore, revert
- scope: 바뀐 영역. 단일 영역이거나 애매하면 생략한다.
- subject: 명령형, 마침표 없음, 72자 이내
- 본문: 무엇을 바꿨는지보다 왜 바꿨는지를 적는다. 변경이 여러 파일에 걸치면 쓴다. 72자에서 줄바꿈한다.
- footer: `BREAKING CHANGE:`나 이슈 참조가 있을 때만 넣는다.
- 문장 언어와 어투는 `git log`에서 확인한 기존 커밋을 따른다.

서로 무관한 관심사가 섞여 있으면 보고할 때 한 줄로 알린다. 쪼개라는 지시가 없으면 하나로 커밋한다.

## 3. 보고와 수정

메시지 전체를 코드 블록으로 보여주고, 고칠 부분이 있는지 묻는다.

- 수정 지시를 받으면 해당 부분만 고쳐 전체 메시지를 다시 보여준다.
- "승인", "좋아", "커밋해" 같은 명시적 승인을 받은 뒤에만 다음 단계로 간다.

## 4. 커밋

```bash
git add -A
git commit -F - <<'EOF'
<승인된 메시지>
EOF
```

- `--no-verify`로 훅을 건너뛰지 않는다. 훅이 실패하면 출력을 보여주고 사용자에게 묻는다.
- 확인: `git log -1 --stat`으로 해시와 포함된 파일을 보고한다.

## 5. push

커밋 결과를 보고하면서 push할지 묻는다. 승인하면 push한다.

```bash
git push                 # upstream이 있을 때
git push -u origin HEAD  # upstream이 없을 때
```

- 승인 없이는 push하지 않는다.
- push가 거부되면(비-fast-forward, 보호 브랜치 등) 출력을 보여주고 어떻게 할지 묻는다. 강제 push는 사용자가 명시적으로 요청할 때만 하고, `--force-with-lease`를 쓴다.
