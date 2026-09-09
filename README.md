# Watch Video Skill

Навык для Codex, Claude Code и других Agent Skills-совместимых агентов: получает видео по ссылке или из локального файла, извлекает речь и визуальные кадры, после чего отвечает по фактическому содержанию ролика.

Основа проекта — [claude-video](https://github.com/bradautomates/claude-video) Брэдли Бонанно. В этом форке добавлены экономные контакт-листы, подробный анализ первых 15 секунд, локальный whisper.cpp и русская установка с Apify MCP.

## Быстрая установка

Скопируйте [готовый промпт установки](INSTALL_PROMPT.md) в Codex или Claude Code. Агент установит навык глобально, задаст вопросы по-русски и проверит настройку.

Или установите навык одной командой:

```bash
npx skills add sstani-bgv/watch-video-skill -g -y -a codex -a claude-code
```

После этого попросите агента прочитать `skills/watch/SETUP.md` и выполнить настройку. Полная инструкция: [SETUP.md](skills/watch/SETUP.md).

Для Claude Code также доступна установка как плагина:

```text
/plugin marketplace add sstani-bgv/watch-video-skill
/plugin install watch@watch-video-skill
```

## Что умеет

- YouTube, Vimeo, X и другие источники, которые поддерживает `yt-dlp`.
- Локальные `.mp4`, `.mov`, `.mkv`, `.webm` и другие видео.
- Instagram и TikTok через обычный `yt-dlp`, а при его отказе — через подключаемый [Apify MCP](https://docs.apify.com/integrations/mcp).
- Готовые субтитры, облачное распознавание Groq/OpenAI или полностью локальный whisper.cpp.
- Экономный просмотр множества кадров через хронологические контакт-листы 3×2.
- Отдельный `--hook`: первые 15 секунд с частотой 15 кадров/с, упакованные в контакт-листы 5×9 для анализа монтажа и удержания.
- Режимы `transcript`, `efficient`, `balanced` и `token-burner`, фокус по диапазону времени и точечные кадры по таймкодам.

## Примеры

```text
/watch https://youtu.be/VIDEO_ID кратко перескажи и укажи ключевые таймкоды
/watch video.mp4 где ломается интерфейс?
/watch https://www.tiktok.com/@user/video/123 разбери хук первых 15 секунд
```

Прямой запуск реализации:

```bash
python3 skills/watch/scripts/watch.py video.mp4
python3 skills/watch/scripts/watch.py video.mp4 --detail efficient
python3 skills/watch/scripts/watch.py video.mp4 --hook
python3 skills/watch/scripts/watch.py video.mp4 --start 00:45 --end 01:10
python3 skills/watch/scripts/watch.py video.mp4 --whisper local
```

## Транскрипция

| Вариант | Что требуется | Особенности |
|---|---|---|
| Готовые субтитры | Ничего | Бесплатно; зависит от источника |
| Groq | `GROQ_API_KEY` | Рекомендуемый облачный вариант; бесплатный тариф имеет лимиты |
| Локальный | `whisper-cli` + GGML-модель | Аудио остаётся на компьютере; требует ресурсов и места |
| OpenAI | `OPENAI_API_KEY` | Запасной облачный вариант |

Секреты хранятся только в `~/.config/watch/.env` с правами `0600`. Не добавляйте этот файл в репозиторий.

## Как устроен анализ

1. `yt-dlp` сначала ищет субтитры и метаданные.
2. При необходимости скачивается видео или только аудио.
3. `ffmpeg` выбирает ключевые кадры или смены сцен.
4. Кадры дедуплицируются и собираются в компактные контакт-листы.
5. Если субтитров нет, используется выбранный Whisper.
6. Агент читает визуальный ряд вместе с транскриптом и отвечает с таймкодами.

Apify используется только как резервный путь для Instagram/TikTok. OAuth предпочтительнее токена. Запуск Actors может расходовать лимиты аккаунта, поэтому платный Actor нельзя запускать без явного согласия пользователя.

## Разработка и проверка

```bash
python3 -m pytest -q
bash skills/watch/scripts/build-skill.sh
```

Релизный тег `vX.Y.Z` автоматически собирает `watch.skill` для загрузки в веб-интерфейс Claude.

## Лицензия и авторство

MIT. Исходное уведомление Bradley Bonanno сохранено в [LICENSE](LICENSE), подробности — в [AUTHORS.md](AUTHORS.md).
