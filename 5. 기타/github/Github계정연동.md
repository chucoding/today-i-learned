# GitHub 계정연동
Github 계정 연동 방법 정리

## MAC 에서 레포별 계정 분리

### 1. SSH config

`~/.ssh/config` 에 아래 내용 삽입   
(없으면 만들어서 사용. `mkdir -p ~/.ssh`)

```ssh-config
# 기본 GitHub 계정 - 일반 레포
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519

# 두 번째 계정용 (예: chucoding)
Host github-chucoding
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_chucoding
```

| 항목 | 변경 여부 | 설명 |
|------|-----------|------|
| **HostName** | 변경 안 함 | GitHub 서버 주소가 `github.com`으로 고정. |
| **User** | 변경 안 함 | GitHub SSH는 모두 `git` 사용자로 접속. (GitHub 아이디 아님) |
| **Host** | 바꿔도 됨 | 내 컴퓨터에서만 쓰는 **별칭**. 두 번째 계정용은 `github-work`, `github-personal` 등 자유롭게 지어도 됨. 바꾸면 4번에서 쓰는 원격 URL의 호스트 이름도 그에 맞춰야 함. |

> 💡 ed25519 : SSH 키 파일 이라는 뜻(Elliptic Curve 중 하나로 안전하고 속도도 좋아서 많이 씀.)

### 2. Git 전역 설정 (includeIf)

**대상 파일**: `~/.gitconfig`  
(홈 디렉터리 `.gitconfig`. 보통 이미 있음.)

**하는 일**: 맨 아래에 아래 블록을 **추가**. `gitdir` 경로를 "두 번째 계정 쓸 레포가 있는 폴더"로 맞춘다.

```ini
[includeIf "gitdir:~/IdeaProjects/today-i-learned/"]
	path = ~/.gitconfig-chucoding
```

### 3. 계정별 Git 설정 파일

`~/.gitconfig-chucoding`  
(없으면 홈에 새로 만든다.)

두 번째 계정의 커밋용 name/email. 아래 내용으로 저장.

```ini
[user]
	name = chucoding
	email = chucoding@gmail.com
```

### 4. 레포 원격 URL 변경

두 번째 계정으로 쓸 **그 레포 디렉터리**에서만 아래 한 번 실행. `github-chucoding`과 `chucoding/레포이름`은 본인 계정·레포에 맞게 수정.

```bash
git remote set-url origin git@github-chucoding:chucoding/today-i-learned.git
```

### 5. SSH 키 생성 및 GitHub 등록

터미널에서 아래 순서대로 실행. 두 번째 계정용 키 파일 이름은 1번에서 쓴 `IdentityFile`과 맞춘다.

**5.1** 두 번째 계정용 키 생성:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_chucoding -C "chucoding@gmail.com" -N ""
```

**5.2** 공개키 확인 후 복사:

```bash
cat ~/.ssh/id_ed25519_chucoding.pub
```

**5.3** GitHub **두 번째 계정** 로그인 → **Settings → SSH and GPG keys → New SSH key** → Key type: **Authentication Key** → 위에서 복사한 내용 붙여넣기.

**5.4** 연결 테스트:

```bash
ssh -T git@github-chucoding
# 성공 시: "Hi chucoding! You've successfully authenticated..."
```

### 6. 다른 레포도 같은 계정으로 쓰기

- **원격만** 바꿀 때: 그 레포 폴더에서 `git remote set-url origin git@github-chucoding:chucoding/레포이름.git` 실행.
- **커밋 identity**도 두 번째 계정으로 쓰려면: `~/.gitconfig`를 연 뒤, **includeIf** 블록을 하나 더 추가하고 `gitdir` 경로를 그 레포가 있는 폴더로 지정.

```ini
[includeIf "gitdir:~/IdeaProjects/다른-레포/"]
	path = ~/.gitconfig-chucoding
```

## 윈도우에서 레포별 계정 분리

**보통은 SSH 설정 없이 아래만 하면 된다.**  
윈도우는 Git Credential Manager(GCM)가 **레포(URL)마다 로그인 정보를 따로 저장**해서, HTTPS로 push할 때 해당 레포에 맞는 계정을 쓰거나 로그인 창을 띄워 준다. 그래서 **커밋용 이름/이메일만 레포별로 바꿔 주면** 된다.

### 방법 1: git config만 사용 (권장)

두 번째 계정으로 쓸 **레포 폴더**에서만 아래 실행. (한 번만 하면 그 레포에 저장됨.)

```bash
git config user.name "chucoding"
git config user.email "chucoding@gmail.com"
```

- **원격(remote)** 은 HTTPS 그대로 사용: `https://github.com/chucoding/today-i-learned.git`
- **첫 push** 할 때 로그인 창이 뜨면 **두 번째 계정**으로 로그인. 이후엔 GCM이 그 레포용으로 저장해 둠.
- 다른 레포는 그대로 쓰면 기본 계정으로 동작.

**여러 레포를 같은 계정으로 쓰려면**  
각 레포 폴더에서 위 `git config user.name` / `user.email` 한 번씩 실행하거나, 전역 `~/.gitconfig`에 **includeIf**로 경로 묶어서 써도 된다. (아래 "방법 2"의 2·3번만 적용.)
