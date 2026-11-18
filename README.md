# skill-scout (스킬 감별사)

남이 만든 에이전트 스킬을 찾고, 깔기 전에 세 가지를 판정한다: 믿을 만한가, 안전한가, 우리에게 맞는가. 쓰기로 하면 프로젝트에 맞게 고쳐서 하네스에 넣는 것까지 한다.

Claude Code용과 Codex용 두 판이 들어 있다. 절차는 같고, 도구 이름과 스킬을 두는 경로만 다르다.

## 하는 일

1. **찾기**: 브라우저로 skillsmp.com을 검색해 후보를 5개 안으로 추린다. 검색어만 넣지 않고 영역, 카테고리, 직업, 최근 업데이트 필터를 요청에서 스스로 골라 함께 건다(콘텐츠 언어는 걸지 않는다). 필터를 너무 많이 걸면 결과가 0개가 되므로 단계로 좁힌다.
2. **출처 확인**: 사이트에 적힌 별 수를 믿지 않고 원본 GitHub 저장소를 `gh api`로 직접 조회한다.
3. **보안 점검**: 복제하거나 실행하지 않고 스킬 폴더의 모든 파일을 읽어 숨은 지시, 외부 전송, 자격 증명 읽기, 받아서 바로 실행, 본문 밖의 지시, 승인 넓히기, 과한 권한 등을 본다.
4. **적합성 판정**: 요청, 스택과 버전, 환경, 프로젝트 규칙과의 충돌, 이미 있는 스킬과의 중복을 본다.
5. **결과**: 스킬마다 짧은 설명, 출처, 보안, 적합성, 권고를 표로 돌려주고, 적합성을 무엇에 비춰 어떤 기준으로 봤는지 적는다.
6. **묻기**: 지금 적용할지, 어디에 둘지 사용자에게 묻는다.
7. **적용**: 승인받으면 검사한 커밋에 고정해 넣는다. 고쳐서 넣는 절차는 `apply.md`에 있다.

## 설치: Claude Code

Claude Code 2.1.275 이상에서는 세션 안에서 한 줄로 설치한다.

```
/plugin install skill-scout --marketplace dlsxody1/skill-scout
```

셸에서는 두 줄이다.

```bash
claude plugin marketplace add dlsxody1/skill-scout
```

```bash
claude plugin install skill-scout@dlsxody1
```

스킬 이름은 `skill-scout:skill-scout`이다. 새 판은 `claude plugin marketplace update dlsxody1` 뒤 `claude plugin update skill-scout@dlsxody1`로 받는다.

## 설치: Codex

`codex/skill-scout` 폴더를 Codex가 스킬을 찾는 곳에 둔다. 나만 쓰려면 `~/.agents/skills`, 저장소에 넣어 팀이 쓰려면 저장소의 `.agents/skills`다.

```bash
git clone https://github.com/dlsxody1/skill-scout.git /tmp/skill-scout && mkdir -p ~/.agents/skills && cp -r /tmp/skill-scout/codex/skill-scout ~/.agents/skills/
```

Codex에서 `$skill-scout`로 부르거나 `/skills`에서 고른다.

## 쓰는 법

"쓸 만한 스킬 찾아줘", "이 스킬 깔아도 돼?", "찾은 스킬 우리 프로젝트에 맞게 고쳐서 넣어줘"처럼 말하거나, skillsmp나 GitHub의 SKILL.md 링크를 준다.

## 필요한 것

- 로그인된 GitHub CLI(`gh`)
- 브라우저를 다루는 도구. skillsmp.com은 일반 요청으로는 열리지 않는다. 없으면 스킬이 검색 결과나 링크를 붙여 달라고 한다.

## 저장소 구조

| 경로 | 내용 |
| --- | --- |
| `.claude-plugin/marketplace.json` | Claude Code 마켓플레이스 목록 |
| `plugins/skill-scout/` | Claude Code 플러그인. 스킬은 `skills/skill-scout/`의 `SKILL.md`와 `apply.md` |
| `codex/skill-scout/` | Codex 스킬. `SKILL.md`, `apply.md`, `agents/openai.yaml` |

두 판의 본문은 따로 관리한다. 한쪽을 고치면 다른 쪽에도 같은 뜻으로 반영한다.

## 한계

읽어서 하는 점검은 보증이 아니다. 별 수는 살 수 있고, 검사한 뒤에 저장소가 바뀔 수 있다. 그래서 설치는 검사한 커밋에 고정하고 사람의 승인을 받는다.
