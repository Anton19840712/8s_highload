# InsureTech Sprint 8 Overview

Эта папка `8s_highload` содержит всё, что требуется для Sprint 8: архитектурные артефакты и нагрузочные доки.

## Кратко по заданиям
| Task | Суть | Где смотреть |
| --- | --- | --- |
| Task1 | Технологическая архитектура To-Be | `Task1/InureTech_технологическая архитектура_to-be.drawio` |
| Task2 | Dynamic Scaling: манифесты, HPA, Locust, лог + доказательства | `Task2/README.md`, `Task2/*.yaml`, `Task2/locustfile.py`, `Task2/scale-*.jpg` |
| Task3 | Event-driven C4 + риски | `Task3/insuretech_c4_event_driven.drawio`, `Task3/problems_and_risks.md` |
| Task4 | ОСАГО C4 | `Task4/insuretech_c4_osago.drawio` |
| Task5 | GraphQL-схема | `Task5/schema.graphql` |
| Task6 | Nginx конфигурация для rate limiting | `Task6/nginx.conf` |

## Как повторить Task2
1. Перейти в `Task2` и прочитать `README.md` — там полный журнал команд, описание `scale-memory.jpg` и `scale-rps.jpg`.
2. Запустить `minikube start --driver=docker`, затем `kubectl apply` для deployment/service и `hpa-memory`.
3. Держать `kubectl port-forward svc/insuretech-app-service 30080:80`, запускать Locust из `Task2/locustfile.py`, и фиксировать HPA/Pods под нагрузкой.

## Коммиты
Применяется схема `do/so/in/ex` (см. `Task2/README.md` и `../README.md`), чтобы описывать действие, эффект, файлы и пример.

## Доказательства
- `Task2/scale-memory.jpg` — HPA + `kubectl get pods`
- `Task2/scale-rps.jpg` / `Task2/scale-rps-aux.jpg` — Locust (+ `kubectl top pods`)

## Общие ссылки
- `sprints/8s/README.md` — дополнительный обзор всего Sprint 8 (таблица, контекст, способом проверки).
