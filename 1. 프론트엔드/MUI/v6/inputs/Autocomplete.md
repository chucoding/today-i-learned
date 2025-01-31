# Autocomplete
자동완성 기능(ex. 태그, 검색창 등)

### 사용 예시
```jsx
import Autocomplete from '@mui/lab/Autocomplete';
import Popper from '@mui/material/Popper';
import TextField from '@mui/material/TextField';

const Component = (value = [], onChange = () => {}) => {
    return (
        <Autocomplete
            multiple
            options={...}
            autoHighlight
            blurOnSelect={false}
            openOnFocus={true}
            selectOnFocus={true}
            getOptionLabel={...}
            value={value}
            onChange={(event, newValue) => {...}}
            renderTags={() => <TagList />}
            renderInput={(params) => <TextField {...params} />}
            freeSolo
            PopperComponent={(props) => (
                <Popper {...props} style={{ ...props.style }}>
                    <div css={autocompleteStyles}>
                        {props.children}
                    </div>
                </Popper>
            )}
        />
    );
}
```

### 주요 옵션
- `multiple`
    - 옵션 다중 선택
- `options`
    - 옵션 목록
    - 이미 선택된 옵션 제외하고 싶은 경우
        ```jsx
        options={tags.filter(tag => !value.some(v => v === tag))}
        ```
- `autoHighlight`
    - 자동 하이라이트 적용
    - 태그 자동 완성 시 첫 번째 옵션 자동 하이라이트 처리시 필요
- `blurOnSelect`
    - 자동 하이라이트 적용 시 자동 선택 방지
    - 태그 자동 완성 시 첫 번째 옵션 자동 하이라이트 처리시 필요
- `openOnFocus`
    - 포커스 시 자동 열기
    - 태그 자동 완성 시 첫 번째 옵션 자동 하이라이트 처리시 필요
- `selectOnFocus`
    - 포커스 시 자동 선택
    - 태그 자동 완성 시 첫 번째 옵션 자동 하이라이트 처리시 필요
- `getOptionLabel`
    - 옵션 목록 표시 문자열
    - 옵션 목록이 id와 name 속성을 가진 객체 배열인 경우
        ```jsx
        getOptionLabel={(option) => option.name}
        ```
- `value`
    - 선택된 옵션 목록(배열 형태의 값만 사용가능)
- `onChange`
    - 선택된 옵션 목록 변경 시 처리 함수
    - validation check 방법
        ```jsx
        onChange={(event, newValue) => {
            const validatedTags = validationCheck(newValue);
            if (validatedTags) {
                onChange(validatedTags);
            }
        }}
        ```
- `renderTags`
    - 인풋창에 선택된 옵션목록 커스텀 디자인 컴포넌트 지정
         ```jsx
        renderTags={() =>
            <TagList
                tags={value}
                handleDelete={(tag) => onChange(value.filter(t => t.name !== tag.name))}
            />
        }
        ```
    - 지정안하면 기본 디자인 적용
       
- `renderInput`
    - 인풋 렌더링 함수
    - 필수 지정해야함.
    - 커스텀 방법
        ```jsx
        renderInput={(params) => (
            <TextField
                {...params}
                css={tagInputStyles(value)}
                variant="outlined"
                placeholder={value.length === 0 ? "태그 등록 (최대 3개)" : ""}
                error={error}
                helperText={error ? errorMessage : ""}
            />
        )}
        ```
- `freeSolo`
    - 자동완성 목록 없는 경우 No options 안나오도록 처리
- `PopperComponent`
    - Autocomplete 옵션 스타일 적용
    - 커스텀 방법
        ```jsx
        PopperComponent={(props) => (
            <Popper {...props} style={{ ...props.style }}>
                <div css={autocompleteStyles}>
                    {props.children}
                </div>
            </Popper>
        )}
        ```

### validation check
1. 중복 체크 (동일한 태그는 한 번만 입력할 수 있습니다.)
2. 개수 체크 (태그는 최대 5개까지만 입력할 수 있습니다.)
3. 길이 체크 (태그는 10자 이내로 입력해주세요.)

### 스타일링
- 인풋창 스타일링
```jsx
const tagInputStyles = (value) => css`
    & .MuiInputBase-root {
        &:hover .MuiOutlinedInput-notchedOutline {
            border-color: #FF7733;
        }
        
        & input::placeholder {
            color: rgba(48, 48, 51, 0.5);
            opacity: 1;
        }
        
        & .MuiOutlinedInput-notchedOutline {
            border-color: #DFE0EB;
            border-radius: 0.6rem;
            font-family: inherit;
        }
    }
    
    & .MuiOutlinedInput-root {
        font-family: var(--bs-body-font-family);
        font-size: 1.6rem;
        color: #303033;
        
        & .MuiAutocomplete-input {
            padding-right: 2.5rem;
            ${value?.length > 0 && `
                padding-left: 1rem !important;
            `}
        }
        
        & input:focus ~ .MuiOutlinedInput-notchedOutline {
            border-color: #FF7733;
            border-width: 1px;
        }
    }
    
    & .MuiAutocomplete-inputRoot {
        padding: 0 1.6rem;
    }
    
    & .MuiFormHelperText-root {
        font-size: 1.2rem;
    }
`;
```

- 자동완성 스타일링
```jsx
const autocompleteStyles = css`
    & .MuiAutocomplete-option {
        color: #303033;
        opacity: 0.8;
        font-size: 1.6rem;
        font-family: var(--bs-body-font-family);
    }
`;
```

