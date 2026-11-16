# Інтеграція заставки Alderwood у гру (інструкції для Claude Code)

Скопіюй цей файл (або посилання на нього) у сесію Claude Code, відкриту в репозиторії гри.

---

Інтегруй у цю гру спільну заставку студії Alderwood з
https://github.com/andriiolshevskyi7-hash/alderwood-splash. Гра — Godot 4 (Android, портрет).
Виконай кроки по черзі:

1. **Скопіюй аддон.** Клонуй репозиторій у тимчасову папку і скопіюй лише папку аддона:
   ```bash
   TMP=$(mktemp -d)
   git clone --depth 1 https://github.com/andriiolshevskyi7-hash/alderwood-splash "$TMP/alderwood-splash"
   rm -rf addons/alderwood_splash
   mkdir -p addons
   cp -r "$TMP/alderwood-splash/addons/alderwood_splash" addons/
   rm -rf "$TMP"
   cat addons/alderwood_splash/VERSION
   ```
   Не копіюй нічого іншого з репозиторію (README, .git тощо).

2. **Зроби заставку головною сценою.** Прочитай `run/main_scene` у секції `[application]`
   файлу `project.godot` — це стара головна сцена. Якщо там `uid://...`, знайди відповідний
   `res://` шлях (пошук цього uid у `.tscn`/`.uid` файлах) і використовуй саме `res://` шлях.
   - Якщо стара головна сцена — `res://main.tscn`: встанови
     `run/main_scene="res://addons/alderwood_splash/splash.tscn"`.
   - Інакше створи файл `res://alderwood_splash_main.tscn` (у корені гри, НЕ в `addons/`):
     ```
     [gd_scene load_steps=2 format=3]

     [ext_resource type="PackedScene" path="res://addons/alderwood_splash/splash.tscn" id="1_splash"]

     [node name="AlderwoodSplash" instance=ExtResource("1_splash")]
     next_scene = "<res:// шлях до старої головної сцени>"
     ```
     і встанови `run/main_scene="res://alderwood_splash_main.tscn"`.
   - Перевір, що в старій головній сцені / autoload-ах немає логіки, яка покладається на те,
     що вона завантажується першою через `main_scene` (наприклад, перевірки
     `get_tree().current_scene` при старті). Якщо є — повідом мене, нічого не ламай.

3. **Вимкни стандартну заставку Godot.** У секції `[application]` файлу `project.godot`
   встанови (додай або заміни):
   ```ini
   boot_splash/show_image=false
   boot_splash/bg_color=Color(0.0627451, 0.0784314, 0.0941176, 1)
   ```
   Це колір `#101418` — фон заставки, щоб при запуску не було спалаху іншого кольору.
   Не чіпай інші налаштування `project.godot`.

4. **Перевір, що збірка не зламалась.**
   - `export_presets.cfg`: якщо `export_filter` не `"all_resources"` (наприклад, `"resources"`
     чи `"scenes"`), додай файли аддона (і сцену-обгортку, якщо є) до списку експорту;
     переконайся, що `include_filter`/`exclude_filter` не відсікають `addons/alderwood_splash/*`.
     Більше нічого в пресетах не змінюй (ключі, підписи, пакет — не чіпати).
   - Workflow-и в `.github/workflows/`: перевір, що вони експортують гру з `project.godot`
     без жорстко прописаної головної сцени, і що перед експортом відбувається імпорт ресурсів
     (`godot --headless --import` або еквівалент), бо в аддоні немає `.import`-файлів.
     Якщо імпорту немає і експорт без нього падає — додай цей крок.
   - Якщо Godot доступний локально — запусти `godot --headless --import` і переконайся,
     що немає помилок у `addons/alderwood_splash`. Якщо є можливість — запусти workflow збірки
     і перевір, що він зелений.

5. **Не змінюй файли всередині `addons/alderwood_splash`.** Це копія зі спільного репозиторію;
   будь-які правки заставки робляться лише в репозиторії alderwood-splash. Якщо щось у
   заставці треба виправити для цієї гри — не редагуй, а опиши проблему мені.
   (Згенеровані Godot файли `*.import` / `*.uid` поруч — це нормально, їх можна комітити.)

Наприкінці: закоміть зміни окремим комітом на кшталт
`Add Alderwood splash v<VERSION>` і коротко звітуй, що змінено (main_scene, next_scene,
boot_splash, export_presets/CI) і яку версію заставки встановлено.

**Оновлення заставки в майбутньому:** повтори крок 1 (папка перезаписується повністю),
сцену-обгортку і `project.godot` не чіпай, якщо в новій версії не сказано інакше.
