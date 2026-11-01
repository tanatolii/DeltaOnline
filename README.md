# DeltaOnline — Deltarune Ghost presence (manifest + xdelta)

Публичный репозиторий с **манифестом обновлений** и **xdelta-патчами** для лаунчера
Deltarune Ghost. Полные `data.win` здесь не лежат: патч — 2–17 МБ вместо 15–165 МБ.

## Файлы

```
manifest.json                 список глав, версия, SHA256
patches/*.xdelta              патчи original/<slot>/data.win -> ghost.win
README.md
```

**Не кладите сюда IP сервера и JoinKey.** Их игрок вводит в лаунчере; репозиторий публичный.

## Для игрока

1. Скачайте папку `publish` лаунчера (`DeltaruneGhostLauncher.exe` + `xdelta3.exe`).
2. Запустите лаунчер, укажите папку игры, host/port, имя и `JoinKey`.
3. В поле `Update manifest` вставьте:
   `https://raw.githubusercontent.com/<ваш-логин>/DeltaOnline/main/manifest.json`
4. `Load manifest` → выберите главу → `Update selected` (или `Update all`) → `Play`.

Лаунчер сам:
- найдёт чистый `original/<slot>/data.win`, `<slot>_windows/data.win` или
  `data.win.bak-ghost-*` с нужным SHA256;
- скачает только нужный патч, проверит SHA256, соберёт `build/<slot>/ghost.win`;
- запишет `chapter`/`world` в `ghost-client.ini` и запустит `DELTARUNE.exe -game ...`.

## Манифест

```json
{
  "version": "3",
  "notes": "chat, 2x dark-world ghosts, presence in menus/cutscenes/battles",
  "chapters": [
    {
      "slot": "chapter4",
      "chapter": 4,
      "label": "Chapter 4",
      "world_id": "ch4-test-v1",
      "patch_url": "patches/deltarune-chapter4-ghost-v3-20261101-205511.xdelta",
      "patch_sha256": "...",
      "original_sha256": "...",
      "result_sha256": "...",
      "result_bytes": 133107614
    }
  ]
}
```

- `patch_url` может быть относительным к `manifest.json` или абсолютным URL.
- `original_sha256` — версия игры, к которой подходит патч. После обновления Steam
  патч нужно пересобрать.
- `world_id` — тег совместимости клиента; сервер принимает список `WorldIds`.
- `chapter` — номер главы для протокола; 1-01 — отдельный `slot` с `chapter: 1`.

## Обновление (для хоста)

1. Собрать главу:
   ```powershell
   DeltaruneGhostServer\client\Build-Ghost-Chapter.ps1 -Slot chapter5
   ```
2. Опубликовать патч и обновить манифест:
   ```powershell
   DeltaruneGhostServer\client\Publish-Chapter.ps1 -Slot chapter5 -Chapter 5 `
     -Label "Chapter 5" -World ghost-ch5-v1
   ```
   Патч появится в `dist\DeltaOnline\patches`, манифест — в `dist\DeltaOnline\manifest.json`.
3. Закоммитить и запушить `manifest.json` + `patches/`.

## Git для новичка

Если репозиторий уже создан на GitHub и пустой:
```powershell
cd E:\DeltaruneAiConnector\dist\DeltaOnline
git init
git add .
git commit -m "ghost v3: chapters"
git branch -M main
git remote add origin https://github.com/<логин>/DeltaOnline.git
git push -u origin main
```

Если в репозитории уже есть README:
```powershell
git clone https://github.com/<логин>/DeltaOnline.git C:\Temp\DeltaOnline
copy E:\DeltaruneAiConnector\dist\DeltaOnline\manifest.json C:\Temp\DeltaOnline\
xcopy /E /I E:\DeltaruneAiConnector\dist\DeltaOnline\patches C:\Temp\DeltaOnline\patches
cd C:\Temp\DeltaOnline
git add .
git commit -m "update ghost patches"
git push
```

Можно и через веб-интерфейс GitHub: `Add file` → `Upload files` → перетащить
`manifest.json` и папку `patches`, затем `Commit changes`.

## Сервер

Один процесс на 7777 обслуживает несколько глав:
`Chapters: [1,2,3,4,5,6]`, `WorldIds: [...]` в `server.private.json`.
Игроки в разных главах не видят друг друга; чат — только в своей главе+комнате.
Подробности — в `DeltaruneGhostServer\PROTOCOL.md`.
