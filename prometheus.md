# Prometheus

Prometheus - это система мониторинга и алертинга, ориентированная на надежность и высокую доступность. В Prometheus используются различные типы данных и конструкции для организации и хранения метрик. Вот несколько основных типов:

Конечно, давайте рассмотрим основные типы метрик в Prometheus более подробно и на простом языке:

### 1. Counter (Счетчик)

**Что это?**
Счетчик — это простой тип метрики, который только увеличивается. Его значение не может уменьшаться (кроме как при перезапуске системы).

**Примеры использования:**
- Подсчет количества запросов на сервер.
- Подсчет общего числа ошибок в системе.

**Как работает:**
Если вы хотите отслеживать, сколько раз что-то произошло, используйте счетчик. Например, каждый раз, когда пользователь заходит на сайт, счетчик увеличивается на 1.

### 2. Gauge (Гейдж)

**Что это?**
Гейдж — это тип метрики, значение которой может как увеличиваться, так и уменьшаться.

**Примеры использования:**
- Текущая загрузка CPU.
- Использование памяти.
- Количество активных пользователей на сайте.

**Как работает:**
Гейдж — это как термометр. Он показывает текущую температуру, и эта температура может изменяться в обе стороны.

### 3. Histogram (Гистограмма)

**Что это?**
Гистограмма — это тип метрики, который позволяет измерять распределение значений.

**Примеры использования:**
- Время загрузки веб-страницы.
- Задержка ответа от сервера.

**Как работает:**
Гистограмма разбивает диапазон значений на интервалы (например, время отклика: 0-1 сек, 1-2 сек, и так далее) и подсчитывает, сколько раз значение попало в каждый интервал.

### 4. Summary (Сводка)

**Что это?**
Сводка — это тип метрики, похожий на гистограмму, но который также может вычислять процентили.

**Примеры использования:**
- Время обработки запроса.
- Размер передаваемых данных.

**Как работает:**
Сводка собирает все значения и может вычислять, например, медиану или 99-й процентиль (значение, ниже которого находится 99% всех наблюдений).

### 5. Untyped (Без типа)

**Что это?**
Untyped используется, когда неизвестен тип метрики.

**Как работает:**
Prometheus обрабатывает такие метрики, не делая предположений о их поведении.

`Summary` и `Histogram` — два типа метрик в Prometheus, которые используются для измерения распределения значений, например, времени отклика запросов или размеров передаваемых пакетов. Несмотря на то, что оба типа предназначены для решения схожих задач, они имеют различия в механизме работы и предоставляемых возможностях.

### Summary

- **Процентили**: `Summary` напрямую вычисляет и хранит процентили. Это означает, что вы можете запросить, например, 95-й процентиль времени отклика запросов, и Prometheus вернет точное значение.
- **Глобальные процентили**: Вычисленные процентили применимы к глобальным данным, а не к отдельным бакетам или интервалам, как в случае с `Histogram`.
- **Больше ресурсов**: Так как `Summary` вычисляет процентили на лету, это требует больше вычислительных ресурсов и памяти.
- **Мгновенные данные**: `Summary` предоставляет мгновенные данные о распределении, что может быть полезно для наблюдения за системой в реальном времени.

### Histogram

- **Бакеты**: `Histogram` использует бакеты для хранения количества значений, попавших в каждый интервал. Вам нужно заранее определить границы бакетов.
- **Агрегация**: Благодаря бакетам `Histogram` легче агрегировать данные с разных источников. Это делает его более подходящим для использования в распределенных системах.
- **Процентили**: Вычисление процентилей для `Histogram` требует дополнительных операций и может быть менее точным, поскольку оно основано на границах бакетов.
- **Эффективность**: В целом `Histogram` более эффективен с точки зрения использования ресурсов, поскольку не требует вычислений на лету для каждого запроса.

### Вывод

Выбор между `Summary` и `Histogram` зависит от конкретных требований к мониторингу и доступных ресурсов.

- Используйте `Summary`, если вам нужны точные глобальные процентили и ресурсов достаточно.
- Используйте `Histogram`, если вам нужна высокая производительность, возможность агрегации данных и точность процентилей не критична.

Важно также учесть, что границы бакетов в `Histogram` следует выбирать внимательно, так как от этого зависит точность и полезность собираемых метрик.

Summary высчитывается на стороне приложения, которое экспортирует метрики для Prometheus.

