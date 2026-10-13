# Установка на deb/rpm-дистрибутивах без сборки из сырцов вручную

Шаблон: см. `wmde-start-menu`, `wmde-panel`, `wmde-bg`. Автор собирает пакеты у себя локально, как сейчас `makepkg -si`.

Опорные платформы — Debian stable и Fedora: `.deb` собираем на Debian (встаёт и на Ubuntu/Mint),
`.rpm` — на Fedora (встаёт и на RHEL/openSUSE). Наоборот не гарантируется.

Соседние чек-ауты не нужны: все path-зависимости (`lib`, `shell`) внутри репозитория.

## Собрать

```sh
# зависимости сборки: свежий stable rustc cargo git + nfpm (lockfile уже требует новее 1.93)
cargo install --locked nfpm  # или бинарник с github.com/goreleaser/nfpm/releases

just deb   # -> target/wmde-randr_*.deb
just rpm   # -> target/wmde-randr-*.rpm
```

Версия — `1.10.0.<commits>.g<sha>`, как `pkgver()` в `PKGBUILD`.
По умолчанию сборка под чистый `x86-64`. Опционально под `x86-64-v3` (как в Arch-репозитории):

```sh
WMDE_CPU=x86-64-v3 just deb
```

## Офлайн-сборка (без сети к git-зависимостям)

`vendor.tar` в git не лежит (см. `.gitignore`), генерим на машине с сетью один раз:

```sh
just vendor          # -> vendor.tar (реестр + git-крейты)
just deb-vendored    # сборка --frozen --offline -> target/*.deb
just rpm-vendored    # аналогично -> target/*.rpm
```

На машину без сети привозим `vendor.tar` файлом.

## Поставить

```sh
# Debian/Ubuntu/Mint
sudo apt install ./target/wmde-randr_*.deb

# Fedora/RHEL/openSUSE
sudo dnf install ./target/wmde-randr-*.rpm
```

Рантайм-зависимости подтянутся сами (см. `packaging/nfpm.yaml`).
Пакет нужен `wmde-osd`: тот вызывает `wmde-randr kdl`, чтобы применить раскладку мониторов.
