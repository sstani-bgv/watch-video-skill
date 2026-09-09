# Промпт для установки

Скопируйте сообщение ниже в Codex или Claude Code:

```text
Установи глобально навык Watch из публичного репозитория https://github.com/sstani-bgv/watch-video-skill для Codex и Claude Code. После установки полностью прочитай skills/watch/SETUP.md из этого репозитория и проведи настройку по-русски, задавая вопросы по одному. Сначала проверь и установи ffmpeg и yt-dlp. Затем спроси, какой способ распознавания речи я выбираю: Groq, локальный whisper.cpp, только готовые субтитры или OpenAI. Если нужен ключ, попроси его только в момент записи в защищённый ~/.config/watch/.env, не повторяй ключ и не выводи его в логах. Потом спроси режим анализа видео. После этого предложи подключить официальный Apify MCP через OAuth для резервного получения роликов из Instagram и TikTok. Не запускай платные Apify Actors без моего явного согласия. Подключи vidIQ через адаптер текущего инструмента: в Codex используй штатный vidIQ App (app-69dd11f3e50c8191b1ca48d03cf7e2ad@openai-curated-remote), а в Claude Code — коннектор vidIQ из Claude.ai с MCP tools вида mcp__claude_ai_Vidiq__vidiq_*. Не устанавливай Codex App командой Claude Code и не придумывай MCP URL. Дай мне подтвердить OAuth в интерфейсе, затем проверь наличие реальных vidIQ tools. В конце покажи результат без секретов.
```

Для самостоятельной установки навыка без мастера:

```bash
npx skills add sstani-bgv/watch-video-skill -g -y -a codex -a claude-code
```