Когда вы используете тип метрики Summary в своем приложении, оно отслеживает и вычисляет процентили, среднее значение, сумму и количество наблюдений локально. Затем эти вычисленные данные экспортируются и могут быть собраны Prometheus при выполнении операции сбора метрик.

Это означает, что при использовании Summary все необходимые вычисления производятся на стороне приложения, что может привести к дополнительной нагрузке, особенно если количество и частота наблюдений велики.

Тем не менее, благодаря локальным вычислениям, Summary позволяет получать точные процентили для данных, которые уже находятся в приложении, без необходимости передачи всех индивидуальных значений в Prometheus для последующего анализа. Это может быть особенно полезно в распределенных системах, где агрегация данных на стороне сервера может быть ресурсоемкой задачей.

---

Выбор типа метрики зависит от того, что вы хотите измерить и как вы собираетесь использовать эти данные. Счетчики и гейджи — это более простые и интуитивно понятные типы, в то время как гистограммы и сводки предоставляют более мощные инструменты для анализа распределения значений. Untyped используется реже и в особых случаях.

# Установка Prom

Развертывание Prometheus включает в себя несколько этапов. Ниже приведен общий процесс установки и настройки Prometheus на Linux-системе. Обратите внимание, что детали могут варьироваться в зависимости от вашей операционной системы и конкретных требований.

### Шаг 1: Загрузка и распаковка Prometheus

