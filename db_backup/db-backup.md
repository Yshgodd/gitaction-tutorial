# 데이터베이스 컨테이너 백업하기

- 서버 이전할 때 db 볼륨을 백업해서 다시 적용해야 하는 상황
- 각 dbms 마다 백업 파일 만드는 방법이 다르니 참고
- db는 도커 컨테이너이고 docker-compose.yml 로 실행중

## Postgresql

1. __컨테이너 이름 확인__
    ```bash
    docker ps
    ``` 
2. __백업 실행__

    ```bash
    docker exec -t <컨테이너명> pg_dump -U <사용자명> <DB명> > /경로/백업파일.sql
    ```
    
    - <사용자명>: PostgreSQL DB 사용자명

    - <DB명>: 백업할 데이터베이스 이름

    - /경로/백업파일.sql: 호스트에 저장할 백업 파일 경로 (예: /home/cemi/postgres_backup.sql)

        예시:
        ```bash
        docker exec -t ezrnd-notice-pg pg_dump -U postgres ezrnd_notice_db > /home/cemi/postgres_backup.sql
        ```

- 백업완료 후 백업 파일을 다른 저장소에 옮기세요 

__3. 데이터 복구하기 (백업파일 적용하기)__ 

1. 컨테이너 실행 
    - 기존 컴포즈 파일 실행 
        ```bash
        docker compose up -d
        ``` 
2. 백업파일 컨테이너로 복사
    - docker cp <백업파일경로> <컨테이너이름>:/이름.sql
        
        예시:  
        ```bash
        docker cp /home/cemi/postgres_backup.sql <컨테이너이름>:/backup.sql
        ```

3. 복사된 백업파일을 컨테이너 안에 적용
    ```bash
    docker exec -it <컨테이너이름> bash

    $ psql -U <DB유저> -d <DB명> -f /<컨테이너안에복사한파일경로>
    ```        
    예시:
    ```bash
    docker exec -it ezrnd-notice-pg bash

    $ psql -U postgres -d ezrnd_notice_db -f /backup.sql
    ```

4. 백업 됐는지 확인.

    - pgadmin에 연결하여 확인
    - 컨테이너 내부에서 테이블 확인

    