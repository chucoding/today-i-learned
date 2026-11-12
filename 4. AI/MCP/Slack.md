# Slack

Slack 커넥터의 DM과 이미지 읽기

## 목차
- [Slack 커넥터](#slack-커넥터)
- [DM 읽기](#dm-읽기)
- [DM 속 이미지 읽기](#dm-속-이미지-읽기)
- [참고자료](#참고자료)

## Slack 커넥터

> 💡 커넥터란?
> claude.ai에 등록해 쓰는 원격 MCP 서버. Claude Code는 이를 MCP 서버 출처 중 하나로 불러와 `/mcp` 목록에 `claude.ai Slack`처럼 표시함.

- Slack 공식 MCP 서버(`https://mcp.slack.com/mcp`)를 Claude 커넥터로 연결한 것.
- 로그인한 사용자의 user token 권한으로 동작. 본인이 참여한 대화만 읽음.
- 워크스페이스 관리자가 커넥터 승인 시 뺀 scope의 기능은 쓸 수 없음.

## DM 읽기

1:1 DM과 그룹 DM 모두 읽기 가능.

| 용도 | 도구 | DM용 scope |
|------|------|------|
| 대화와 스레드 읽기 | `slack_read_channel`, `slack_read_thread` | `im:history`, `mpim:history` |
| 메시지 검색 | `slack_search_public_and_private` | `search:read.im`, `search:read.mpim` |
| 참여 중인 대화 목록 | `slack_list_user_channels` | `im:read`, `mpim:read` |

- `slack_search_public`은 공개 채널만 검색하므로 DM은 나오지 않음.

## DM 속 이미지 읽기

- 파일 읽기 도구 `slack_read_file`(scope `files:read`)로 읽음.
  - 공유된 파일의 메타데이터와 내용을 가져오는 도구. 이미지도 Claude가 보는 이미지로 돌려줌.
- 메시지의 이미지 URL을 WebFetch로 여는 방식은 불가.
  - Slack에 올린 파일은 `Authorization: Bearer` 헤더에 토큰을 실어야 내려받을 수 있기 때문.

## 참고자료

| TITLE | URL |
|------|------|
| Slack MCP 서버 주소와 도구별 scope | [https://docs.slack.dev/ai/slack-mcp-server](https://docs.slack.dev/ai/slack-mcp-server) |
| 파일 읽기 도구 추가 | [https://docs.slack.dev/changelog/2026/05/13/new-mcp-tools](https://docs.slack.dev/changelog/2026/05/13/new-mcp-tools) |
| Slack 파일 다운로드의 토큰 요구 | [https://docs.slack.dev/messaging/working-with-files](https://docs.slack.dev/messaging/working-with-files) |
| Claude Slack 커넥터 | [https://claude.com/marketplace/connectors/slack](https://claude.com/marketplace/connectors/slack) |
| Claude Code에서 쓰는 claude.ai 커넥터 | [https://code.claude.com/docs/en/mcp](https://code.claude.com/docs/en/mcp) |
