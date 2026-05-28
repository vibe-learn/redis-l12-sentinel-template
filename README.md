        # redis — Sentinel — авто-failover

        Homework-шаблон для урока **l2_sentinel** (Sentinel — авто-failover) на платформе Vibe Learn.

        ## Что делать

        docker-compose: 3 Sentinel + master + 2 replicas. Go-клиент через github.com/redis/go-redis/v9
с FailoverOptions. Скрипт: kill -9 master, измеряет время до автоматического переключения
клиента на новый мастер (должно быть ≤30s). Тесты проверят корректную работу клиента
во время и после failover'a.

## Контекст (из transfer-задачи урока)

Команда хочет «упростить deploy» — поставить 2 Sentinel'a вместо 3, чтобы сэкономить ноду.
Quorum выставлен в 1. Объясни, почему это плохая идея — какие конкретные сценарии приведут
к катастрофе с этим конфигом.

## Recap из урока

- Sentinel — отдельный процесс, наблюдает за мастером и репликами. На failover автоматически назначает нового мастера.
- **Минимум 3 Sentinel** на проде. Quorum обычно `floor(N/2)+1` (для N=3 → quorum=2). Иначе split-brain при разделении.
- Failover: PING каждую секунду → SDOWN (один считает) → ODOWN (quorum согласен) → выбор leader-Sentinel → новый мастер.
- Async-репликация → возможна потеря последних команд при failover. Sentinel не убирает эту гонку.
- Sentinel = single-master HA. Для шардинга нужен Cluster (следующий урок).

        ## Как работать

        1. Платформа Vibe Learn создаёт копию этого репо в твоём GitHub-аккаунте по клику «Начать домашку» на странице урока (через GitHub `/generate`, codecrafters-pattern).
        2. Склонируй копию локально, реализуй TODO в `main.go`, прогони тесты, запушь.
        3. CI (`.github/workflows/ci.yml`) запускает `go vet` + `go test ./...` на каждый push. Платформа слушает результат через webhook от GitHub Actions и обновляет статус домашки на странице урока.

        ## Локальное окружение

        - Go 1.22+
        - Docker + docker-compose — `docker compose up -d` поднимает single-node Redis 7 на `localhost:6379` (с включёнными keyspace-notifications и AOF). Адрес переопределяется через env `REDIS_ADDR`.

        ## Запуск

        ```bash
        # Поднять локальный Redis
        docker compose up -d

        # Прогнать тесты (интеграционный включается через REDIS_INTEGRATION=1)
        go test ./...
        REDIS_INTEGRATION=1 go test ./...

        # Запустить main (печатает marker; замени stub на реализацию)
        go run .
        ```

        ## Заметка автора

        Это baseline-шаблон, сгенерированный платформой. Бизнес-сущность задачи (что конкретно реализовать в `main.go`, какие тесты сделать строгими) расширяется по ходу итераций — параллельно с углублением теории урока.
