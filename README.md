# DeltaOnline — онлайн для Deltarune (манифест + xdelta)

Публичный репозиторий с **манифестом обновлений** и **xdelta-патчами** для лаунчера
**Delta Online**. Полные `data.win` здесь не лежат: патч — 0.4–2.6 МБ вместо 13–165 МБ.

## Что это

Базовая онлайн-модификация глав 1–5: в игре видны другие игроки в той же комнате
(спрайт Криса с ником) и общий чат (`T` — открыть, `Enter` — отправить, `Esc` — закрыть).
Больше ничего не меняется: сюжет, бои, сохранения остаются локальными.

## Файлы

```
manifest.json                 версия, список глав, SHA256 патчей и результатов
patches/*.xdelta              патчи <slot>_windows/data.win -> online.win
README.md
```

## Важно про ключ

**Здесь нет и не должно быть IP сервера и JoinKey.** Репозиторий публичный.
`launcher.json` с `host`/`port`/`key` раздаётся отдельно — хост кладёт его рядом с
`DeltaOnlineLauncher.exe` и передаёт друзьям вместе с папкой `publish`.

## Для игрока

1. Возьмите у хоста папку `publish`: `DeltaOnlineLauncher.exe`, `xdelta3.exe`,
   `launcher.json` (в нём уже вписаны сервер и ключ).
2. Запустите `DeltaOnlineLauncher.exe`. Если папка игры не нашлась — «...» и укажите
   папку с `DELTARUNE.exe`.
3. `Load manifest` → выберите главу → `Установить и играть`.
   Повторное нажатие на уже установленную главу ничего не качает.

Лаунчер сам:
- находит чистый `data.win` главы (`<slot>_windows/data.win`, `original/<slot>/data.win`
  или `data.win.bak-online-*`) и сверяет его SHA256 с манифестом;
- скачивает только нужный патч, проверяет SHA256, кладёт `online.win` в папку главы;
- дописывает `host`/`port`/`name`/`key` в `%LOCALAPPDATA%\DELTARUNE\online-client.ini`
  и запускает `DELTARUNE.exe -game <...>/online.win`.

Требования: лицензионная DELTARUNE с главами 1–5, Windows x64, .NET не нужен
(лаунчер self-contained). Папка `lang\` уже лежит в папке главы — её не удаляйте.

## Манифест

```json
{
  "version": "10",
  "notes": "base build: vanilla chapters 1-5 + online client (players + chat)",
  "chapters": [
    {
      "slot": "chapter1",
      "chapter": 1,
      "label": "Chapter 1",
      "world_id": "online-ch1-v1",
      "patch_url": "patches/deltarune-chapter1-online-v10-20261105-143034.xdelta",
      "patch_sha256": "…",
      "original_sha256": "…",
      "result_sha256": "…",
      "result_bytes": 13107692
    }
  ]
}
```

| Поле | Что значит |
|---|---|
| `slot` | папка главы: `chapter1` … `chapter5` |
| `chapter` | номер главы 1–5 (уходит в `hello` клиента) |
| `world_id` | группа присутствия; у каждой главы своя (`online-chN-v1`) |
| `patch_url` | путь к патчу относительно `manifest.json` |
| `patch_sha256` | SHA256 файла патча |
| `original_sha256` | SHA256 чистого `data.win`, на который патч накладывается |
| `result_sha256` | SHA256 результата; именно это имя файла ищет/проверяет лаунчер |
| `result_bytes` | размер результата |

## Как выпустить новую версию

```powershell
# в основном репозитории проекта
Online\client\Build-Online-Chapter.ps1          # пересобрать online.win во всех главах
Online\client\Publish-Chapter.ps1 -Version 11   # патчи + Online\patches\manifest.json
```

Затем выложить сюда: `manifest.json` и новые `patches/*.xdelta` (старые удалить —
друзьям они больше не нужны), обновить этот README при смене формата.

Ничего, кроме `manifest.json` и `patches/`, в репозиторий не выкладывается:
полные `data.win`, музыка, сейвы и конфиги сервера остаются локально.
