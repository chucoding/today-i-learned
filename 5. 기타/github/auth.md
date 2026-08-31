# 개요

Github 계정관리 방법 정리

# 연동 방식 선택(HTTPS vs SSH)

- 순수 통신 암호화 수준은 **SSH와 HTTPS 모두 안전**
- SSH방식은 프로젝트나 폴더 단위로 GitHub 계정 두 개를 **고정적으로 분리**
- 계정 선택 실수를 최소화할 수 있음.

# **MAC 에서 레포별 계정 분리**

### **1. SSH config**

`~/.ssh/config` 에 아래 내용 삽입 (없으면 만들어서 사용. `mkdir -p ~/.ssh`)

```
# 기본 GitHub 계정 (hyeonseok-suh) - 일반 레포
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519
  IdentitiesOnly yes

# chucoding 계정용 - today-i-learned 등
Host github-chucoding
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_chucoding
  IdentitiesOnly yes

# GIST 용 별도
Host gist-chucoding
  HostName gist.github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_chucoding
  IdentitiesOnly yes

```

> ⚠️ `**IdentitiesOnly yes`는 모든 블록에 빠짐없이 넣는다.** 한 블록만 빠져도 그 호스트에서 다른 계정으로 붙는다. 이유는 [아래](#identitiesonly)에 정리.


| 항목               | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **HostName**     | GitHub 서버 주소가 [`github.com`](http://github.com)으로 고정.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **User**         | GitHub SSH는 모두 `git` 사용자로 접속. (GitHub 아이디 아님)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **Host**         | 내 컴퓨터에서만 쓰는 **별칭**. (두 번째 계정용은 `github-work`, `github-personal` 등 자유롭게 지어도 됨. 바꾸면 4번에서 쓰는 원격 URL의 호스트 이름도 그에 맞춰야 함.)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `IdentitiesOnly` | 잘못된 키가 먼저 선택되는 문제를 `IdentitiesOnly yes`로 방지 (push 권한은 SSH 인증에 성공한 키의 주인 계정에 있기 때문에 push 오류를 사전에 방지) *`IdentitiesOnly`가 없으면 ssh는 `IdentityFile`**에 지정한 키보다 ssh-agent에 올라온 키를 먼저 내민다.** GitHub은 **먼저 통과된 키의 주인**으로 계정을 확정하므로, agent에 회사 키가 들어 있으면 지정한 개인 키는 차례가 오지도 않는다. - `AddKeysToAgent yes` 옵션 (ssh 접속할 때마다 그 키를 agent에 자동 등록) agent에 키가 들어가는 경로는 여러 가지다. 하나만 걸려도 재발한다.- `AddKeysToAgent yes` 옵션 (ssh 접속할 때마다 그 키를 agent에 자동 등록)- 손으로 실행한 `ssh-add`- VS Code, 1Password, JetBrains 등의 자체 SSH agent- 패스프레이즈를 키체인에 저장한 키의 로그인 시 자동 로드 그래서 **"agent를 비우는" 방식이 아니라 "agent와 무관하게 지정한 키만 쓰는" 방식**이 맞다. 그게 `IdentitiesOnly yes`다. |


> 💡 ed25519 : SSH 키 파일 이라는 뜻(Elliptic Curve 중 하나로 안전하고 속도도 좋아서 많이 씀.)



### **2. Git 전역 설정 (includeIf) -** 다른 계정으로 COMMIT 하기

`~/.gitconfig` 에 다음과 같이 작성

```
[includeIf "gitdir:~/IdeaProjects/<REPO>/"]
	path = ~/.gitconfig-chucoding

```

레포 추가되는 경우 **includeIf** 블록을 하나 더 추가하고 `gitdir` 경로를 그 레포가 있는 폴더로 지정.

### **3. 계정별 Git 설정 파일**

`~/.gitconfig-chucoding`생성 후 다음과 같이 작성

```
[user]
	name = chucoding
	email = chucoding@gmail.com

```

## **4. 레포 원격 URL 변경**

### 레포지토리 URL 설정 방법

SSH 설정 파일에 host명인 `github-chucoding` 으로 원격 연결

```bash
git remote set-url origin git@github-chucoding:chucoding/레포이름.git

```

### GIST URL 설정 방법

SSH 설정 파일에 host명인 `gist-chucoding` 으로 원격 연결

```bash
git remote set-url origin git@gist-chucoding:<GIST>.git
# git remote set-url origin git@gist-chucoding:a548473d5c3a2a954e1a633adfcd636b.git# git remote set-url origin git@gist.github.com:a548473d5c3a2a954e1a633adfcd636b.git

```

## **5. SSH 키 생성 및 GitHub 등록**

터미널에서 아래 순서대로 실행. 두 번째 계정용 키 파일 이름은 1번에서 쓴 `IdentityFile`과 맞춘다.

**5.1** 두 번째 계정용 키 생성:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_chucoding -C "chucoding@gmail.com" -N ""
```

**5.2** 공개키 확인 후 복사:

```bash
cat ~/.ssh/id_ed25519_chucoding.pub
```

**5.3** GitHub **두 번째 계정** 로그인 → **Settings → SSH and GPG keys → New SSH key** → Key type: **Authentication Key** → 위에서 복사한 내용 붙여넣기.

**5.4** 연결 테스트:

```bash
ssh -T git@github-chucoding
# 성공 시: "Hi chucoding! You've successfully authenticated..."
```

## **6. 다른 레포도 같은 계정으로 쓰기**

2, 4번 진행

# **윈도우에서 레포별 계정 분리**

**보통은 SSH 설정 없이 아래만 하면 된다.**

윈도우는 Git Credential Manager(GCM)가 **레포(URL)마다 로그인 정보를 따로 저장**해서, HTTPS로 push할 때 해당 레포에 맞는 계정을 쓰거나 로그인 창을 띄워 준다. 그래서 **커밋용 이름/이메일만 레포별로 바꿔 주면** 된다.

### **방법 1: git config만 사용 (권장)**

두 번째 계정으로 쓸 **레포 폴더**에서만 아래 실행. (한 번만 하면 그 레포에 저장됨.)

```bash
git config user.name "chucoding"
git config user.email "chucoding@gmail.com"

```

- **원격(remote)** 은 HTTPS 그대로 사용: [`https://github.com/chucoding/today-i-learned.git`](https://github.com/chucoding/today-i-learned.git)
- **첫 push** 할 때 로그인 창이 뜨면 **두 번째 계정**으로 로그인. 이후엔 GCM이 그 레포용으로 저장해 둠.
- 다른 레포는 그대로 쓰면 기본 계정으로 동작.

**여러 레포를 같은 계정으로 쓰려면**

각 레포 폴더에서 위 `git config [user.name](http://user.name)` / [`user.email`](http://user.email) 한 번씩 실행하거나, 전역 `~/.gitconfig`에 **includeIf**로 경로 묶어서 써도 된다. (아래 "방법 2"의 2·3번만 적용.)