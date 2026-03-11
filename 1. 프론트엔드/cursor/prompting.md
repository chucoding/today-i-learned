# Prompting agents

> CURSOR 공식 문서 : [CURSOR](https://cursor.com/docs/agent/prompting)

## Context Engineering

### Chat
- 범위 : 현재 Ctrl+L 채팅창의 같은 탭/같은 세션
- 한계 : 대화가 아주 길어지는 경우 모델 허용 범위에 따라 달라짐.

> 💡 다른 채팅 탭 포함되는지?  
> Cursor 2.0에서 @Past Chats 같은 기능이 제거되었고, 공식 포럼에서도 **“Agent can no longer directly use past chats as context”**라고 안내했습니다. 즉, 다른 채팅을 새 채팅이 자동 참조하는 구조가 아닙니다. [참고자료](https://forum.cursor.com/t/what-happened-to-past-chats/143666)

### Code File
- 범위 : 명시적으로 붙인 파일/코드/코드베이스 중심으로 동작.

> 💡 열려 있는 여러 에디터 탭(파일 탭) 포함되는지?
> - 기본 자동 호함 아님.
> - / Reference open editors 같은 방식으로 명시적으로 추가 가능
> - [참고자료](https://forum.cursor.com/t/add-open-files-to-chat-cmd-l/7082)