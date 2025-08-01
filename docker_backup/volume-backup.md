# 컨테이너 볼륨 백업 - 리눅스 to 리눅스 (우분투 동일)

1. 컨테이너 및 이미지 확인
현재 동작 중인 컨테이너와 사용 중인 이미지를 확인합니다.

    ```bash
    docker ps -a
    ```

2. docker-compose.yml, .env 등 설정 파일 백업

    - 서비스 정의(docker-compose.yml), 환경 변수 파일(.env), 기타 설정 파일을 안전한 곳에 복사합니다.

3. 데이터 볼륨 백업
    - 볼륨 이름과 실제 경로를 확인합니다.

    ```bash
    docker volume ls
    docker volume inspect <볼륨명>

    >>> 
    [
        {
            "CreatedAt": "2025-02-26T17:25:39+09:00",
            "Driver": "local",
            "Labels": {
                "com.docker.compose.config-hash": "39c8a6eb49df4913058a75986454ea536ea764d4bcd8077440f2da9be52306b3",
                "com.docker.compose.project": "ezrnd-api",
                "com.docker.compose.version": "2.32.4",
                "com.docker.compose.volume": "shared-static"
            },
            "Mountpoint": "/var/lib/docker/volumes/ezrnd-api_shared-static/_data", # 볼륨경로
            "Name": "ezrnd-api_shared-static", # 볼륨명
            "Options": null,
            "Scope": "local"
        }
    ]
    ````

    볼륨 데이터를 tar로 압축 백업:

    ```bash
    sudo tar cvf backup_<볼륨명>.tar -C /var/lib/docker/volumes/<볼륨명>/_data .
    ```
    
    또는 임시 컨테이너를 활용:

    ```bash
    docker run --rm -v <볼륨명>:/volume -v $(pwd):/backup ubuntu tar cvf /backup/backup_<볼륨명>.tar /volume
    ```


4. 백업 파일 외부 저장
    - 위에서 백업한 모든 파일(설정, 볼륨, DB, 이미지 등)을 외장하드, NAS, 클라우드 등 안전한 외부 저장소에 복사합니다.

## 포맷 후
5. 설정 파일 복원
    - docker-compose.yml, .env 등 설정 파일을 원래 위치에 복사합니다.

6. 데이터 볼륨 복원
    
    1) 볼륨을 먼저 만들기
        - __볼륨을 미리 생성__ 한 후, tar 파일을 해당 경로에 풀어줍니다.
        - __볼륨 이름은 폴더이름도 포함해__ ex)<폴더명>_<볼륨> =>  ezrnd-api_shared-static 
        ```bash
        docker volume create <볼륨명>
        sudo tar xvf backup_<볼륨명>.tar -C /var/lib/docker/volumes/<볼륨명>/_data
        ```
        ```bash
        docker compose up -d
        ```
    2) compose up 먼저하고 풀기
        ```bash
        docker compose up -d
        sudo tar xvf backup_<볼륨명>.tar -C /var/lib/docker/volumes/<볼륨명>/_data  
        ```




