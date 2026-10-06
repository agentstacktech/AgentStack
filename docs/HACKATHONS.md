# Чеклист сдачи

Часы: America/Sao_Paulo (BRT). Соло, Alexander Vasilev, без CNPJ. Живой стенд, если поле формы его примет: https://agentstack.tech

## Hack Apertus

Дедлайн сдачи: 16 Oct 2026, 07:00 BRT (16 Oct 2026, 12:00 CEST). Продления нет. Соло или команда до 5. Сдача на сайте, не на Devpost: https://hackapertus.ch/online-hack/submissions

### Сегодня, 6 Oct

- [ ] Открыть в браузере https://hackapertus.devpost.com/rules и подтвердить, пускают ли резидента Brazil. Выгрузка HTML 6 Oct перечисляет Brazil в standard exceptions; допуск этим не закрыт. Справка: https://help.devpost.com/article/144-what-are-the-standard-exceptions-for-global-eligibility
- [ ] На тех же правилах подтвердить возраст: Aged 18+ (на обзоре ещё «above legal age of majority»). https://hackapertus.devpost.com/
- [ ] Пока допуск Brazil в браузере не закрыт — аккаунт, репозиторий и сборку Apertus не начинать.
- [ ] Открыть форму https://hackapertus.ch/online-hack/submissions и сверить поля с блоком «Что класть в форму». Поля сняты из JS бандла страницы 6 Oct, не из отрисованной формы.
- [ ] Открыть гайд в браузере: https://hackapertus.notion.site/getting-started-guide-onlinehack
- [ ] На правилах строка «Submissions to be done here (link will be shared soon)» расходится с гайдом (сдача на сайте). Сверить обе страницы в браузере: https://hackapertus.devpost.com/rules и https://hackapertus.ch/online-hack/submissions

### Если браузер покажет, что Brazil не пускает

- [ ] Apertus не регистрировать и не собирать. В этом файле остаются только Apart и Open Agent.

### Если браузер подтвердит допуск

- [ ] Завести Devpost и зарегистрироваться на https://hackapertus.devpost.com/ . Email тот же, что пойдёт в форму сдачи.
- [ ] Войти в Discord: https://discord.gg/hack-apertus
- [ ] Завести GitHub, если его нет. Репозиторий только из шаблона **Use this template**: https://github.com/HackApertus/project-template
- [ ] Остаться соло: команду не собирать. В форме team members — одно имя. Лимит организаторов: 1–5.
- [ ] Выбрать один пункт трека в форме. Список на 6 Oct: Track 1A - Red-teaming; Track 1B - Input Adaptation; Track 1B - Core Task Intelligence; Track 1B - Behavioural Steering; Track 2A - FHGR; Track 2A - OpenParlData; Track 2A - OST; Track 2A - UZH; Track 2A - ZHAW; Track 2B - Own Project.
- [ ] Запросить ключ CSCS тем же email, что Devpost: http://hackapertus.ch/online-hack/cscs/getapi
- [ ] Кредиты Hugging Face тем же email, если нужны (в форму сдачи не входят): http://hackapertus.ch/online-hack/hugging-face
- [ ] Заявку в Phoeniqs не слать: приём закрыт 4 Oct 2026, 20:30 CEST (гайд Resources).

### Завтра, 7 Oct

- [ ] Обслуживание Devpost: баннер на https://hackapertus.devpost.com/details/dates — 7 Oct 06:00 UTC = 7 Oct 03:00 BRT. Длительность не указана. Если регистрация Devpost не сделана 6 Oct, после падения сайта проверить, что логин снова открыт, и дорегистрироваться.
- [ ] Сессий Apertus на 7 Oct в расписании гайда нет. Q&A партнёров — 8 Oct, 12:00 CEST = 8 Oct, 07:00 BRT. На 7 Oct слот не бронировать. Расписание: страница Online sessions в гайде.
- [ ] Если допуск есть и трек выбран: открыть README этой папки шаблона и отметить внизу файла, каких файлов не хватает. 1A: `track_1a/README.md`. 1B: `track_1b/README.md`. 2A: `track_2a/README.md` плюс страница челленджа в гайде. 2B: `track_2b/README.md`.

