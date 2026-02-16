# Task2: Доказательства динамического масштабирования

## Текущий статус
- Minikube с `metrics-server` запущен; деплой теперь использует образ `k8s.gcr.io/echoserver:1.10`, и актуальный под `insuretech-app-77c6ff885c-m8mf7` стабильно находится в состоянии `1/1 Running`.
- Работает только HPA по памяти (`insuretech-app-hpa-memory`): она фиксирует около 14% использования памяти, состояние `ScalingActive True`, а старые события `AmbiguousSelector`/`FailedGetResourceMetric` относятся к удалённому RPS-HPA.
- Проведены два прогона Locust (20 пользователей, 5 новых пользователей в секунду, 1 минута каждый). Второй прогон дал 403 запроса, 0 неудач, ≈6,7 req/s, тайминги 9–61 мс; `kubectl top pods` показывает ≈1m CPU и 2 МБ памяти во время нагрузки.

## Журнал команд
- 2026-02-16 17:32 — `kubectl apply -f deployment.yaml` — манифест перекатился без изменений.
- 2026-02-16 17:32 — `kubectl apply -f service.yaml` — сервис уже был в актуальном состоянии.
- 2026-02-16 17:32 — `kubectl rollout restart deployment/insuretech-app` — перезапустили деплой, чтобы подтянуть новый образ.
- 2026-02-16 17:32 — `kubectl get pods -l app=insuretech-app -o wide` — старый под завершался, новый появился и стал `1/1`.
- 2026-02-16 17:32 — `kubectl describe pod insuretech-app-595b5fdb7b-d82lb` — проверили, что старый под остановился корректно, без `ImagePullBackOff`.
- 2026-02-16 18:26 — `kubectl get pods -l app=insuretech-app` — остался только под `insuretech-app-6cdbf888f6-jwz6p`.
- 2026-02-16 18:26 — `kubectl top pods` — под показывает около 1m CPU и 2 МБ памяти в простой.
- 2026-02-16 18:26 — `kubectl describe hpa insuretech-app-hpa-memory` — 13% памяти, `ScalingActive True`, в событиях появляются старые `AmbiguousSelector`.
- 2026-02-16 18:27 — `kubectl port-forward svc/insuretech-app-service 30080:80` — сервис доступен на `localhost:30080`.
- 2026-02-16 18:27 — `locust -f locustfile.py --host http://127.0.0.1:30080 --headless -u 20 -r 5 --run-time 1m` — 388 запросов, 0 ошибок, ≈6,5 req/s (см. `scale-rps.jpg`).
- 2026-02-16 18:27 — `kubectl describe hpa insuretech-app-hpa-memory` (во время нагрузки) — 13–14% памяти, `ScalingActive True`, вывод сохранён в `scale-memory.jpg`.
- 2026-02-16 19:01 — второй запуск `locust …` — 403 запроса, 0 ошибок, ≈6,7 req/s, перцентили 9–61 мс (см. `scale-rps.jpg`, `scale-rps-aux.jpg`).
- 2026-02-16 19:01 — `kubectl describe hpa insuretech-app-hpa-memory` — 14% памяти, `ScalingActive True`, тот же вывод используется для `scale-memory.jpg`.

## Доказательства
- `scale-memory.jpg`: скриншот или лог, объединяющий `kubectl describe hpa insuretech-app-hpa-memory` и `kubectl get pods -l app=insuretech-app` во время прогона Locust, чтобы показать, что HPA видит метрики памяти и один под `1/1`.
- `scale-rps.jpg` и `scale-rps-aux.jpg`: лог или скриншот окна Locust (второй прогон) с 403 запросами, 0 ошибками, ~6,7 req/s, а также параллельный вывод `kubectl top pods`/`kubectl get pods`, демонстрирующий реакцию кластера.
- Упоминайте эти файлы в журнале команд, чтобы и текст, и скриншоты ссылались на единое доказательство.

## Что дальше
1. Если потребуется дополнительная HPA (например, по RPS), убедитесь, что она не пересекается по селектору с существующей, иначе появится ещё одно `AmbiguousSelector`.
2. Продолжайте добавлять в журнал timestamp + команду + результат, чтобы сохранялась история команд и был очевиден источник данных.
