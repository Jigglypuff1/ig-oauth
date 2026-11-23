# Страница-редирект для Instagram OAuth

Meta не принимает `nephriteshop://...` как `redirect_uri`. Эта страница принимает
HTTPS-редирект от Meta (`?code=...&state=...` или `?error=...`) и возвращает браузер
в приложение через `nephriteshop://instagram/oauth`.

Секретов здесь нет. Страницу нужно только разместить на HTTPS; в приложении её код
не используется.

В странице нет внешних скриптов/шрифтов/аналитики, стоит `Referrer-Policy:
no-referrer` и CSP. Для Netlify файл `_headers` добавляет `Cache-Control: no-store`
и `X-Content-Type-Options: nosniff`. GitHub Pages `_headers` игнорирует — мета-теги
в `index.html` при этом всё равно работают.

## Вариант 1: GitHub Pages

1. Создайте **публичный** репозиторий (например, `ig-oauth`).
2. Загрузите в него папку `oauth-redirect` с `index.html`.
3. Settings → Pages → Deploy from a branch → `main` / root.
4. Получите URL вида:
   ```text
   https://<username>.github.io/ig-oauth/oauth-redirect/
   ```
5. Этот URL добавьте в Meta → Valid OAuth Redirect URIs.

## Вариант 2: Netlify Drop

1. Откройте `https://app.netlify.com/drop`.
2. Перетащите папку `oauth-redirect`.
3. Получите HTTPS-URL сайта (корень сайта — это `index.html`).
4. Войдите в аккаунт, чтобы сайт не был временным.
5. URL добавьте в Meta → Valid OAuth Redirect URIs.

## После размещения

1. `local.properties`:
   ```properties
   instagram.redirectUri=https://<ваш-URL>/
   ```
   URL должен **точь-в-точь** совпадать с Meta, включая завершающий `/`.
2. Пересоберите APK:
   ```powershell
   .\gradlew.bat :app:assembleDebug
   ```
3. Установите и войдите: приложение откроет браузер, после согласия Meta откроет
   эту страницу, она вернёт вас в приложение.

Если автоматический переход на `nephriteshop://` заблокирован браузером, на странице
есть кнопка «Открыть приложение».
