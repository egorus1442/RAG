# 📋 Информация о проекте RAG API

## Описание

RAG API - это минималистичное, но полностью функциональное REST-API для Retrieval-Augmented Generation (RAG) на Python. Проект реализует полный цикл работы с документами: загрузка, обработка, индексация и генерация ответов на основе контекста.

## Возможности

- 📄 Загрузка документов (PDF, DOCX, TXT)
- 🔍 Извлечение текста и разбивка на чанки
- 🧠 Генерация эмбедингов через OpenAI API
- 💾 Хранение эмбедингов в ChromaDB (SQLite)
- 🔎 Семантический поиск по документам
- 🤖 Генерация ответов через OpenRouter API
- 🔐 Аутентификация по API токену

## Быстрый старт

### 1. Установка зависимостей
```bash
# Активируйте виртуальную среду
source venv/bin/activate

# Установите зависимости
pip install -r requirements.txt
```

### 2. Настройка переменных окружения
```bash
# Скопируйте example.env в .env
cp example.env .env

# Отредактируйте .env файл с вашими API ключами
nano .env
```

### 3. Запуск
```bash
# Автоматический запуск с проверкой проблем
python3 start_rag.py

# Или обычный запуск
python3 run.py
```


## Архитектура

### Основные компоненты

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   FastAPI App   │    │   FileProcessor │    │  Text Extractor │
│   (main.py)     │◄──►│   (services/)   │◄──►│  (utils/)       │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         ▼                       ▼                       ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Auth Module   │    │   File Storage  │    │  Embeddings     │
│   (auth.py)     │    │   (uploads/)    │    │  Service        │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         ▼                       ▼                       ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   LLM Service   │    │   ChromaDB      │    │   Config        │
│   (OpenRouter)  │    │   (SQLite)      │    │   (config.py)   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### Поток данных

1. **Загрузка файла** (`POST /upload`)
   - Файл принимается через FastAPI
   - **FileProcessor** обрабатывает файл:
     - Сохраняет оригинал
     - Конвертирует в TXT через TextExtractor
     - Возвращает метаданные
   - Текст разбивается на чанки
   - Эмбединги генерируются через OpenAI API
   - Эмбединги сохраняются в ChromaDB

2. **Поиск и генерация** (`POST /query`)
   - Запрос преобразуется в эмбединг
   - ChromaDB находит похожие документы
   - Контекст формируется из найденных документов
   - OpenRouter генерирует ответ на основе контекста

3. **Удаление файла** (`DELETE /file/{file_id}`)
   - **FileProcessor** удаляет все версии файла (оригинал + TXT)
   - Эмбединги удаляются из ChromaDB

### Преимущества архитектуры

- **Унификация обработки:** Все файлы конвертируются в TXT, что упрощает дальнейшую обработку
- **Модульность:** Логика обработки файлов вынесена в отдельный сервис
- **Тестируемость:** FileProcessor можно легко тестировать независимо
- **Расширяемость:** Легко добавить новые форматы файлов или изменить логику обработки
- **Надежность:** Автоматическая очистка файлов при ошибках

## Технологический стек

### Основные технологии
- **FastAPI** - современный веб-фреймворк для Python
- **ChromaDB** - векторная база данных для хранения эмбедингов
- **SQLite** - базовая СУБД для ChromaDB
- **OpenAI API** - генерация эмбедингов (text-embedding-ada-002)
- **OpenRouter API** - генерация ответов (gpt-4o-mini)

### Библиотеки для обработки файлов
- **PyMuPDF** - извлечение текста из PDF
- **python-docx** - извлечение текста из DOCX
- **python-multipart** - обработка загрузки файлов

### Дополнительные библиотеки
- **pydantic** - валидация данных
- **python-dotenv** - управление переменными окружения
- **uvicorn** - ASGI сервер
- **logging** - система логирования

## Структура кода

### Модули

#### `main.py` - Основное приложение
- Определение FastAPI приложения
- Эндпоинты API
- Обработка ошибок
- Middleware

#### `config.py` - Конфигурация
- Загрузка переменных окружения
- Настройки приложения
- Создание директорий

#### `auth.py` - Аутентификация
- Проверка API токенов
- Middleware для защиты эндпоинтов

