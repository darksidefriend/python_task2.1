Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   # Отчет: Развертывание статического сайта на GitHub Pages с MkDocs и автоматизация CI/CD  **Цель работы:**    Создать статический сайт-документацию с помощью Python-генератора **MkDocs**, автоматизировать деплой на **GitHub Pages** через **GitHub Actions**, а также исследовать альтернативные инструменты и платформы: российские CDN, возможности GitVerse для CI/CD и различные способы деплоя статических сайтов.  ---  ## 1. Создание и локальная разработка сайта на MkDocs  ### 1.1. Подготовка окружения Python  - Установлен Python версии 3.12 (актуальная на момент выполнения).  - Проверена работа менеджера пакетов:    ```bash    pip --version   `

*   bashpip install --user virtualenv
    
*   bashvirtualenv venvsource venv/bin/activate # для Linux/macOS# venv\\Scripts\\activate # для Windows
    

### 1.2. Установка MkDocs и инициализация проекта

*   bashpip install mkdocs
    
*   bashmkdocs new my-static-sitecd my-static-site
    
*   textmy-static-site/├── mkdocs.yml # конфигурационный файл└── docs/ └── index.md # стартовая страница
    

### 1.3. Наполнение контентом и локальный запуск

*   В файл docs/index.md добавлен приветственный текст.
    
*   yamlsite\_name: Мой статический сайт на MkDocstheme: readthe docs
    
*   bashmkdocs serve
    
*   Сайт стал доступен по адресу http://127.0.0.1:8000.
    

2\. Подготовка репозитория и настройка CI/CD (GitHub Actions)
-------------------------------------------------------------

Для автоматической сборки и публикации сайта при каждом пуше в ветку main был настроен пайплайн GitHub Actions.

### 2.1. Инициализация Git и первый коммит

*   bashgit init
    
*   textvenv/site/\_\_pycache\_\_/\*.pyc
    
*   bashgit add .git commit -m "Initial commit with MkDocs project"
    

### 2.2. Создание репозитория на GitHub и связь с локальным

*   На GitHub создан новый репозиторий (например, my-static-site).
    
*   bashgit remote add origin https://github.com/username/my-static-site.gitgit push -u origin main
    

### 2.3. Настройка GitHub Actions workflow

В корне репозитория создан каталог .github/workflows/ и внутри него файл deploy.yml.Использованы современные официальные экшены для загрузки артефактов и деплоя на GitHub Pages (аналог экшена "Static HTML").

yaml

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   name: Deploy MkDocs to Pages  on:    push:      branches: ["main"]    workflow_dispatch:  # Разрешения для GitHub Pages  permissions:    contents: read    pages: write    id-token: write  concurrency:    group: "pages"    cancel-in-progress: false  jobs:    build:      runs-on: ubuntu-latest      steps:        - name: Checkout repository          uses: actions/checkout@v4        - name: Setup Python          uses: actions/setup-python@v5          with:            python-version: '3.12'        - name: Install dependencies          run: |            pip install mkdocs        - name: Build site          run: mkdocs build        - name: Upload artifact          uses: actions/upload-pages-artifact@v3          with:            path: 'site/'    deploy:      environment:        name: github-pages        url: ${{ steps.deployment.outputs.page_url }}      runs-on: ubuntu-latest      needs: build      steps:        - name: Deploy to GitHub Pages          id: deployment          uses: actions/deploy-pages@v4   `

### 2.4. Настройка GitHub Pages

*   В репозитории перейдены в **Settings → Pages**.
    
*   В разделе **Build and deployment** выбран источник **GitHub Actions**.Это позволяет пайплайну самостоятельно управлять публикацией.
    

3\. Результат и отладка
-----------------------

После пуша изменений в ветку main автоматически запустился workflow.Сборка прошла успешно, и сайт стал доступен по адресу:

text

Plain textANTLR4BashCC#CSSCoffeeScriptCMakeDartDjangoDockerEJSErlangGitGoGraphQLGroovyHTMLJavaJavaScriptJSONJSXKotlinLaTeXLessLuaMakefileMarkdownMATLABMarkupObjective-CPerlPHPPowerShell.propertiesProtocol BuffersPythonRRubySass (Sass)Sass (Scss)SchemeSQLShellSwiftSVGTSXTypeScriptWebAssemblyYAMLXML`   https://username.github.io/my-static-site/   `

