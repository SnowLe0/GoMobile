1. Собираем образ gitlab-runner с правильно проброшенным GID docker
    ```sh
    docker-compose build --build-arg DOCKER_GID=$(getent group docker | cut -d : -f 3)
    ```

1. Собираем и запускаем контейнер gitlab и ожидаем, когда он полностью запустится (около 5-10 мин)

    ```sh
    docker-compose up -d gitlab
    ```

1.  Переходим на страницу https://gitlab.local/ и авторизуемся

    - Пользователь `root` и исходный пароль из файла `/applications/gitlab/config/initial_root_password`

1. В Gitlab создаём проект и переходим в `Settings` > `CI/CD` > `Runners`

1. Копируем `Token` и `URL` для регистрации Gitlab-Runners

1. Выполняем регистрацию Gitlab-Runner подставляя значения `URL` и `Token`
    
    ```sh
    docker-compose run --rm gitlab-runner register \
        -n \
        -u <URL> \
        -r <TOKEN> \
        --executor shell \
        --tag-list "docker" \
        --name "Docker-Runner"
    ```

1. Перезапускаем gitlab-runner

    ```sh
    docker-compose up -d gitlab-runner
    ```

1. Переходим в папку с приложением и инициализируем git

    ```sh
    cd ./app && \
    git init
    ```

1. Конфигурируем git и добавляем удаленный репозиторий

    ```sh
    git config user.name "Administrator"
    git config user.email "admin@example.com"
    git config http.sslVerify false && \
    git remote add origin https://gitlab.local/root/ci.git
    ```

1. Индексирум и отправляем в удаленный репозиторий файлы приложения

    ```sh
    git add . && \
    git commit -m "INIT" && \
    git push -u origin master
    ```
1. Ждем выполнения `pipeline`

1. Контейнер приложения будет доступен на хостовой машине. Чтобы выполнить наше приложение, запустим собранный контейнер.

    ```sh
    docker start -a helloapp
    ```