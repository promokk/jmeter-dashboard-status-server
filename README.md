# Jmeter-dashboard-status-server
Дашборд Grafana для мониторинга статусов генераторов нагрузки (серверов) 
при запуске тестов с помощью [Jmeter](https://jmeter.apache.org/). 
Позволяет определить свободен или занят сервер процессом jmeter или jmeter-server.  
Это ускорит поиск свободного сервера для запуска теста.  
Для работы дашборда используется Prometheus и [Process-exporter](https://github.com/ncabatoff/process-exporter).

Дашборд загружен в [Grafana Labs](https://grafana.com/grafana/dashboards/21999-jmeter-dashboard-status-server/).

---
# Оглавление
* [Описание дашборда](#dashboardDescription)
  * [Count Server](#countServer)
  * [Count Status](#countStatus)
  * [Server Status](#serverStatus)
* [Настройка Prometheus и Process-exporter](#settings)

---
## Описание дашборда <a id="dashboardDescription"></a>
Если для тестов используется больше одного генератора нагрузки (сервера), 
то иногда возникает вопрос, свободен ли сервер.  
Этот небольшой дашборд позволит найти свободные сервера для запуска тестов.

![Jmeter Status Server - картинка](https://github.com/promokk/jmeter-dashboard-status-server/main/data/Jmeter_Status_Server.png)

---
### Count Server <a id="countServer"></a>
Общее количество серверов.  
Метрика зависит от количества target (серверов) в prometheus для process-exporter.

---
### Count Status <a id="countStatus"></a>
Количество серверов по статусам доступности:  
* Free - свободные сервера
* Busy - занятые сервера
* Unhealthy - нездоровые сервера. Либо сервер недоступен, либо выключен process-exporter.  
  Следует посмотреть состояние prometheus targets --> http://{host}:9090/targets

---
### Server Status <a id="serverStatus"></a>
Таблица серверов со статусами доступности:
* Free - сервер свободен
* Busy - сервер занят

**Примечание:** сервера со статусом "Unhealthy" не отображаются.

---
## Настройка Prometheus и Process-exporter <a id="settings"></a>
1. Установить и настроить [Prometheus](https://prometheus.io/docs/prometheus/latest/getting_started/)  
   Установить на Linux можно с помощью bash-скрипта --> 
   [install-prometheus.sh](https://github.com/promokk/bash-scripts/blob/main/install-scripts/install-prometheus.sh)
    * Добавить новый job в файл конфигурации prometheus /etc/prometheus/prometheus.yml.  
      Вместо {host} необходимо указать свои сервера.
    ~~~shell
    scrape_configs:
      - job_name: 'process-exporter'
        scrape_interval: 10s
        static_configs:
          - targets: [
            '{host}:9256',
            '{host}:9256'
          ]
     ~~~
2. Установить и настроить [Process-exporter](https://github.com/ncabatoff/process-exporter?tab=readme-ov-file)  
   Установить на Linux можно с помощью bash-скрипта --> 
   [install-process-exporter.sh](https://github.com/promokk/bash-scripts/blob/main/install-scripts/install-process-exporter.sh)