### Что класть в форму

Один сабмит на проект. Если сдаёт напарник, второй раз не слать. Победителей ориентируют на 23 Oct 2026, 07:00 BRT (23 Oct, 12:00 CEST): https://hackapertus.devpost.com/details/dates

- [ ] First name
- [ ] Last name
- [ ] Email (used for Devpost sign-up) — тот же, что на Devpost
- [ ] GitHub username
- [ ] Team name (соло — своё имя)
- [ ] Team members, имена через запятую (соло — своё имя)
- [ ] Project name
- [ ] Swiss student team: Yes или No
- [ ] Model: `8B v1.5`, `70B v1.5` или `8B + 70B v1.5`. Трек должен оценивать или быть собран на Apertus 1.5. Другие open-weights только как поддержка, роль описать в отчёте.
- [ ] PDF в поле загрузки. Принимается только PDF. Лимит файла в коде формы: 101 MB. Лимит страниц — в строке трека ниже.
- [ ] GitHub repository URL, `https://...`
- [ ] Чекбокс Terms: https://hackapertus.ch/terms-and-conditions (п. 6: то, что собрано, open source)
- [ ] Hugging Face username — в форме без звёздочки
- [ ] Your message — в форме без звёздочки
- [ ] Сдать до 16 Oct 2026, 07:00 BRT

### Формат по треку

Общее для репозитория из шаблона: оставить папку своего трека как есть, остальные три папки удалить, из корня `make run` поднимает Docker на чистом checkout. Переменные: `LLM_NAME`, `LLM_BASE_URL`, `LLM_API_KEY`.

- [ ] **1A.** Папка `track_1a/`. Репозиторий PRIVATE. Коллаборатор https://github.com/judgeailights . PDF отчёта, max 6 pages, в репо файл `TeamName_Report.pdf` и тот же PDF в форму. До 5 файлов в `findings/` по `findings/findings.schema`, лицензия CDLA-Permissive-2.0, findings не публиковать до 1 Dec 2026. Поля dataset и demo video в форме для 1A нет. README: https://github.com/HackApertus/project-template/blob/main/track_1a/README.md
- [ ] **1B.** Папка `track_1b/`. Репозиторий PUBLIC. Один подпункт: Input Adaptation, Core Task Intelligence или Behavioural Steering. PDF max 6 pages, в репо `TeamName_Report.pdf`. Hugging Face dataset URL обязателен: аккаунт HF, клон https://huggingface.co/datasets/HackApertus/online_hack_template , датасет PUBLIC (evaluation, model responses, metadata). Demo video в форме для 1B нет. README: https://github.com/HackApertus/project-template/blob/main/track_1b/README.md
- [ ] **2A FHGR.** PDF «Documentation of approach, idea & strategy», max 6 pages. GitHub URL с runnable Docker и deployment instructions. Demo video URL, max 2 min, в валидации обязателен. Поля dataset в форме нет. Страница челленджа в гайде в выгрузке не открылась — подтвердить список файлов на https://hackapertus.notion.site/getting-started-guide-onlinehack
- [ ] **2A OpenParlData.** PDF Technical report, max 6 pages. GitHub URL: PDF-to-schema converter, промпты Apertus, validator, README с samples. Demo video и dataset в форме нет. Public или private репозитория README `track_2a` не пишет — подтвердить на странице челленджа в гайде.
- [ ] **2A OST.** PDF Technical report, max 6 pages, в подписи формы: token usage и inference time. GitHub URL: entailment CLI и cited passages. Demo video и dataset в форме нет. Public/private — подтвердить на странице челленджа в гайде.
- [ ] **2A UZH.** PDF «Short method description», max 2 pages. GitHub URL: reproducible code и model predictions; адаптеры в репо по подписи формы необязательны. Ярлык demo video: «optional, max. 3 min» и одновременно звёздочка; валидация бандла поле не требует. Открыть форму и подтвердить, обязателен ли URL. Dataset в форме нет. Состав файлов — подтвердить на странице челленджа в гайде.
- [ ] **2A ZHAW.** PDF Technical report, max 2 pages. GitHub URL: 5 slides и Results file. Demo video URL, max 3 min, в валидации обязателен. Dataset в форме нет. Состав файлов — подтвердить на странице челленджа в гайде.
- [ ] **2B.** Проект новый, начат внутри окна хакатона. Папка `track_2b/`, репозиторий PUBLIC. PDF max 6 pages, в репо `TeamName_Report.pdf`. Demo video URL, max 2 min, обязателен. Dataset URL в форме необязателен; если есть — тот же шаблон HF, что у 1B, доступ PUBLIC. `data/` не больше 100 MB. Одна архитектура из трёх: on-premise, air-gapped или sovereign Swiss cloud. README: https://github.com/HackApertus/project-template/blob/main/track_2b/README.md
- [ ] URL https://agentstack.tech в полях формы нет. Где трек требует demo video URL, сдавать URL видео, не URL сайта. Куда писать URL стенда — на форме 6 Oct поля нет; не вписывать его в чужое поле, пока браузер не покажет отдельную строку.