#### `models.py` - Модели данных
- Pydantic модели для валидации
- Схемы запросов и ответов

#### `utils/text_extractor.py` - Извлечение текста
- Поддержка PDF, DOCX, TXT
- Разбивка текста на чанки
- Обработка ошибок

#### `services/embeddings_service.py` - Работа с эмбедингами
- Интеграция с OpenAI API
- Управление ChromaDB
- Поиск похожих документов

#### `services/file_processor.py` - Обработка файлов
- Конвертация файлов в TXT формат
- Сохранение оригиналов и текстовых версий
- Управление жизненным циклом файлов
- Автоматическая очистка при ошибках

#### `services/llm_service.py` - Генерация ответов
- Интеграция с OpenRouter API
- Формирование промптов
- Обработка контекста

#### `fix_dependencies.py` - Исправление зависимостей
- Автоматическое исправление проблем с NumPy
- Установка недостающих модулей
- Проверка совместимости версий

#### `start_rag.py` - Автоматический запуск
- Проверка всех зависимостей
- Исправление проблем
- Автоматический запуск API

## API Endpoints

### Аутентификация
Все эндпоинты требуют заголовок `Authorization: Bearer <API_TOKEN>`

### Основные эндпоинты

1. **POST /upload** - Загрузка файла
   - Принимает multipart/form-data
   - Поддерживает PDF, DOCX, TXT
   - Возвращает file_id и метаданные

2. **POST /query** - Запрос к документам
   - Принимает JSON с вопросом
   - Выполняет семантический поиск
   - Генерирует ответ на основе контекста

3. **DELETE /file/{file_id}** - Удаление файла
   - Удаляет файл и эмбединги
   - Возвращает подтверждение

4. **GET /health** - Проверка состояния
   - Мониторинг работоспособности API

## Конфигурация

### Переменные окружения

| Переменная | Описание | По умолчанию |
|------------|----------|--------------|
| `OPENAI_API_KEY` | Ключ OpenAI для эмбедингов | - |
| `OPENROUTER_API_KEY` | Ключ OpenRouter для генерации | - |
| `API_TOKEN` | Токен для аутентификации API | - |
| `UPLOAD_DIR` | Директория для файлов | `uploads` |
| `CHUNK_SIZE` | Размер чанка текста | `1000` |
| `CHUNK_OVERLAP` | Перекрытие чанков | `200` |
| `TOP_K` | Количество похожих документов | `5` |

### Настройки ChromaDB
- Путь к базе данных: `./chroma_db`
- Коллекция: `documents`
- Метрика расстояния: `cosine`

## Безопасность

### Аутентификация
- Bearer token аутентификация
- Все эндпоинты защищены
- Токен настраивается через переменную окружения

### Валидация данных
- Pydantic модели для всех запросов
- Проверка типов файлов
- Валидация размера файлов

### Обработка ошибок
- Централизованная обработка исключений
- Структурированные ответы об ошибках
- Подробное логирование всех операций

### Логирование
- Все операции записываются в файл `rag_api.log`
- Отслеживание обработки файлов (стандартная vs LLM)
- Логирование запросов и ответов
- Мониторинг ошибок и предупреждений
- Скрипты для анализа логов (`view_logs.py`, `monitor_logs.py`)

## Производительность

### Оптимизации
- Асинхронная обработка запросов
- Кэширование эмбедингов в ChromaDB
- Эффективная разбивка текста на чанки
- Параллельная обработка файлов

### Масштабируемость
- Модульная архитектура
- Разделение ответственности
- Возможность замены компонентов
- Поддержка различных провайдеров LLM


## Тестирование

### Автоматические тесты
- `test_api.py` - интеграционные тесты
- Проверка всех эндпоинтов
- Тестирование обработки ошибок

### Ручное тестирование
- Swagger UI: http://localhost:8000/docs
- curl команды в документации
- Примеры в README

## Примеры использования

### Загрузка файла
```bash
curl -X POST "http://localhost:8000/upload" \
  -H "Authorization: Bearer your_token" \
  -F "file=@document.pdf"
```

### Запрос к API
```bash
curl -X POST "http://localhost:8000/query" \
  -H "Authorization: Bearer your_token" \
  -H "Content-Type: application/json" \
  -d '{"question": "Что такое ИИ?"}'
```