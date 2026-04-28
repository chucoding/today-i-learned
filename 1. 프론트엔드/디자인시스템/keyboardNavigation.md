# 키보드 네비게이션

## Radio Group
> [ARIA radiogroup/radio 패턴](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/input/radio)

| 키 | 표준 동작 |
| --- | --- |
| `Tab` / `Shift + Tab`      | radio group 안팎으로 포커스 이동. 그룹에 선택된 radio가 있으면 선택된 항목으로 포커스. 선택된 항목이 없으면 첫 번째 radio로 포커스. |
| `Space`                    | 현재 포커스된 radio를 선택. 이미 선택되어 있으면 변화 없음.                                                  |
| `ArrowRight` / `ArrowDown` | 다음 radio로 이동하고, 그 radio를 선택. 마지막이면 첫 번째로 wrap.                                         |
| `ArrowLeft` / `ArrowUp`    | 이전 radio로 이동하고, 그 radio를 선택. 첫 번째면 마지막으로 wrap.                                         |
