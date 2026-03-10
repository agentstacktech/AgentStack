# AgentStack Plugins — Руководство по авторству Skills

**Version:** 0.1  
**Date:** 2026-02-24  
**Validation:** Каждый раздел проверен по [AGENTSTACK_PLUGIN_PHILOSOPHY.md](AGENTSTACK_PLUGIN_PHILOSOPHY.md) (раздел «Skills: валидация по PHILOSOPHY_INDEX») и [philosophy/PHILOSOPHY_INDEX.md](../philosophy/PHILOSOPHY_INDEX.md).

---

## Назначение

Единые правила и качественные примеры для skills в плагинах Cursor и Claude. Skills учат агента, когда и как использовать экосистему AgentStack (8DNA, Projects, Rules Engine, **Assets**, **RBAC**, **Buffs**, **Payments**, **Auth**, MCP tools). **8DNA** в skills описывается публично как **JSON+** (структурированный JSON с встроенной поддержкой вариантов, например A/B тесты), с явным указанием хранилища данных (key-value API, project API); см. **docs/architecture/DNA_KEY_VALUE_API.md**. Детали архитектуры не раскрывать в публичных skills до патентов. Отдельные skills: **Assets** — ассеты (торговля, игры, инвентарь); **RBAC** — роли и права; **Buffs** — триалы, подписки, эффекты; **Payments** — платежи и кошельки; **Auth** — логин, регистрация, профиль.

---

## Обязательная структура SKILL.md

1. **Frontmatter (YAML):** `name`, `description` — обязательны.
2. **When to use** — когда агент должен применить этот skill (триггеры, типичные запросы).
3. **Capabilities** и/или **Instructions** — что умеет skill и пошаговые указания.
4. **Examples** — конкретные примеры в формате «запрос пользователя → инструмент/действие».
5. **References** — ссылки на MCP_SERVER_CAPABILITIES, MCP_QUICKSTART, доки 8DNA/философии.

Дополнительно допустимы: **Tips**, **Flow**, **Core concepts** — по необходимости, без раздувания.

---

## Требования к description

- **Третье лицо:** описание вставляется в системный контекст; не «I can…» / «You can…», а «Does X», «Use when…».
- **WHAT + WHEN:** что делает skill и при каких запросах применять.
- **Триггерные слова:** включить ключевые термины (имена MCP-групп, домен: projects, 8DNA, rules, API key, и т.д.).

### Примеры формулировок

**Хорошо:**

```yaml
description: Creates and manages AgentStack projects and API keys via MCP (projects.*). Use when the user wants to create a project, get an API key, list or inspect projects, get stats, manage users or settings, or attach an anonymous project to an account.
```

Почему: третье лицо, WHAT (creates/manages projects and API keys via MCP), WHEN (wants to create…, list…, get stats…), триггеры (projects, API key, stats, attach).

**Плохо:**

```yaml
description: Helps with AgentStack.
```

Почему: нет WHEN, нет триггеров, размытый WHAT.

---

## Правила ссылок

- **Репо (docs, philosophy):** в тексте skill использовать нейтральные отсылки: «See **MCP_SERVER_CAPABILITIES** in repo docs», «See **8DNA_HIERARCHY_EVOLUTION_CAPABILITIES**, **PARADIGM_SHIFT_BIOLOGICAL_COMPUTING** in repo philosophy». При открытии из репо пути: `docs/MCP_SERVER_CAPABILITIES.md`, `philosophy/…`.
- **Плагин:** «See **MCP_QUICKSTART.md** in plugin root» — в корне cursor-plugin или claude-plugin. Не использовать абсолютные пути; Cursor/Claude подставляют контекст плагина.
- **Elegant Minimalism:** не копировать большие таблицы MCP из docs в skill; краткая таблица в skill + ссылка на MCP_SERVER_CAPABILITIES для полного списка.

---

## Прогрессивное раскрытие (reference.md / examples.md)

- **SKILL.md** остаётся основным носителем; целевой объём до ~500 строк.
- **reference.md** — выносить детальные справочники только если таблица инструментов или параметров сильно растёт и дублирует MCP_SERVER_CAPABILITIES. Иначе предпочитать ссылку на док.
- **examples.md** — выносить расширенные сценарии только если в SKILL накапливается много примеров (например, >10); в SKILL оставить 2–3 ключевых.
- Ссылки из SKILL — один уровень: «See [reference.md](reference.md)» или «See [examples.md](examples.md)». Не строить цепочку reference → другие файлы.

---

## Золотой пример: agentstack-projects

Эталон структуры и тона для MCP-ориентированного skill.

| Элемент | Как сделано | Почему так |
| -------- | ------------- | ----------- |
| **description** | Third person, WHAT (creates/manages projects and API keys via MCP), WHEN (create project, get API key, list, stats…), триггеры (project, API key, stats, attach) | Агент надёжно выбирает skill по запросу пользователя. |
| **When to use** | Конкретные фразы пользователя и сценарии (anonymous, attach to account) | Чёткие триггеры без размытых формулировок. |
| **Capabilities** | Таблица MCP tools с кратким назначением | Быстрый скан; детали — в MCP_SERVER_CAPABILITIES. |
| **Instructions** | Нумерованные шаги: first-time, list/inspect, attach, API keys | Законченный модуль (Time-Decomposition-Completion); каждый шаг с выходом. |
| **Examples** | Формат «"User says…" → `tool` with params» | Конкретный ввод → действие; воспроизводимо. |
| **References** | MCP_SERVER_CAPABILITIES, MCP_QUICKSTART (plugin) | Ссылки вместо копипаста (Elegant Minimalism). |
| **Tips** | Короткие ограничения (anonymous tier, keys shown once) | Минимум текста, только необходимое. |

Для плагина Claude в том же skill заменять «Cursor» на «Claude Code» и проверять, что ссылки ведут на артефакты claude-plugin (MCP_QUICKSTART в корне плагина).

---

## Валидация перед коммитом

Перед внесением изменений в skill пройти чеклист из [AGENTSTACK_PLUGIN_PHILOSOPHY.md](AGENTSTACK_PLUGIN_PHILOSOPHY.md) (раздел «Skills: валидация по PHILOSOPHY_INDEX»):

- Creation over Conflict: создаём ценность, не боремся с альтернативами.
- Time-Decomposition-Completion: законченный модуль, нет полуготовых инструкций.
- Decomposition: один skill — один домен.
- Elegant Minimalism: минимум текста, ссылки вместо дублирования, SKILL.md до ~500 строк.
- 8DNA / Time: skill 8DNA соответствует официальной архитектуре; при изменениях — версии и CHANGELOG.

См. также [SKILLS_AUDIT.md](SKILLS_AUDIT.md) для текущего состояния и рекомендуемых правок.