### Что не закрыто

- [ ] Допуск Brazil — только после просмотра правил в браузере.
- [ ] Пять страниц челленджей 2A внутри Notion: README `track_2a` отсылает в гайд, дочерние страницы в API-выгрузке не пришли.
- [ ] Demo video для UZH: подпись и валидация расходятся.
- [ ] Public или private для репозиториев 2A.
- [ ] Длительность обслуживания Devpost 7 Oct.
- [ ] Куда на форме класть URL живого стенда.

## Apart Research Sprint

Спринт 23–25 Oct 2026 (AI Collusion). Сдачу спринта на этой неделе не делать. Страница: https://apartresearch.com/sprints/ai-collusion-research-sprint-2026-10-23-to-2026-10-25

- [ ] Заполнить pre-hack survey, иначе по брифу только observer: без призов и без подбора команды. https://apartresearch.notion.site/2b0fcfd1de9d8080ac3ac4c41a78c8f8?pvs=105
- [ ] Форма в API называется Pre-Sprint Survey, описание: «Required for all participants.» Список вопросов выгрузка не отдала. Если в браузере форма пустая — не угадывать поля, открыть страницу ещё раз.
- [ ] Фразы «observer» на самой форме в выгрузке не было. После отправки сохранить подтверждение, что ответ принят.
- [ ] Войти в Discord из брифа: https://discord.gg/ssZDasNkSE
- [ ] Публичная страница даёт другой инвайт, `https://discord.gg/XswWBvugYs`, и про опрос молчит. Открыть оба и подтвердить, один это сервер или два.
- [ ] Зарегистрироваться на странице спринта как online. Хабы NYC и Hong Kong не выбирать. Соло на странице разрешено.
- [ ] Виджет сдачи проекта на публичной странице 6 Oct мог показывать закрытое окно. Это не шаг этой недели. Подтвердить только, что регистрация участника (не PDF) открыта. PDF, код и запись — не раньше спринта.

## Open Agent Hackathon 2026

Только то, что нужно до ближайшего воркшопа: 7 Oct 2026, 13:00–14:30 BRT, Zetaris. На странице академии слот указан как 9:00–10:30 AM PT. https://academy.genai.works/courses/open-agent-hackathon-2026/details

- [ ] На странице академии нажать Register now до 7 Oct, 13:00 BRT.
- [ ] Ссылку на эфир (Zoom или аналог) страница не публикует. До 13:00 получить её из письма или кабинета курса и проверить, что она открывается.
- [ ] Подтвердить на тех двух страницах, нужна ли отдельная регистрация ивента, чтобы войти на Zetaris. Если да — зарегистрироваться до 13:00 на https://hackathon.genai.works/event/open-agent-hackathon-2026 . Если нет — эту регистрацию до воркшопа не делать.
- [ ] Жёсткое закрытие регистрации ивента, если трек вообще в игре: 12 Oct 2026, 21:00 BRT (13 Oct 2026, 00:00 UTC). Счётчик «closes in N minutes» на странице с этой датой не смешивать. До 13:00 7 Oct этот дедлайн не наступает.
