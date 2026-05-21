# Lead Scoring — Claude Skill для приоритизации входящих лидов

> Workshop из сессии AI Camp Almaty 2026 · Глава «РОП» · «Кому звонить первым»

## Что внутри

Claude Skill, который **ранжирует лиды 0–100** по вероятности конверсии за 14 дней и выдаёт два артефакта:

1. **HTML-отчёт** — TOP-10 с opener-фразами, ранжированная таблица, сегменты A/B/C, инсайт.
2. **CSV** со скорами по каждому лиду — для загрузки обратно в CRM или дальнейшей автоматизации.

Работает с любой CRM. Адаптивный mapping колонок — скилл сам опознаёт `crm_notes`, `created_at`, `position` и т.д. независимо от того, как они названы у тебя.

## Проблема, которую решает

В воронке тысяча лидов, на всех ресурсов нет. Два сценария по умолчанию — оба плохие:

- **«Звоним всем подряд»** → менеджеры выгорают, конверсия размазана, А-лиды остывают.
- **«Менеджер выбирает интуитивно»** → пристрастие к удобным сегментам, провал по новым категориям, нет воспроизводимости.

Скилл даёт **третий вариант**: AI ранжирует по 4 группам сигналов (поведение, fit, recency, crm_notes) и обосновывает каждое решение. Менеджер видит «вебинар 45 мин + рассрочка + ≤3 дней» — понимает, почему этот лид сверху.

### Важно: работает с любым уровнем CRM-зрелости

В реальности в воронке у тебя **два типа лидов одновременно**:

- **Rich-лиды:** пришли из каналов с tracking — email-метрика, клики, скачивания, вебинары.
- **Minimal-лиды:** пришли через форму или входящий звонок — поведенческих сигналов нет, только `crm_notes`.

Скилл **адаптивно** меняет веса: если behavior пустой, главным становится комментарий менеджера. Сильные фразы вроде «готова оплатить сегодня», «нужен счёт на ТОО», «корпоративно на 4 человек» — выталкивают лида в A-категорию даже без tracking.

Поэтому **методология применима в любой компании** — не нужно сначала ставить Mindbox или вебинарную платформу. Базовая CRM достаточна.

---

## Установка

### Вариант 1: Claude Code (CLI / VS Code / JetBrains)

```bash
# Склонируй репо
git clone https://github.com/dkoraitest/ai-camp-lead-scoring.git ~/.claude/skills/lead-scoring

# Перезапусти Claude Code (или перезагрузи окно IDE)
# Скилл автоматически появится в списке доступных
```

Проверка: в Claude Code напиши «оцени лидов» — скилл должен подхватиться.

### Вариант 2: Claude.ai (web / desktop)

1. Скачай этот репо как ZIP: `Code → Download ZIP` на GitHub
2. Распакуй
3. В Claude.ai: `Settings → Capabilities → Skills → Upload skill`
4. Выбери папку `ai-camp-lead-scoring`
5. Готово — теперь Claude в любом чате может вызвать скилл

### Вариант 3: Без установки, через ChatGPT

Если у тебя нет доступа к Claude.ai, открой `prompt.md` в этом репо, скопируй его целиком в ChatGPT, прикрепи `.claude/skills/lead-scoring/sample-leads.csv` (или свой) — получишь тот же результат, но без HTML-вёрстки.

---

## Использование

### Запуск

Просто напиши в Claude один из триггеров:
- «оцени мои лиды»
- «lead scoring»
- «приоритизируй вот эту выгрузку» (и прикрепи CSV)
- «скорь лидов на тестовом наборе»

Скилл сам уточнит, есть ли у тебя CSV-выгрузка, или использовать тестовый набор из 50 лидов (`.claude/skills/lead-scoring/sample-leads.csv`).

### Что произойдёт

1. Скилл попросит CSV или предложит использовать тестовый.
2. Прочитает первые строки, опознает колонки, покажет mapping одной строкой.
3. Скорит все лиды по методологии (4 группы сигналов, адаптивные веса).
4. Создаст **HTML-отчёт** + **CSV** в `active/lead-scoring/` (или в `./lead-scoring-output/`), либо как artifact в Claude.ai.
5. Покажет короткое summary в чате и предложит follow-up — поставить на cron, адаптировать под B2B, посмотреть эталонный прогон.

### Acceptance test (демо-прогон)

Если ты на воркшопе AI Camp Almaty 2026 — запусти прогон на `.claude/skills/lead-scoring/sample-leads.csv`. Должен получить:
- 14 лидов в A-категории, 22 в B, 14 в C
- TOP-10 с openers (opening-фразами)
- HTML-отчёт открывается в браузере, выглядит как готовый артефакт
- Эталон лежит в [references/example-output.md](.claude/skills/lead-scoring/references/example-output.md)

Если получилось — подними руку. У 70% зала должно сработать с первого раза.

---

## Что забираешь домой

1. **Working skill** — ставится у тебя, работает с любой твоей CRM-выгрузкой.
2. **Методология** — 4 группы факторов с весами, адаптивный режим для minimal-данных, edge cases. См. [SKILL.md](SKILL.md).
3. **План на понедельник** — как выгрузить лидов из своей CRM (AmoCRM, Bitrix24, HubSpot, Salesforce, Pipedrive) и поставить процесс на автопилот. См. [references/monday-action.md](.claude/skills/lead-scoring/references/monday-action.md).
4. **Адаптация под B2B** — если у тебя длинный цикл и несколько ЛПР, та же логика, другие веса. См. [references/adapt-to-b2b.md](.claude/skills/lead-scoring/references/adapt-to-b2b.md).

---

## Файлы

| Файл | Что |
|---|---|
| [SKILL.md](SKILL.md) | Основной файл скилла — инструкции для Claude. |
| [sample-leads.csv](.claude/skills/lead-scoring/sample-leads.csv) | 50 синтетических лидов онлайн-школы для демо-прогона. |
| [assets/report-template.html](assets/report-template.html) | HTML-шаблон отчёта (self-contained, открывается офлайн). |
| [references/example-output.md](.claude/skills/lead-scoring/references/example-output.md) | Эталонный результат на `.claude/skills/lead-scoring/sample-leads.csv`. |
| [references/monday-action.md](.claude/skills/lead-scoring/references/monday-action.md) | Как подключить к своей CRM и поставить на cron. |
| [references/adapt-to-b2b.md](.claude/skills/lead-scoring/references/adapt-to-b2b.md) | Адаптация методологии под B2B. |
| [prompt.md](prompt.md) | Fallback-промпт для пользователей ChatGPT (без HTML-отчёта). |

---

## Другие воркшопы AI Camp Almaty 2026

Этот репо — 1 из 4. Остальные:

- **[ai-camp-enrich-and-pitch](https://github.com/dkoraitest/ai-camp-enrich-and-pitch)** — Claude Skill: outbound-разведка B2B-клиента + готовое opening-сообщение под ЛПР
- **[ai-camp-info-helper](https://github.com/dkoraitest/ai-camp-info-helper)** — Claude Skill с cron-запуском: утренние поводы касания по списку клиентов
- **[ai-camp-proposal-template](https://github.com/dkoraitest/ai-camp-proposal-template)** — Skill `make-proposal`: транскрипт звонка → SPSV → research → brainstorming → HTML КП

---

## Контакты

Telegram: @dkorobovtsev
Email: dkor.aitest@gmail.com

## License

MIT — см. [LICENSE](LICENSE).
