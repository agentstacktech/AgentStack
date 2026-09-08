# AgentStack в браузере: ChatGPT и Gemini

**Для кого:** вы хотите управлять проектами AgentStack прямо из окна чата GPT или Gemini.  
**Endpoint:** `https://agentstack.tech/mcp`

---

## Шаг 0 — один раз получить API-ключ

В терминале (или через любой HTTP-клиент):

```bash
curl -s -X POST https://agentstack.tech/mcp/tools/projects.create_project_anonymous \
  -H "Content-Type: application/json" \
  -d "{\"params\": {\"name\": \"Мой AI проект\"}}"
```

В ответе на верхнем уровне будут **`user_api_key`** / **`project_api_key`** и нейтральный объект **`bootstrap`** (какие заголовки использовать — без дублирования секретов в тексте инструкций).

**Настройте один раз** в параметрах GPT/Gemini или менеджере паролей: вставьте ключ в поле API Key / Token (`X-API-Key`). Не просите модель «запомнить» ключ.

Уже есть аккаунт? Создайте ключ на [agentstack.tech/me/keys](https://agentstack.tech/me/keys).

**Установка MCP (все клиенты):** [MCP_SETUP_QUICKSTART.md](MCP_SETUP_QUICKSTART.md) · EN: [MCP_BROWSER_QUICKSTART.md](MCP_BROWSER_QUICKSTART.md)

---

## ChatGPT в браузере

### Способ A — Custom GPT (самый простой, ~10 минут)

Подходит: ChatGPT Plus и выше. Работает в обычном чате браузера.

1. Откройте [chatgpt.com](https://chatgpt.com) → **Explore GPTs** → **Create**.
2. **Configure** → **Actions** → **Import from URL** или вставьте схему из репо:  
   `https://github.com/agentstacktech/gpt-plugin/blob/main/openapi/agentstack-mcp.yaml`
3. **Authentication** → **API Key**:
   - Header: `X-API-Key`
   - Value: ваш ключ из шага 0
4. **Instructions** — скопируйте блок из [GPT_INSTRUCTIONS.md](https://github.com/agentstacktech/gpt-plugin/blob/main/GPT_INSTRUCTIONS.md).
5. **Save** → выберите «Only me» или «Anyone with link».

**Готово.** Открываете свой GPT и пишете команды обычным языком (см. таблицу ниже).

---

### Способ B — MCP Connector (Developer Mode)

Подходит: ChatGPT Plus / Pro / Business (нужен Developer Mode).

1. [chatgpt.com](https://chatgpt.com) → профиль → **Settings**.
2. **Apps & Connectors** → **Advanced** → включите **Developer mode**.
3. **Create** (или Add custom connector):
   - **Name:** AgentStack
   - **Description:** Управление проектами AgentStack
   - **URL:** `https://agentstack.tech/mcp`
   - **Authentication:** Token → вставьте ваш API-ключ  
     (AgentStack принимает его как Bearer; альтернатива — OAuth через AgentStack)
4. Сохраните. Должен появиться **1 tool**: `agentstack.execute`.

**В чате:** нажмите **+** рядом с полем ввода → **More** → выберите AgentStack.

---

## Gemini в браузере

### Способ — Connected Apps (только Gemini Spark)

> **Ограничение Google:** custom MCP в веб-Gemini доступен **только в Gemini Spark** (США, 18+, личный Google-аккаунт, включён Keep Activity). Обычный чат Gemini **без Spark** custom MCP не поддерживает.

1. [gemini.google.com](https://gemini.google.com) → **Settings & help** → **Connected Apps**.  
   (Если нет пункта — **Personal Intelligence** → **Connected Apps**.)
2. Включите **Keep Activity** (Settings → Activity).
3. Блок **Custom apps for Spark** → **Add a custom app**.
4. **MCP server URL:** `https://agentstack.tech/mcp`
5. Если OAuth не проходит автоматически → **Advanced features** → credentials (OAuth client от AgentStack) или пройдите OAuth AgentStack.
6. **Next** → подтвердите.

**В чате Spark:** `@` → выберите ваш custom app → напишите команду.

**Нет Spark?** Используйте ChatGPT (способ A выше) или [Gemini CLI](https://github.com/agentstacktech/gemini-plugin/blob/main/GEMINI_CLI_QUICKSTART.md) в терминале — это не браузер, но тот же MCP.

---

## Команды для окна чата (копируйте)

После настройки пишите **обычным языком** — AI сам вызовет AgentStack.

### Проекты

| Что хотите | Пример фразы в чате |
|------------|---------------------|
| Список проектов | `Покажи все мои проекты AgentStack` |
| Создать проект | `Создай новый проект AgentStack с названием "Test App"` |
| Статистика | `Покажи статистику проекта 1025` |
| Настройки | `Покажи настройки проекта 1025` |
| Активность | `Покажи последнюю активность проекта 1025` |

### API-ключи

| Что хотите | Пример фразы |
|------------|--------------|
| Список ключей | `Покажи API ключи проекта 1025` |
| Создать ключ | `Создай API ключ "backend" для проекта 1025` |

### Данные (8DNA)

| Что хотите | Пример фразы |
|------------|--------------|
| Прочитать config | `Прочитай project.data.config из проекта 1025` |
| Записать config | `Сохрани в project.data.theme значение "dark" для проекта 1025` |

### Правила и автоматизация

| Что хотите | Пример фразы |
|------------|--------------|
| Список правил | `Покажи все logic rules проекта 1025` |
| Создать правило | `Создай правило: когда пользователь регистрируется — дать trial на 7 дней` |

### Платежи и buffs

| Что хотите | Пример фразы |
|------------|--------------|
| Баланс | `Покажи баланс кошелька проекта 1025` |
| Trial | `Дай пользователю trial на 7 дней в проекте 1025` |

### Общие

| Что хотите | Пример фразы |
|------------|--------------|
| Что умеет AgentStack | `Какие действия AgentStack доступны для проектов?` |
| Профиль | `Покажи мой профиль AgentStack` |

---

## Если что-то не работает

| Проблема | Решение |
|----------|---------|
| 401 / unauthorized | Проверьте API-ключ в настройках GPT/Gemini |
| Gemini не видит custom app | Нужен Gemini Spark (США); без Spark — используйте ChatGPT |
| ChatGPT «Tool scan failed» | Повторите сохранение 2–3 раза; URL строго `https://agentstack.tech/mcp` |
| AI «выдумывает» проекты | Напишите: «Вызови AgentStack API, не придумывай данные» |
| Нет прав на действие | У ключа узкие service_caps — создайте ключ с нужными правами |

Проверка сервера (без локального clone Core):
```bash
curl -sS -H "X-API-Key: $AGENTSTACK_API_KEY" https://agentstack.tech/mcp/actions | head
```

---

## Сравнение: что выбрать

| | ChatGPT Custom GPT | ChatGPT MCP Connector | Gemini Spark |
|--|-------------------|----------------------|--------------|
| Сложность | ★★☆ | ★★★ | ★★★ |
| Нужен Plus | Да | Да | Spark access |
| Обычный чат браузера | ✅ | ✅ | только Spark |
| Настройка один раз | ✅ | ✅ | ✅ |

**Рекомендация:** начните с **ChatGPT Custom GPT (способ A)** — быстрее всего получить рабочий чат с командами.
