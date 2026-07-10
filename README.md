# DFHome Store

Публичный **индекс магазина** для [DFHome](https://github.com/DFHome/core): список
Git-репозиториев с пакетами, которые можно установить из UI (вкладки «Всё»,
«Интеграции», «Виджеты»).

Ядро не хранит метаданные пакетов — только URL репозиториев. Имя, описание,
версия и тип пакета читаются из `manifest.json` каждого репозитория; версия для
установки определяется по **последнему SemVer-тегу** (`v1.0.0`, `v1.2.3`, …).

## Формат `repositories.json`

```json
{
  "packages": [
    { "repository": "https://github.com/DFHome/integration-demo" },
    { "repository": "https://github.com/DFHome/widget-metric" }
  ]
}
```

Ключ `packages` — основной. Для обратной совместимости ядро также принимает
устаревший ключ `integrations` с тем же форматом.

Каждая запись — только URL Git-репозитория. Дополнительные поля в индексе не
нужны: карточка в магазине строится из манифеста пакета.

## Типы пакетов

В `manifest.json` пакета указывается `package_type`:

| Значение | Назначение |
|----------|------------|
| `integration` (по умолчанию) | Источник устройств, комнат, плана и т.д. |
| `widget` | Шаблон виджета дашборда (и опционально API-роуты пакета) |

Виджетные пакеты технически устанавливаются тем же механизмом, что и интеграции
(`StoreClient.install` → `IntegrationManager.load`), но в UI отображаются на
вкладке «Виджеты».

## Текущий каталог

| Репозиторий | Тип | Описание |
|-------------|-----|----------|
| [integration-demo](https://github.com/DFHome/integration-demo) | интеграция | Виртуальные устройства, комнаты, план |
| [widget-metric](https://github.com/DFHome/widget-metric) | виджет | Числовой показатель с устройства |
| [widget-chart](https://github.com/DFHome/widget-chart) | виджет | График показателя за несколько часов |
| [widget-weather](https://github.com/DFHome/widget-weather) | виджет | Погода по городу |
| [widget-yandex-station](https://github.com/DFHome/widget-yandex-station) | виджет | Яндекс Станция (UI, интеграция Яндекс — позже) |

## Как добавить пакет в индекс

1. Опубликуйте репозиторий с `manifest.json` в корне и SemVer-тегами релизов.
2. Основная ветка репозитория — **`main`**.
3. Добавьте URL в `packages` в этом репозитории и сделайте commit в `main`.
4. Ядро подхватит изменения при следующем обновлении каталога магазина
   (по умолчанию индекс: `https://raw.githubusercontent.com/DFHome/store/main/repositories.json`).

Пользователи также могут установить любой Git-репозиторий вручную через UI,
минуя этот индекс.

## Требования к репозиторию пакета

- `manifest.json` в корне с уникальным `domain` и SemVer `version`.
- Точка входа `__init__.py` с `async def setup(ctx)` и `async def unload(ctx)`.
- Git-теги вида `v1.0.0` для версионирования в магазине.
- Зависимости Python — в поле `requirements` манифеста (ставятся через `uv`).

Подробнее: [документация интеграций](https://github.com/DFHome/core/blob/main/docs/INTEGRATIONS.md)
и [гайд по разработке](https://github.com/DFHome/core/blob/main/docs/INTEGRATION_DEVELOPMENT.md)
в репозитории ядра.

## Лицензия

Apache License 2.0 — см. [LICENSE](LICENSE).
