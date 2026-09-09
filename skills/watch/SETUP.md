# Установка Watch

Это руководство читает AI-агент при первой установке. Общайся с пользователем по-русски, задавай вопросы по одному и не проси лишних ключей.

## 1. Установить базовые зависимости

Сначала запусти:

```bash
python3 "${SKILL_DIR}/scripts/setup.py"
```

На macOS скрипт установит `ffmpeg` и `yt-dlp` через Homebrew. На Linux и Windows он покажет точные команды. После установки проверь:

```bash
python3 "${SKILL_DIR}/scripts/setup.py" --json
```

## 2. Спросить способ распознавания речи

Спроси пользователя:

> Как распознавать речь, если у видео нет готовых субтитров: через Groq в облаке, локально на компьютере или вообще без Whisper?

Предложи варианты в таком порядке:

1. **Groq (рекомендуется)** — быстро, есть бесплатный тариф с лимитами; нужен `GROQ_API_KEY`.
2. **Локальный Whisper** — аудио никуда не отправляется; установка и распознавание занимают место и ресурсы компьютера.
3. **Только готовые субтитры** — без ключа; ролики без субтитров анализируются только визуально.
4. **OpenAI Whisper** — запасной облачный вариант; нужен `OPENAI_API_KEY`, тарификация по правилам аккаунта OpenAI.

### Groq или OpenAI

Попроси пользователя создать ключ в [Groq Console](https://console.groq.com/keys) или [OpenAI API Keys](https://platform.openai.com/api-keys). Если пользователь присылает ключ, не повторяй его в ответе и не передавай в аргументах shell-команд. Запиши его напрямую в `~/.config/watch/.env`, установи права `0600` и добавь:

```dotenv
WATCH_WHISPER=groq
GROQ_API_KEY=<ключ>
```

или:

```dotenv
WATCH_WHISPER=openai
OPENAI_API_KEY=<ключ>
```

### Локальный Whisper на macOS

Объясни, что будет установлена реализация [whisper.cpp](https://github.com/ggml-org/whisper.cpp) и модель `small` (около 466 MB). После согласия:

```bash
brew install whisper-cpp
mkdir -p "$HOME/.config/watch/models"
curl -L --fail --output "$HOME/.config/watch/models/ggml-small.bin" "https://huggingface.co/ggerganov/whisper.cpp/resolve/main/ggml-small.bin"
```

Затем запиши в `~/.config/watch/.env`:

```dotenv
WATCH_WHISPER=local
WHISPER_LOCAL_MODEL=~/.config/watch/models/ggml-small.bin
```

На Linux/Windows следуй официальной инструкции whisper.cpp и укажи абсолютный путь к GGML-модели в `WHISPER_LOCAL_MODEL`.

### Только готовые субтитры

Оставь ключи пустыми и объясни, что можно использовать `--no-whisper`. Это полноценный выбор, не спрашивай о ключе повторно после завершения настройки.

## 3. Спросить режим анализа

Спроси один раз: `transcript`, `efficient`, `balanced` (рекомендуется) или `token-burner`. Запиши выбор как `WATCH_DETAIL=<режим>` в `~/.config/watch/.env`.

## 4. Предложить Apify MCP

Спроси:

> Подключить Apify MCP для Instagram и TikTok, когда обычный yt-dlp не может получить ролик?

Поясни, что OAuth не требует вставлять токен в конфиг, но запуск Actors может расходовать лимиты Apify. Без явного согласия пользователя не запускай платный Actor.

### Codex

```bash
codex mcp add apify --url "https://mcp.apify.com?tools=actors,docs"
codex mcp login apify
```

### Claude Code

```bash
claude mcp add --transport http --scope user apify "https://mcp.apify.com?tools=actors,docs"
claude mcp login apify
```

OAuth — рекомендуемый вариант. Если пользователь сознательно выбирает токен, попроси создать его в Apify Console и следуй [официальной инструкции Apify MCP](https://docs.apify.com/integrations/mcp); не коммить токен и не показывай его в логах.

При анализе Instagram/TikTok сначала пробуй обычный `/watch`. Обращайся к Apify только после ошибки прямого получения: найди подходящий Actor через MCP, получи URL медиа или локальный файл и передай его в Watch. Сообщи пользователю до запуска, если Actor платный.

## 5. Завершить и проверить

В `~/.config/watch/.env` установи `SETUP_COMPLETE=true`, права файла `0600`, затем выполни:

```bash
python3 "${SKILL_DIR}/scripts/setup.py" --json
python3 "${SKILL_DIR}/scripts/watch.py" --help
```

Не публикуй содержимое `.env`. В конце кратко перечисли выбранный режим транскрипции, режим анализа и статус Apify, но не значения секретов.
