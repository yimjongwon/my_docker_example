### postgres db 컨테이너 만들기

```bash

# 컨테이너가 실행시에 주입되는 환경변수를 -e 옵션으로 주입해 줄 수 있다.
# postgres:15 이미지를 만든 사람이 설정한대로 환경변수를 주입해주면 알아서 동작한다
# 컨테이너 실행시에 정해진 환경변수의 값이 존재한다면 그 값에 따라서 동작이 준비된다.
docker run -d \
    -p 5432:5432 \
    -e POSTGRES_USER=scott \
    -e POSTGRES_PASSWORD=tiger \
    -e POSTGRES_DB=scott_db \
    --name my-postgres \
    postgres:15

# 실행중인 container에 -it (인터렉티브하게) 접속해서 bash로 들어가기
docker exec -it my-postgres /bin/bash

# container 안에서 설정된 환경변수 확인해 보기
echo $POSTGRES_USER
echo $POSTGRES_PASSWROD
echo $POSTGRES_DB

# DB 접속하기 컨테이너 내부에서 접속할때는 비밀번호를 물어 보지 않는다 (이미 충분한 권한이 있다고 가정됨)
psql -U scott -d scott_db

# table 생성
CREATE TABLE member( num SERIAL PRIMARY KEY, name VARCHAR(20), addr TEXT);

# insert
INSERT INTO member (name,addr) VALUES( 'KIM', 'seoul');

# container 정지
docker container stop my-postgres

# container 다시 시작
docker container start my-postgres

# container 를 삭제후에 다시 run 해본다.
docker container rm -f my-postgres

# 재실행 후에 dbeaver로 접속을 해보면 table이 없는것을 알수가 있다

# 외부 volume 의 필요성이 느껴진다

# 외부 volume 만들기
docker volume create pgdata
# 만들어진 volume 목록 확인하기
docker volume ls
# 만들어진 volume 를 사용하는 container를 다시 run 하기
docker run -d \
    -p 5432:5432 \
    -e POSTGRES_USER=scott \
    -e POSTGRES_PASSWORD=tiger \
    -e POSTGRES_DB=scott_db \
    -v pgdata:/var/lib/postgresql/data \
    --name my-postgres \
    postgres:15

# 1. volume 를 사용하는 컨테이너에 -it하게 접속해서 scott 계정으로 postgres db에 들어간다음
# 2. member table를 만들고 sample 데이터를 insert 하고
# 3. 컨테이너를 빠져 나와서 컨테이너를 삭제후에
# 4. 다시 동일한 volume을 사용하는 컨테이너를 실행한다음
# 5. postgres db 에 접속해서 sample 데이터가 유지되는지 확인

# db container에 다른 container가 접근해서 data를 insert, update, delete, select 하기 위해서는 network 설정이 필요하다

# 컨테이너를 삭제하고 다시 run 하기
docker container rm -f my-postgres

# network를 추가로 구성하기
docker network create my-net

# network 목록 검색하기
docker network ls

# network 정보 자세히 검색하기
docker network inspect my-net

# 컨테이너 실행(--network)
docker run -d \
    -p 5432:5432 \
    -e POSTGRES_USER=scott \
    -e POSTGRES_PASSWORD=tiger \
    -e POSTGRES_DB=scott_db \
    -v pgdata:/var/lib/postgresql/data \
    --network my-net \
    --name my-postgres \
    postgres:15

# 실행된 container의 자세한 정보 확인해보기
docker network inspect my-postgres

# ip 주소만 따로 출력
docker network inspect my-postgres | grep IPAddress
# ip 주소가 my-net network 대역의 ip 주소인것을 알수가 있다.
```