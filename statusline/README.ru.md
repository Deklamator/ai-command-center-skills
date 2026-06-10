# Statusline

[![en](https://img.shields.io/badge/lang-en-blue.svg)](README.md)
[![ru](https://img.shields.io/badge/lang-ru-green.svg)](README.ru.md)

Кастомный статусбар для Claude Code: контекст в токенах, лимиты подписки и стоимость сессии. Ноль сетевых запросов — всё берётся из JSON, который Claude Code сам передаёт скрипту.

## Превью

```
project • Fable 5 • 260k/1M • [ h 27% 46m │ W 36% ] • $3.43
```

- **project** — Имя текущей директории (git-ветка показывается только если это не `main`/`master`)
- **Fable 5** — Текущая модель (приглушённо)
- **260k/1M** — Контекстное окно: занято токенов / размер окна. Главный элемент — всегда жирный
- **[ h 27% 46m │ W 36% ]** — Блок лимитов: 5-часовое окно (потрачено % + время до сброса) и недельное окно
- **$3.43** — Стоимость сессии (приглушённо, в конце)

## Визуальная иерархия

Токены — самый громкий элемент; всё спокойное уходит в тусклый серый. Зелёного нет нигде — низкое использование не информация.

| Потрачено | Токены | Лимиты |
|---|---|---|
| < 50% | жирный белый | тускло-серый |
| 50–80% | жирный жёлтый | жёлтый |
| > 80% | жирный красный | жирный красный |

## Требования

- `jq` — обработка JSON
- Claude Code **v2.1.80+** — `rate_limits` в payload статусбара
- Подписка Claude Pro/Max — `rate_limits` приходит только подписчикам

```bash
brew install jq
```

## Установка

1. Скопируй скрипт в конфиг Claude:

```bash
cp statusline.sh ~/.claude/
chmod +x ~/.claude/statusline.sh
```

2. Добавь в `~/.claude/settings.json`:

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh"
  }
}
```

3. Перезапусти Claude Code.

## Как это работает

Скрипт получает JSON от Claude Code через stdin и читает всё из него:

1. `context_window.total_input_tokens` / `context_window_size` → счётчик токенов (`260k/1M`)
2. `rate_limits.five_hour` / `rate_limits.seven_day` → потрачено % и отсчёт до сброса. Поле появилось в v2.1.80; приходит после первого ответа API в сессии, поэтому последнее увиденное значение кэшируется в `/tmp/claude-rate-limits.json` как fallback
3. `cost.total_cost_usd` → стоимость сессии

Никаких OAuth-токенов, опросов API и python — только `jq` и bash.
