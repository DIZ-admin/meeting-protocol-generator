# Конвейер генерации протоколов совещаний

Автоматизированный конвейер для преобразования аудиозаписей совещаний в структурированные, удобочитаемые и машиночитаемые протоколы, сокращая ручной труд и обеспечивая единообразие документации.

## Возможности

- Распознавание речи с определением спикеров (диаризация)
- Автоматическое извлечение решений и задач
- Генерация структурированных протоколов в форматах Markdown и JSON
- Поддержка нескольких языков (DE, EN, RU)
- Отправка уведомлений и протоколов в Telegram
- Поддержка пакетной обработки нескольких аудиофайлов
- Веб-интерфейс для загрузки и обработки файлов с OpenAPI спецификацией
- Отслеживание прогресса обработки в реальном времени
- Улучшенная обработка транскриптов с учетом информации об участниках
- Валидация входных и выходных данных с использованием JSON Schema
- Структурированный вывод Map-Reduce логики в формате JSON
- Расширенное логирование с настройкой через YAML-конфигурацию
- CI/CD интеграция с автоматическими тестами и проверкой качества кода

## Структура проекта

```
meeting/
├── app/                           # Основной пакет приложения
│   ├── cli.py                     # Старый CLI интерфейс (argparse)
│   ├── cli_typer.py               # Новый CLI интерфейс на Typer
│   ├── config/                    # Модуль конфигурации
│   ├── core/                      # Ядро системы и бизнес-логика
│   │   ├── models/                # Модели данных
│   │   ├── services/              # Бизнес-логика
│   │   └── exceptions.py          # Иерархия исключений
│   ├── adapters/                  # Внешние интеграции
│   │   ├── asr/                   # Адаптеры для ASR
│   │   ├── llm/                   # Адаптеры для языковых моделей
│   │   └── notifications/         # Адаптеры для уведомлений
│   ├── utils/                     # Утилиты
│   ├── templates/                 # Шаблоны для генерации протоколов
│   └── web/                       # Веб-интерфейс на FastAPI
│       ├── app.py                 # Основной файл веб-приложения
│       ├── templates/             # HTML шаблоны
│       └── static/                # Статические файлы (CSS, JS)
├── schema/                        # JSON схемы
├── tests/                         # Тесты
│   ├── unit/                      # Модульные тесты
│   └── integration/               # Интеграционные тесты
├── scripts/                       # Скрипты для запуска и управления
│   └── start_web.py               # Скрипт для запуска веб-интерфейса
├── output/                        # Директория для выходных файлов
├── uploads/                       # Директория для загруженных файлов
├── logs/                          # Директория для логов
├── cache/                         # Кэш для API запросов
├── .env.example                   # Пример файла окружения
├── Dockerfile                     # Dockerfile для сборки образа
├── docker-compose.yml             # Настройки Docker Compose
├── pyproject.toml                 # Метаданные проекта и зависимости
└── README.md                      # Документация
```

## Установка

### Предварительные требования

- Python 3.11 или выше
- API-ключи для внешних сервисов:
  - Replicate API Key для ASR
  - OpenAI API Key для обработки текста
  - Telegram Bot Token и Chat ID (опционально)

### Установка с использованием pip

1. Клонируйте репозиторий:
   ```bash
   git clone https://github.com/DIZ-admin/meeting-protocol-generator.git
   cd meeting-protocol-generator
   ```

2. Создайте и активируйте виртуальное окружение:
   ```bash
   python -m venv .venv
   source .venv/bin/activate  # для Linux/macOS
   # .venv\Scripts\activate    # для Windows
   ```

3. Установите зависимости:
   ```bash
   pip install -e .
   pip install pytest pytest-cov flake8 mypy colorlog pyyaml
   ```

4. Создайте файл .env на основе .env.example и заполните необходимые переменные окружения:
   ```bash
   cp .env.example .env
   # Отредактируйте .env и добавьте ваши API-ключи
   ```

### Установка с использованием Docker

1. Клонируйте репозиторий:
   ```bash
   git clone https://github.com/DIZ-admin/meeting-protocol-generator.git
   cd meeting-protocol-generator
   ```

2. Создайте файл .env на основе .env.example и заполните необходимые переменные окружения:
   ```bash
   cp .env.example .env
   # Отредактируйте .env и добавьте ваши API-ключи
   ```

3. Соберите и запустите Docker-контейнер:
   ```bash
   docker-compose up -d --build
   ```

## Использование

### Веб-интерфейс

Запуск веб-интерфейса локально:

```bash
# Активируйте виртуальное окружение перед запуском
source .venv/bin/activate
python scripts/start_web.py
```

Или с использованием Docker:

```bash
docker-compose up web
```

После запуска веб-интерфейс будет доступен по адресу http://localhost:8000

Веб-интерфейс предоставляет следующие возможности:

- Загрузка аудиофайлов через браузер
- Ввод метаданных протокола (название, дата, место, участники и т.д.)
- Отслеживание прогресса обработки в реальном времени
- Просмотр и скачивание готовых протоколов в форматах Markdown и JSON

### Улучшенный CLI интерфейс (Typer)

Новый CLI интерфейс на основе Typer предоставляет улучшенный пользовательский опыт с прогресс-барами и цветным выводом:

```bash
# Активируйте виртуальное окружение перед запуском
source .venv/bin/activate

# Обработка одного файла
python -m app.cli_typer process /путь/к/аудиофайлу.m4a --lang ru

# Пакетная обработка файлов
python -m app.cli_typer process /путь/к/директории --batch --lang ru

# Запуск веб-интерфейса
python -m app.cli_typer web
```

Доступные опции команды `process`:

- `audio`: Путь к аудиофайлу или директории с аудиофайлами
- `--batch`, `-b`: Обработать все аудиофайлы в директории
- `--lang`, `-l`: Код языка (например, 'de', 'en', 'ru')
- `--output`, `-o`: Директория для сохранения результатов
- `--skip-telegram`: Пропустить отправку уведомлений в Telegram
- `--title`, `-t`: Название встречи
- `--date`, `-d`: Дата встречи в формате YYYY-MM-DD
- `--location`: Место проведения встречи
- `--organizer`: Организатор встречи
- `--participants`: Список участников (через запятую)
- `--agenda`: Список пунктов повестки (через запятую)
- `--debug`: Включить подробное логирование

Для просмотра всех опций:

```bash
python -m app.cli_typer --help
python -m app.cli_typer process --help
```

### Старый CLI интерфейс (argparse)

Старый CLI интерфейс также доступен для обратной совместимости:

```bash
# Обработка одного аудиофайла
python -m app.cli /путь/к/аудиофайлу.m4a --lang de

# Обработка всех аудиофайлов в директории
python -m app.cli /путь/к/директории --batch --lang de
```

### С использованием Docker

```bash
# Обработка одного файла
docker-compose run --rm app /путь/к/аудиофайлу.m4a --lang de

# Пакетная обработка
docker-compose run --rm batch
```

## Разработка

### Запуск тестов

```bash
# Запуск всех тестов
pytest

# Запуск с отчетом о покрытии
pytest --cov=app tests/

# Запуск только модульных тестов
pytest tests/unit/

# Запуск только интеграционных тестов
pytest tests/integration/
```

### Проверка качества кода

```bash
# Проверка стиля кода
flake8 app/ tests/

# Проверка типов
mypy app/
```

## Лицензия

MIT License - см. файл [LICENSE](LICENSE) для деталей.