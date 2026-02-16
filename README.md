# InsureTech Sprint 8 Overview

В этой папке содержится всё по Task1–Task6: архитектурные диаграммы, конфигурации и доказательства Task2.

| Task | Краткое описание | Файл |
| --- | --- | --- |
| Task1 | Технологическая архитектура To-Be | `Task1/InureTech_технологическая архитектура_to-be.drawio` |
| Task2 | Dynamic Scaling и нагрузка Locust | `Task2/README.md`, `Task2/*.yaml`, `Task2/scale-memory.jpg`, `Task2/scale-rps.jpg`, `Task2/scale-rps-aux.jpg` |
| Task3 | Event-driven C4 + риски | `Task3/insuretech_c4_event_driven.drawio`, `Task3/problems_and_risks.md` |
| Task4 | ОСАГО-сценарий в C4 | `Task4/insuretech_c4_osago.drawio` |
| Task5 | GraphQL схема | `Task5/schema.graphql` |
| Task6 | Конфигурация Nginx | `Task6/nginx.conf` |

### Как проверить Task2
1. Открой `Task2/README.md`: там журнал команд, ссылки на скрины и инструкции.
2. Запусти `minikube start --driver=docker`, затем `kubectl apply -f Task2/deployment.yaml`, `Task2/service.yaml`, `Task2/hpa-memory.yaml`.
3. Держи `kubectl port-forward svc/insuretech-app-service 30080:80` и запускай `locust -f Task2/locustfile.py --host http://127.0.0.1:30080 --headless -u 20 -r 5 --run-time 1m`.

Ссылки на доказательства:
- `Task2/scale-memory.jpg`
- `Task2/scale-rps.jpg`, `Task2/scale-rps-aux.jpg`