1. Перейдите на официальный сайт Prometheus и скачайте последнюю стабильную версию для вашей операционной системы: [https://prometheus.io/download/](https://prometheus.io/download/)
2. Распакуйте скачанный архив. Например, если вы скачали версию для Linux:
   ```
   tar xvfz prometheus-*.tar.gz
   cd prometheus-*
   ```

### Шаг 2: Настройка Prometheus

1. Создайте файл конфигурации `prometheus.yml`. Пример простой конфигурации:
   ```yaml
   global:
     scrape_interval: 15s

   scrape_configs:
     - job_name: 'prometheus'
       static_configs:
         - targets: ['localhost:9090']
   ```
   Этот файл указывает Prometheus собирать данные о себе каждые 15 секунд.

### Шаг 3: Запуск Prometheus

1. Запустите Prometheus с указанием файла конфигурации:
   ```
   ./prometheus --config.file=prometheus.yml
   ```
   Prometheus теперь должен быть запущен и доступен по адресу [http://localhost:9090](http://localhost:9090).

### Шаг 4: Проверка установки

1. Откройте в веб-браузере [http://localhost:9090](http://localhost:9090).
2. Перейдите в раздел "Targets" (Цели), чтобы убедиться, что Prometheus успешно собирает данные о себе.

### Шаг 5: Настройка службы (опционально)

Чтобы Prometheus запускался автоматически при старте системы, вы можете настроить его как службу systemd (или другую систему инициализации, в зависимости от вашей ОС).

Пример файла службы для systemd (`/etc/systemd/system/prometheus.service`):
```ini
[Unit]
Description=Prometheus
After=network-online.target

[Service]
User=prometheus
Restart=on-failure
ExecStart=/путь/к/prometheus \
  --config.file=/путь/к/prometheus.yml

[Install]
WantedBy=multi-user.target
```
Не забудьте после создания файла перезагрузить конфигурацию systemd и включить службу:
```
sudo systemctl daemon-reload
sudo systemctl enable --now prometheus
```

Эти шаги дадут вам базовую установку Prometheus. Для более продвинутой настройки и интеграции с другими сервисами, вам потребуется дополнительная настройка.


# Конфигурация prom

Создание обширной и сложной конфигурации для Prometheus зависит от специфики вашей инфраструктуры, набора сервисов и требований к мониторингу. Ниже приведен пример развернутой конфигурации с комментариями, который демонстрирует различные возможности Prometheus.

```yaml
global:
  scrape_interval: 15s # Задает глобальный интервал сбора метрик
  evaluation_interval: 15s # Интервал вычисления правил алертов и агрегаций
  external_labels:
    datacenter: 'eu-central' # Глобальные метки, добавляемые ко всем временным рядам

rule_files: # Пути к файлам с правилами для алертов и агрегаций
  - 'rules/*.yml'

alerting: # Конфигурация системы алертов
  alertmanagers:
    - static_configs:
        - targets:
            - 'alertmanager:9093'

scrape_configs: # Конфигурация сбора метрик
  - job_name: 'prometheus' # Название задания для сбора метрик с самого Prometheus
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node_exporter' # Сбор метрик с серверов
    scrape_interval: 10s # Переопределение глобального интервала сбора метрик для данного задания
    static_configs:
      - targets: ['node1:9100', 'node2:9100']

  - job_name: 'web_app' # Пример мониторинга веб-приложения
    metrics_path: '/metrics' # Путь, по которому доступны метрики
    scheme: 'https' # Использование HTTPS для доступа к метрикам
    static_configs:
      - targets: ['app1:443', 'app2:443']
    tls_config:
      ca_file: '/path/to/ca.pem'
      cert_file: '/path/to/cert.pem'
      key_file: '/path/to/key.pem'
      insecure_skip_verify: false

  - job_name: 'kubernetes-nodes' # Пример мониторинга узлов Kubernetes
    kubernetes_sd_configs:
      - api_server: 'https://k8s.api.server:6443'
        role: node
    tls_config:
      ca_file: '/var/run/secrets/kubernetes.io/serviceaccount/ca.crt'
    bearer_token_file: '/var/run/secrets/kubernetes.io/serviceaccount/token'

  - job_name: 'kubernetes-pods' # Пример мониторинга подов Kubernetes
    kubernetes_sd_configs:
      - api_server: 'https://k8s.api.server:6443'
        role: pod
    relabel_configs: # Пример переименования меток
      - source_labels: [__meta_kubernetes_pod_name]
        target_label: pod_name

remote_write: # Настройка отправки данных в удаленное хранилище
  - url: 'https://remote.storage/api/write'
    bearer_token: 'your-bearer-token'
    remote_timeout: '30s'

remote_read: # Настройка чтения данных из удаленного хранилища
  - url: 'https://remote.storage/api/read'
    bearer_token: 'your-bearer-token'
    read_recent: true

```

Этот пример демонстрирует различные аспекты конфигурации Prometheus, включая глобальные настройки, правила для алертов, интеграцию с Alertmanager, сбор метрик с различных источников, включая серверы (с использованием Node Exporter), веб-приложения и кластеры Kubernetes, а также отправку данных в удаленные системы для долгосрочного хранения.

Пожалуйста, учтите, что вам нужно будет адаптировать этот конфигурационный файл в соответствии с вашей средой и требованиями.

# ФУНКЦИИ PROMQL

Prometheus предоставляет мощный язык запросов PromQL (Prometheus Query Language), который позволяет пользователю выбирать и агрегировать данные временных рядов. Вот несколько самых популярных и полезных функций PromQL:

### 1. rate()

`rate()` вычисляет среднюю скорость изменения значения счетчика за заданный интервал времени.

**Пример использования:**
```promql
rate(http_requests_total[5m])
```
Это покажет среднее количество HTTP-запросов в секунду за последние 5 минут.

### 2. increase()

`increase()` вычисляет увеличение значения счетчика за заданный интервал времени.

**Пример использования:**
```promql
increase(http_requests_total[1h])
```
Это покажет общее увеличение количества HTTP-запросов за последний час.

### 3. sum()

`sum()` агрегирует значения временного ряда, складывая их.

**Пример использования:**
```promql
sum(http_requests_total)
```
Это вернет общее количество HTTP-запросов.

### 4. avg()

`avg()` вычисляет среднее значение временных рядов.

**Пример использования:**
```promql
avg(cpu_usage{instance="localhost"})
```
Это покажет среднее использование CPU на инстансе localhost.

### 5. max() и min()

`max()` и `min()` выбирают максимальное и минимальное значения из временных рядов соответственно.

### 6. histogram_quantile()

`histogram_quantile()` вычисляет значение квантиля на основе данных гистограммы.

**Пример использования:**
```promql
histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))
```
Это вычисляет 95-й процентиль времени отклика HTTP-запросов за последние 5 минут.

### 7. label_replace()

`label_replace()` используется для переименования, добавления или удаления меток в временном ряду.

### 8. predict_linear()

`predict_linear()` используется для прогнозирования значения временного ряда в будущем, основываясь на линейной регрессии.

### 9. topk() и bottomk()

`topk()` и `bottomk()` возвращают топ-N записей из временных рядов.

### 10. count()

`count()` подсчитывает количество элементов в выборке.

Это только некоторые из функций, доступных в PromQL. Prometheus предлагает обширный набор инструментов для работы с временными рядами, агрегации данных, вычисления процентилей и многого другого. Каждая из этих функций может быть полезна в зависимости от конкретной задачи мониторинга и анализа данных.