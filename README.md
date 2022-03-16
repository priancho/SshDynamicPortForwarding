# Purpose
TensorBoard나 Jupyter notebook은 매우 편리한 도구이나, 적절한 세큐리티
(SSL, Authentication, Authorization 등) 방책이 없이는 사용하기 곤란한
경우가 많다.

여기서는 SSH dynamic port forwarding을 통해서 local computer
(노트북, 데스크탑 등)으로부터 remote server (작업용 서버, 주로 회사 컴퓨터)
에서 동작중인 웹기반 어플리케이션에 액세스하는 방법을 설명한다.
(물론 remote server의 port에 접근하는 것이므로 웹 기반 앱이 아니어도 됨)

# Setup Dynamic Port Forwarding
## Setting from Linux (.ssh/config)
우선 아래와 같이 local computer의 ssh config 파일에 접속 서버 정보를 추가한다.
이때, `DynamicForward 8080`이라는 라인이 추가한다 (포트 번호는 8080이 아니어도 됨).

```bash
vi ~/.ssh/config

Host RemoteServer
  Hostname remote.server.com
  Port 22
  User alice
  DynamicForward 8080
```

이제 `ssh RemoteServer`를 실행하여 해당 서버에 접속하게 되면, local computer의
8080 포트로 보내지는 모든 요청은 ssh 커넥션을 통해 (안전하게) remote server로
포워딩된다.
  
## Setting from Windows (Putty)
[SSH dynamic port forwarding with Putty](https://www.linuxbabe.com/firewall/ssh-dynamic-port-forwarding)의
가장 아래쪽에 있는 Putty 설정 설명을 참고한다.
이 설정을 한 세션을 통해서 remote server에 접속할 경우, 위에서 설명한바와 같이
local computer의 특정 포트로 보내지는 모든 요청은 remote server로 포워딩
되게 된다.


# Accessing a remote server securely
## Chrome and FoxyProxy extension
이제 local computer의 특정 포트 (예, 8080)을 통해 dynamic port forwarding
설정을 했으므로 이를 이용하는 방법을 설명한다.

우선 여기서는 Chrome extension인 FoxyProxy를 통해 특정 URL pattern에 액세스
할 경우에만 remote server로 요청을 포워딩하도록 설정한다.

- 아래 Chrome webstore에 방문하여 FoxyProxy Standard 익스텐션을 설치한다.
  - https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp
- 설정창을 열어 "Add New Proxy" 버튼을 클릭한다
- Proxy Detail 탭을 누르고 Manual proxy configuration을 선택한다
  - IP address는 `localhost`를 입력
  - Port는 `8080`을 입력
  - 밑에 "SOCKS Proxy?" 버튼을 클릭하고 "SOCKS v5"를 고른다
- URL Patterns 탭을 누르고 Add new pattern 버튼을 누른다
  - 패턴 이름과 패턴을 입력한다.
    - 패턴이름 예: `RemoteServerPattern`
    - 패턴 예: `http://remote.server.com*`
- Save 버튼을 클릭한다

이렇게 설정을 하고 Chrome 브라우저의 우상단에 나온 FoxyProxy 익스텐션 버튼을 클릭하고
적절한 옵션 (pattern에 기반하여 proxy를 사용하는 옵션 등)을 선택한다.

이렇게하면 Chrome 브라우저에서 `http://remote.server.com...` 주소에 접속할 경우,
local computer에서 직접 요청을 하는 것이 아니라 remote server로 요청을 보내서
해당 서버내에서 요청을 한 것 처럼 작업을 수행한다.

접속은 SSH로 암호화되기 때문에 안전하다.



## More?
- TODO: 다른 사용방법 추가
