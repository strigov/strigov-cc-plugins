# strigov-cc-plugins

Маркетплейс плагинов для **Claude Code** (cc — Claude Code edition).

## Установка маркетплейса

В Claude Code:

```
/plugin marketplace add strigov/strigov-cc-plugins
```

После добавления — просмотр и установка плагинов:

```
/plugin
```

## Обновление

Когда в маркетплейсе или плагине вышла новая версия:

```
/plugin marketplace update strigov-cc-plugins
/plugin update <plugin-name>@strigov-cc-plugins
```

Первая команда тянет свежий `marketplace.json` (актуализирует список и заявленные версии). Вторая — переустанавливает сам плагин из его репо.

Обновить всё установленное из всех маркетплейсов сразу:

```
/plugin marketplace update --all
/plugin update --all
```

## Плагины

### vassal-litigator-cc

[strigov/vassal-litigator-cc](https://github.com/strigov/vassal-litigator-cc) — Вассал-Литигатор, плагин для ведения судебных дел: intake материалов клиента, хронология, правовой анализ с Codex CLI.

```
/plugin install vassal-litigator-cc@strigov-cc-plugins
```

Форк [vassal-litigator](https://github.com/strigov/vassal-litigator) v0.5.4 (Cowork edition), адаптированный под нативную установку в Claude Code.

### superpowers-strigov-ver

[strigov/superpowers-strigov-ver](https://github.com/strigov/superpowers-strigov-ver) — структурированный multi-model workflow для Claude Code: Sonnet оркестрирует, Codex Sol пишет план, Opus ревьюит план и код, Codex Luna реализует, а Sol выполняет независимый control review. Включает verification gate, review packages, финальное ревью всей ветки и возобновление Codex по адресу треда.

```
/plugin install superpowers-strigov-ver@strigov-cc-plugins
```

Текущая опубликованная версия в marketplace: `0.6.0`.
