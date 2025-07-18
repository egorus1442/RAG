# Changelog

## [1.1.0] - 2024-01-XX

### Добавлено
- **FileProcessor сервис** (`services/file_processor.py`)
  - Централизованная обработка загруженных файлов
  - Автоматическая конвертация всех файлов в TXT формат
  - Сохранение оригиналов и текстовых версий
  - Управление жизненным циклом файлов
  - Автоматическая очистка при ошибках

### Изменено
- **Рефакторинг main.py**
  - Логика обработки файлов вынесена в FileProcessor
  - Упрощен код эндпоинта `/upload`
  - Улучшена обработка ошибок
  - Более чистый и читаемый код

### Улучшения архитектуры
- **Модульность**: Логика обработки файлов теперь в отдельном сервисе
- **Тестируемость**: FileProcessor можно тестировать независимо
- **Расширяемость**: Легко добавить новые форматы файлов
- **Надежность**: Автоматическая очистка файлов при ошибках

### Тестирование
- Добавлен тестовый скрипт `test_file_processor.py`
- Проверка всех основных функций FileProcessor
- Валидация обработки ошибок

### Документация
- Обновлен README.md с новой архитектурой
- Добавлено описание FileProcessor сервиса
- Обновлены диаграммы архитектуры

## [1.0.0] - 2024-01-XX

### Первоначальная версия
- Базовая RAG система с FastAPI
- Поддержка PDF, DOCX, TXT файлов
- Интеграция с OpenAI и OpenRouter
- ChromaDB для хранения эмбедингов
- Базовая аутентификация 