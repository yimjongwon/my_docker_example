### docker hub 사용하기

```bash

# 이미지를 빌드하고 docker hub에 올리기
docker build -t <도커허브아이디>/<이미지이름>:<태그> .
docker build -t  yimjongwon/hub-app:1.0 .

# push
# 이미 로그인 되어 있는 상태라면 그냥 올라가고, 로그인 안되어 있으면 로그인을 해야 한다.
docker push yimjongwon/hub-app:1.0

# 이미지를 docker hub 로부터 pull (다운로드)
docker pull junhanshin/hub-app:1.0

# tag 변경하기
docker tag junhanshin/hub-app:1.0 yimjongwon/hub-app:1.0

# shell 에서 docker hub 이미지 삭제하기
# docker hub access key를 준비한다 

# 1. 비밀번호 대신 Access Token(PAT)을 넣어 JWT 임시 통행증 발급
TOKEN=$(curl -s -H "Content-Type: application/json" -X POST -d '{"username": "yimjongwon", "password": "dckr_pat_YOUR_REAL_TOKEN"}' https://hub.docker.com/v2/users/login/ | jq -r .token)

# 2. 통행증(TOKEN)을 이용해 이미지 강제 삭제 슛!
curl -i -X DELETE -H "Authorization: JWT $TOKEN" https://hub.docker.com/v2/repositories/yimjongwon/hub-app/tags/1.0/


```


<!-- 
<img src="./assets/image.png">
![설명](./assets/image.png) -->