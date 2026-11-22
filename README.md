# Keepsake — Landing Page

Адаптивний лендінг Keepsake з лабораторної роботи №7 (завдання 4), перенесений на Vite + Pug + SCSS для лабораторної роботи №9. Збережено початкові макет, дизайн-токени, брейкпоінти, порядок секцій, тексти, стани, фільтр портфоліо, мобільне меню та вимоги доступності. Змінився спосіб організації та збирання проєкту.

**Demo:** https://sebafarkash.github.io/landing-keepsake-vite/

## Вимоги до середовища

- Node.js `^20.19.0` або `>=22.12.0`.
- npm 10 або новіша версія.

```bash
npm install
npm run dev
npm run build
npm run preview
npm run images
npm run format
npm run lint
```

`npm run images` запускає Sharp і генерує адаптивні AVIF, WebP та JPEG-файли з оригіналів у `raw-images/`.

## Структура проєкту

```text
landing-keepsake-vite/
├── .github/workflows/deploy.yml
├── .husky/pre-commit
├── raw-images/                  # вихідні растрові зображення
├── scripts/optimize-images.js   # генерація адаптивних зображень через Sharp
└── src/
    ├── assets/images/           # AVIF, WebP, JPEG у 150/300/400/800 px
    ├── components/              # header, footer, newsletter та секції сторінки
    ├── data/                    # масиви об'єктів і дані секцій
    ├── includes/                # meta, fonts, structured data та SVG-іконки
    ├── layouts/main.pug         # базовий Pug-макет
    ├── mixins/                  # btn, icon, picture, work-card та інші міксіни
    ├── pages/index.pug          # сторінка, що успадковує main.pug
    └── styles/
        ├── abstracts/           # дизайн-токени, функції та міксіни
        ├── base/                # reset, root custom properties, типографіка
        ├── components/          # окремий partial для кожного БЕМ-блоку
        ├── layout/              # контейнер, секції, header і footer
        └── main.scss            # єдина точка входу SCSS
```

## Архітектура

### Pug

`pages/index.pug` успадковує `layouts/main.pug` через `extends`. Базовий макет має блоки `meta`, `title`, `styles`, `content` і `scripts`; сторінка використовує `append` та `prepend`, щоб доповнювати метадані, підключати шрифти й структуровані дані. Header, footer та кожна секція підключені окремими файлами через `include`.

Повторюваний контент (навігація, категорії, роботи, послуги, статистика, записи журналу, клієнти, колонки футера та соціальні мережі) зберігається в `src/data/` як масиви об'єктів і виводиться через `each`. Міксіни `btn`, `icon`, `picture`, `work-card`, `service`, `journal-card`, `client-logo` та `social-list` повторно використовують розмітку. `btn` підтримує вкладений `block` і довільні атрибути через `&attributes`; `icon` використовує `case/when`; необов'язковий текст журналу обробляється через `if/else`; `picture` використовує `unless` для lazy loading.

Фільтр портфоліо й кнопка Load more працюють без JavaScript на CSS-селекторах `:checked ~`, як у лабораторній №7. Мобільне меню зберігає popover-поведінку та доступне керування кнопкою.

### SCSS

Стилі організовані в `abstracts`, `base`, `layout` і `components`. Модулі підключаються тільки через `@use` та `@forward`; `main.scss` є єдиною точкою входу. Кожен БЕМ-блок має власний partial. Кольори, шрифти, відступи й брейкпоінти задані SCSS-змінними та map-ами; кольори експортуються у CSS custom properties в `:root`. Mobile-first міксин `mq()` використовує map брейкпоінтів і тільки `min-width`. Є функція `rem()` на `sass:math`, міксіни `visually-hidden`, `focus-ring`, `flex-center`, `button-reset`, `font-scale` і `transition` з підтримкою `prefers-reduced-motion`.

## Інструменти

| Інструмент                                   | Призначення                                           |
| -------------------------------------------- | ----------------------------------------------------- |
| Vite + Vituum + Pug plugin                   | Dev-сервер, шаблонізація Pug і production build       |
| Dart Sass                                    | Компіляція модульного SCSS                            |
| PostCSS + Autoprefixer                       | Вендорні префікси відповідно до Browserslist          |
| Sharp                                        | Ресайз і конвертація растрових зображень              |
| Prettier + `@prettier/plugin-pug`            | Форматування Pug, SCSS, JS, JSON і Markdown           |
| Stylelint + `stylelint-config-standard-scss` | Перевірка SCSS, БЕМ-селекторів і глибини вкладеності  |
| Husky + lint-staged                          | Форматування та перевірки staged-файлів перед комітом |
| GitHub Actions                               | Перевірка, production build і деплой на GitHub Pages  |

Усі інструменти збірки розміщені в `devDependencies`: у браузері працює готовий `dist/`, тому Vite, Sass, Pug-плагін і лінтери не потрібні в runtime.

## Цільові браузери

`.browserslistrc`:

```text
last 2 Chrome versions
last 2 Firefox versions
last 2 Edge versions
last 2 Safari versions
last 2 iOS versions
```

Перевірити фактичний список можна командою `npx browserslist`, а властивості з префіксами — `npx autoprefixer --info`.

## Оптимізація CSS та зображень

Порівняння базового стилю з Lab 7 та production-результату Lab 9:

| Ресурс            |                     До / вихідний |                                   Після / production |                                  Результат |
| ----------------- | --------------------------------: | ---------------------------------------------------: | -----------------------------------------: |
| CSS               | 28.7 KB (розгорнутий Sass output) | близько 23.1 KB (Vite minified, близько 4.2 KB gzip) |                         CSS мініфікує Vite |
| Зображення 800 px |                       JPEG 6.0 KB |              AVIF 1.6 KB / WebP 2.6 KB / JPEG 6.0 KB |       AVIF приблизно на 73% менший за JPEG |
| Прев'ю 150 px     |                       JPEG 1.0 KB |              AVIF 0.6 KB / WebP 0.5 KB / JPEG 1.0 KB | браузер обирає відповідний формат і ширину |

Розміри округлені; вони залежать від конкретного вихідного зображення. Міксин `+picture()` генерує AVIF та WebP `<source>` з `srcset`/`sizes` і JPEG fallback з `width`, `height`, `decoding="async"`. Картки завантажуються lazy; перша картка портфоліо має пріоритет завантаження, як у вихідному макеті Lab 7.

## GitHub Pages

Workflow `.github/workflows/deploy.yml` збирає проєкт і публікує `dist/` на GitHub Pages після push у `main`. У `vite.config.js` задано `base: '/landing-keepsake-vite/'`. У налаштуваннях репозиторію потрібно вибрати **Settings → Pages → Source: GitHub Actions**. `dist/` і `node_modules/` не комітяться; `package-lock.json` комітиться.
