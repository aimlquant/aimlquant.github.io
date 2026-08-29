# aimlquant.github.io 작업 안내

조직 루트 `https://aimlquant.github.io/` 를 서빙하는 랜딩 저장소다. 빌더가 없다.
`index.html` 과 `operations/` 아래 정적 HTML 을 직접 편집한다.

## 바꾸면 안 되는 것

**저장소 이름.** GitHub 는 `<org>.github.io` 이름의 저장소만 조직 루트에 서빙한다.

**루트에 하위 경로 이름과 겹치는 디렉터리를 만들지 않는다.** 여기에 `study/` 를 두면
`aimlquant/study` 프로젝트 사이트와 충돌한다. 새 디렉터리를 만들기 전에 조직 저장소
목록(`gh repo list aimlquant`)과 대조한다.

**`guides/` 아래 리다이렉트 stub 3개.** 2026-08-29 에 `/guides/` 를 `/operations/` 로
옮기면서 남긴 것이다. 지우면 기존 북마크가 404 가 된다.

## 공간 이름의 정본

`index.html` 의 `.spaces` 카드 제목이 각 공간 이름의 정본이다. 각 공간 페이지의
`<title>` 은 `<공간 이름> · AI·ML·Quant` 형식을 따른다.

현재 순서와 이름: 스터디 · 강의 · 세미나 · 주간 AI 브리핑 · 운영.

공간 이름을 바꾸면 그 공간 페이지의 `<title>`, `h1`, `.brand-sub` 를 함께 바꾼다.

## 최근 공개 영상 — 회차마다 갱신하는 유일한 블록

`index.html` 의 `.recordings` 안에 게시 단위마다 카드 하나씩 둔다. 새 회차를 공개하면
**이 블록의 카드를 새 회차 것으로 교체**하고 위의 `.latest-intro` 문장도 날짜에 맞춘다.

카드 구조를 바꾸지 말고 값만 갈아끼운다. 텍스트 링크(`<a class="recording">`)로
되돌리지 않는다 — 썸네일과 재생이 사라진다.

```html
<article class="recording">
  <button class="thumb" type="button" data-vid="VIDEO_ID" aria-label="제목 재생">
    <img src="https://i.ytimg.com/vi/VIDEO_ID/mqdefault.jpg" alt="" width="320" height="180" loading="lazy">
    <span class="play" aria-hidden="true"><svg viewBox="0 0 10 12" width="10" height="12" fill="#fff"><path d="M0 0 10 6 0 12Z"/></svg></span>
  </button>
  <div class="recording-body">
    <span>대분류 · 교재 CH.NN</span>
    <strong>회차 제목</strong>
    <a class="more" href="/study/sessions/SESSION_ID/">회차 자료 보기 →</a>
  </div>
</article>
```

- `data-vid` 와 썸네일 URL 의 `VIDEO_ID` 는 **같은 값이어야 한다.** 공개된 YouTube
  video ID 이며 정본은 `media` 저장소의 `channel/inventories/youtube-owner.tsv` 다.
  비공개·일부공개 ID 는 여기에 넣지 않는다
- 썸네일은 `mqdefault.jpg`(320×180) 를 쓴다. `hqdefault.jpg` 는 4:3 이라 위아래 검은
  띠가 생긴다
- `.more` 링크는 회차 페이지로 보낸다. 회차가 없는 운영 영상은 `/operations/` 로 보내고
  문구를 `운영 페이지 보기 →` 로 한다
- 카드를 늘리거나 줄여도 된다. `.recordings` 는 `auto-fit minmax(220px, 1fr)` 이라
  개수에 맞춰 접힌다

**재생은 모달이다.** 썸네일을 누르면 페이지 하단 `<dialog id="player">` 에
`youtube-nocookie.com` iframe 을 만들어 크게 띄운다. 재생 전에는 YouTube 로 요청이
가지 않는다. `#player`, `.player-frame`, `.player-close` 와 그 스크립트는 카드 개수와
무관하니 건드리지 않는다.

`allowFullscreen` 과 `allow` 의 `fullscreen` 은 **둘 다** 있어야 전체화면 버튼이 뜬다.

## 운영 페이지

`operations/index.html` 의 `.records-list` 는 운영 논의 영상 목록이다. 새 운영 논의를
공개하면 여기에 한 줄 추가한다. 재생목록 `PLTmMrGXB8sWE` 가 정본이다.

`.recordings` 와 달리 이 목록은 **교체가 아니라 누적** 이다. 지난 회차를 지우지 않는다.

## 검증

빌더도 테스트도 없으니 실제 렌더를 눈으로 본다. 소스만 읽고 판단하지 않는다.

```bash
python3 -m http.server 8765 &
safe-browser-shot.sh --url http://localhost:8765/ --output /tmp/root.png --width 1280 --height 1500
```

`aimlquant-safe-browser-shot` 은 클릭을 지원하지 않는다. 재생 동작을 확인하려면 사본에
자동 클릭 스크립트를 넣어 `dialog.open` 과 iframe 생성을 확인한다.

확인할 것:

- 재생 아이콘이 상자 안에 중앙 정렬돼 있는가. `display:grid; place-items:center` 는
  `<button>` 안에서 어긋난다 — 절대좌표 + `translate(-50%,-50%)` 를 쓴다
- 재생 아이콘이 썸네일 자체의 제목 글자를 덮지 않는가. 이 채널 썸네일은 제목이 좌상단,
  챕터 배지가 우하단이라 아이콘은 좌하단에 둔다
- 카드 텍스트가 어절 중간에서 끊기지 않는가 (`word-break: keep-all`)
- 배포 후 공개 URL 로 HTTP 200 과 내용을 다시 확인한다. Pages run 은
  `gh run list --limit 1` 로 본다

## 배포

`main` 에 push 하면 Pages 가 배포한다. push 는 사용자 확인 뒤에 한다.

`HANDOFF.md` 는 `.gitignore` 대상이다. 로컬 인수인계용이며 공개 배포하지 않는다.
경로·이름을 바꾸는 것처럼 되돌리기 어려운 변경은 그 이유와 함께 여기에 남긴다.
