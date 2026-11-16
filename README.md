# Alderwood Splash

Спільна заставка студії **Alderwood** для ігор на Godot 4 (Android, портрет).
Уся заставка — одна папка `addons/alderwood_splash/`, яку копіюють у гру без змін.

- Темний фон `#101418`, логотип (дерево + напис "Alderwood") по центру, ~40% ширини екрана
- Анімація: fade-in + легке збільшення 0.4 с → пауза 1.2 с → fade-out 0.4 с (разом 2.0 с)
- Тап по екрану (або клік / `ui_accept`) пропускає заставку
- Без autoload і без залежностей від гри, сумісно з Godot 4.3+
- Версія — у файлі `addons/alderwood_splash/VERSION`

```
addons/alderwood_splash/
├── splash.tscn   # сцена заставки
├── splash.gd     # логіка (@export next_scene)
├── logo.svg      # логотип, текст переведено в контури
└── VERSION
```

## Підключення до гри

1. **Скопіювати папку** (з кореня гри):
   ```bash
   rm -rf /tmp/alderwood-splash
   git clone --depth 1 https://github.com/andriiolshevskyi7-hash/alderwood-splash /tmp/alderwood-splash
   rm -rf addons/alderwood_splash
   mkdir -p addons
   cp -r /tmp/alderwood-splash/addons/alderwood_splash addons/
   rm -rf /tmp/alderwood-splash
   ```

2. **Зробити заставку головною сценою**, а `next_scene` направити на стару головну сцену.
   Подивіться поточне значення `run/main_scene` у секції `[application]` файлу `project.godot`.
   - Якщо стара головна сцена — саме `res://main.tscn`, достатньо:
     ```ini
     run/main_scene="res://addons/alderwood_splash/splash.tscn"
     ```
   - Інакше створіть у грі (поза папкою аддона!) сцену-обгортку `res://alderwood_splash_main.tscn`,
     яка успадковує заставку і задає `next_scene`:
     ```
     [gd_scene load_steps=2 format=3]

     [ext_resource type="PackedScene" path="res://addons/alderwood_splash/splash.tscn" id="1_splash"]

     [node name="AlderwoodSplash" instance=ExtResource("1_splash")]
     next_scene = "res://шлях/до/старої_головної.tscn"
     ```
     і зробіть головною її: `run/main_scene="res://alderwood_splash_main.tscn"`.
     (У редакторі: Scene → New Inherited Scene → splash.tscn, змінити `Next Scene` в інспекторі, зберегти поза `addons/`.)

3. **Вимкнути стандартну заставку Godot** — у секції `[application]` файлу `project.godot`:
   ```ini
   boot_splash/show_image=false
   boot_splash/bg_color=Color(0.0627451, 0.0784314, 0.0941176, 1)
   ```
   Колір = `#101418`, такий самий, як фон заставки, — щоб не було спалаху іншого кольору.

4. **Перевірити збірку.** У `export_presets.cfg` фільтр експорту має включати аддон
   (`export_filter="all_resources"` — ок; якщо експортуються лише вибрані ресурси — додайте
   `addons/alderwood_splash/*`, а `exclude_filter` не повинен його відсікати). Переконайтеся,
   що збірка в GitHub Actions проходить (за потреби перед експортом є крок імпорту
   `godot --headless --import`, бо `.import`-файли аддона генеруються Godot).

5. **Не змінюйте файли в `addons/alderwood_splash` у грі.** Усі зміни — лише в цьому репозиторії
   (з підняттям `VERSION`), а в іграх оновлення = повторити крок 1. Сцена-обгортка з кроку 2
   належить грі й при оновленні не перезаписується.

## Для Claude Code

Готові інструкції для автоматичної інтеграції — у [INTEGRATION_PROMPT.md](INTEGRATION_PROMPT.md).
