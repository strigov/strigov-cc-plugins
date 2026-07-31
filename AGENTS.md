# Инструкции для агентов

Этот репозиторий — **каталог Claude Code marketplace**, а не исходный код перечисленных плагинов. Главный публикуемый артефакт здесь — `.claude-plugin/marketplace.json`.

## Источники истины

- Текущее опубликованное состояние marketplace хранится в `.claude-plugin/marketplace.json`.
- Целевая версия плагина берётся из `.claude-plugin/plugin.json` в **удалённой default-ветке исходного репозитория**, указанного в поле `source.url`.
- `README.md`, этот файл, `CLAUDE.md`, локальный plugin cache, Git-теги и текущая версия каталога не являются источником целевой версии.
- Если пользователь явно назвал целевую версию, она должна совпадать с удалённым `plugin.json`. При расхождении остановитесь и покажите его пользователю.

Никогда не угадывайте «последнюю версию» по истории этого marketplace-репозитория. Сначала прочитайте manifest исходного плагина на GitHub.

## Формат `source` в записях каталога

Все плагины каталога объявляются git-источником с явным HTTPS-URL:

```json
"source": {
  "source": "url",
  "url": "https://github.com/strigov/<plugin-repo>.git"
}
```

Не переводите записи обратно на форму `{"source": "github", "repo": "owner/repo"}`. Для неё Claude Code по умолчанию строит SSH-URL (`git@github.com:owner/repo.git`) и переключается на HTTPS только при выставленной `CLAUDE_CODE_PLUGIN_PREFER_HTTPS` (или `CLAUDE_CODE_REMOTE`); отката с SSH на HTTPS нет, поэтому на машинах без SSH-ключа к GitHub установка падает с `Permission denied (publickey)`. Тип `url` передаёт URL в git как есть.

Имя типа — именно `url`; `"source": "git"` невалидно для plugin source и приводит к ошибке «source type your Claude Code version does not support». Поддерживаемые типы plugin source: относительный путь-строка, `github`, `url`, `git-subdir`, `npm`.

## Обновление или публикация плагина

Когда пользователь просит «обновить плагин в marketplace», «опубликовать новую версию» или аналогичное, выполните следующий порядок:

1. Найдите запись плагина в `.claude-plugin/marketplace.json` и прочитайте `source.url`.
2. Получите manifest из удалённой default-ветки исходного репозитория. Предпочтительный шаблон:

   ```bash
   url="$(jq -r '.plugins[] | select(.name == "<plugin-name>") | .source.url' .claude-plugin/marketplace.json)"
   repo="$(printf '%s\n' "$url" | sed -E 's#^https://github\.com/##; s#\.git$##')"
   branch="$(gh api "repos/$repo" --jq '.default_branch')"
   gh api "repos/$repo/contents/.claude-plugin/plugin.json?ref=$branch" \
     --jq '.content' | base64 --decode | jq '{name, version, description}'
   ```

3. Сравните:
   - текущую опубликованную версию из `marketplace.json`;
   - целевую версию из удалённого `plugin.json`.
4. Если версии уже совпадают, сообщите, что обновление каталога не требуется. Не создавайте пустой commit.
5. Если исходный manifest новее, обновите **обязательно**:
   - `version` соответствующей записи в `.claude-plugin/marketplace.json`;
   - `description` этой записи по актуальному удалённому `plugin.json`;
   - опубликованную версию и краткое описание плагина в `README.md`.
6. Не меняйте `AGENTS.md` и `CLAUDE.md` при обычном version bump. В них намеренно нет текущих номеров версий.
7. Проверьте результат:

   ```bash
   jq -e . .claude-plugin/marketplace.json >/dev/null
   claude plugin validate .
   git diff --check
   git diff -- .claude-plugin/marketplace.json README.md
   ```

8. Перед commit убедитесь, что изменился сам `marketplace.json`, а не только документация.
9. Commit и push выполняйте только если пользователь их явно запросил.

## Запрещённые ошибочные действия

- Не используйте текущую версию marketplace как целевую версию обновления.
- Не переносите номер версии из старого `README.md`, `AGENTS.md` или `CLAUDE.md`.
- Не обновляйте только `README.md` и не называйте это публикацией плагина.
- Не копируйте README в `AGENTS.md` или `CLAUDE.md`.
- Не редактируйте исходный репозиторий плагина из этого checkout.
- Не удаляйте и не переставляйте другие записи каталога при bump одного плагина.

## Критерий завершения

Обновление marketplace готово только когда версия и description в `.claude-plugin/marketplace.json` совпадают с удалённым manifest исходного плагина, README отражает опубликованную версию, JSON и marketplace проходят валидацию, а diff не содержит посторонних изменений.