**Возникшая проблема и её решение:**При первом запуске workflow упал с ошибкой прав доступа. Проблема была устранена явным указанием блока permissions в файле deploy.yml. Без этого экшен deploy-pages не может получить необходимый JWT-токен для деплоя.

Исследовательская часть
-----------------------

### 4.1. Возможности использования отечественных CDN для ускорения доставки контента

CDN (Content Delivery Network) позволяет ускорить загрузку сайта для пользователей из разных регионов за счёт кэширования контента на географически распределённых серверах. Российские аналоги активно развиваются и могут быть интегрированы со статическими сайтами.

ПровайдерОсобенности**NGENIX** ([ngenix.net](https://ngenix.net/))Крупный российский CDN-провайдер, интеграция с Яндекс.Облаком и Selectel, защита от DDoS. Подходит для медиа и интернет-магазинов.**CDNvideo** ([cdnvideo.ru](https://cdnvideo.ru/))Кэширование, оптимизация изображений, защита контента.**DDOS-GUARD** ([ddos-guard.net](https://ddos-guard.net/))Известен защитой от DDoS, но также предоставляет глобальную CDN с узлами в РФ и за рубежом.

**Применение для статического сайта:**Статические файлы (HTML, CSS, JS, изображения) идеально кэшируются. Для использования CDN с GitHub Pages необходимо:

1.  Подключить к репозиторию **кастомный домен**.
    
2.  Настроить CNAME-запись на CDN-провайдера.
    
3.  Настроить правила кэширования в личном кабинете CDN.
    

### 4.2. Возможности GitVerse для реализации CI/CD

**GitVerse** — российская платформа для хостинга Git-репозиториев, аналог GitHub. На текущий момент она предоставляет:

*   **GitVerse Actions** — встроенный CI/CD, совместимый по синтаксису с GitHub Actions. Пайплайны описываются в YAML-файлах в директории .gitverse/workflows/.
    
*   **GitVerse Pages** — сервис для хостинга статических сайтов, аналогичный GitHub Pages. Деплой может выполняться либо пушем в специальную ветку (например, pages), либо через Actions.
    
*   **Маркет действий** — есть готовые шаги для сборки проектов на Python, Node.js, Java, а также для загрузки в S3-хранилища.
    

**Вывод:** GitVerse является полноценной заменой GitHub для данной задачи. Процесс настройки был бы идентичным, за исключением замены github-pages на gitverse-pages и использования соответствующего экшена для деплоя.

### 4.3. Варианты деплоя статического сайта в продакшен-среду

Существует множество способов размещения статических сайтов. Выбор зависит от требований к масштабируемости, контролю и бюджету.

КатегорияПлатформы / ИнструментыОписание / Примеры**PaaS / SaaS хостинги**GitHub Pages, GitLab Pages, Netlify, Vercel, Cloudflare PagesПростота настройки, автоматический деплой по пушу, встроенные CDN, часто бесплатно для открытых проектов.**Объектные хранилища (S3)**Amazon S3, Yandex Object Storage, VK Cloud Storage, [Mail.ru](https://mail.ru/) Cloud StorageГибкость, масштабирование, низкая стоимость. Требуется настройка бакета на статический хостинг. Инструменты: AWS CLI, s3 sync, Terraform, Ansible.**Традиционные серверы (VPS/VDS)**Helios, Timeweb, FirstVDS, собственный серверПолный контроль над окружением. Устанавливается веб-сервер (Nginx, Apache), деплой через Git pull или rsync. Инструменты автоматизации: Capistrano, Fabric, bash-скрипты с rsync, SSH-экшены (например, appleboy/scp-action).

**Сравнение:**

*   **GitHub Pages** — идеально для быстрого старта и документации.
    
*   **Netlify / Vercel** — дополнительные функции (формы, функции, preview deployments).
    
*   **Yandex Object Storage** — подходит для проектов, ориентированных на российских пользователей, с возможностью подключения отечественного CDN.
    
*   **Собственный сервер** — максимальный контроль, но требует администрирования.
    

Заключение
----------

В ходе работы был создан статический сайт с помощью MkDocs, настроен автоматический деплой на GitHub Pages через GitHub Actions. Исследованы альтернативные инструменты: российские CDN, платформа GitVerse со встроенными CI/CD и Pages, а также различные стратегии деплоя статических сайтов. Полученные знания позволяют гибко выбирать стек технологий в зависимости от требований проекта и региона развёртывания.