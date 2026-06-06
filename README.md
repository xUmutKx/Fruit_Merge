# ![icon](icon_mg.png) Fruit Merge — J2ME Merging Puzzle Game

A Suika Game–style merging puzzle built for Nokia and classic Java phones. Drop vegetables into the cup, merge matching ones to grow them, and don't let the pile overflow!

---

## 📱 Compatibility

- **Platform:** J2ME (MIDP-2.0 / CLDC-1.1)
- **Tested On:** Nokia S40 / S60, J2ME Loader (Android)
- **JAR Size:** ~200KB

---

## 🎮 How to Play

1. A vegetable appears above the cup — use **4/6** to move it left or right
2. Press **5** to drop it into the cup
3. Two identical vegetables that touch will **merge** into the next tier
4. Keep merging to evolve vegetables all the way up to the final form
5. Game ends when vegetables pile above the **danger line** for too long

**Tip:** Watch the **"Sonraki" (Next)** preview in the top-right corner to plan your drops!

---

## 🥦 Merge Chain

| Level | Vegetable      |
|-------|----------------|
| 1     | 🥔 Potato      |
| 2     | 🍆 Eggplant    |
| 3     | 🥕 Carrot      |
| 4     | 🥒 Cucumber    |
| 5     | 🧅 Onion       |
| 6     | 🌽 Corn        |
| 7     | 🍅 Tomato      |
| 8     | 🫑 Pepper      |

Merge two identical vegetables → they combine into the next level!

---

## 🕹️ Controls

| Key            | Action                      |
|----------------|-----------------------------|
| **4 / 6**      | Move vegetable left / right |
| **5**          | Drop vegetable              |
| **Soft Left**  | Pause / Resume              |
| **Soft Right** | Back / Menu                 |

---

## ⚠️ Danger System

A red line is drawn near the top of the cup. If any vegetable stays **above this line for 150 game ticks**, it's **game over** — stay organized and merge fast!

---

## 📊 Scoring

| Action          | Points                     |
|-----------------|----------------------------|
| Merge (level N) | Score increases per tier   |
| Chain merge     | Points stack per merge     |

Best score is saved between sessions and shown on the main menu as **Best**.

---

## 📋 Screens

| Screen         | Description                                                               |
|----------------|---------------------------------------------------------------------------|
| **Main Menu**  | OYNA (Play), EN YUKSEK (High Score), AYARLAR (Settings), CIKIS (Exit)    |
| **Game**       | Score display, next-piece preview, danger line, physics cup               |
| **Game Over**  | Final score + best score, restart option                                  |
| **Settings**   | Sound and vibration toggles                                               |
| **Progress**   | Vegetable collection tracker                                              |

---

## 📸 Screenshots

| Gameplay | Main Menu |
|----------|-----------|
| ![gameplay](screenshots/gameplay.png) | ![menu](screenshots/menu.png) |

---

## 🔧 Build (Termux)

```bash
# Setup
mkdir -p build/classes

# Remove null bytes
python3 -c "
import os
for root, dirs, files in os.walk('src'):
    for f in files:
        if f.endswith('.java'):
            p = os.path.join(root, f)
            d = open(p,'rb').read().replace(b'\x00',b'')
            open(p,'wb').write(d)
"

# Compile
javac -source 8 -target 8 -classpath ~/midp.jar -d build/classes \
  src/fruitmerge/FruitMerge.java

# Package
jar cfm FruitMerge.jar META-INF/MANIFEST.MF -C build/classes . icon_mg.png

# ProGuard
java -jar ~/proguard-7.3.2/lib/proguard.jar @proguard.pro

# Bytecode patch (Nokia compatibility)
jar xf FruitMerge_verified.jar
for f in fruitmerge/*.class; do
  printf '\xca\xfe\xba\xbe\x00\x00\x00\x2e' | dd of=$f bs=1 count=8 conv=notrunc 2>/dev/null
done
jar cfm FruitMerge_final.jar META-INF/MANIFEST.MF fruitmerge/*.class icon_mg.png

# Copy to download
cp FruitMerge_final.jar /sdcard/Download/FruitMerge.jar
```

---

## 📁 Project Structure

```
├── src/fruitmerge/
│   └── FruitMerge.java      # Full game (MIDlet, physics, rendering, screens)
├── META-INF/MANIFEST.MF
├── icon_mg.png              # App icon
├── proguard.pro
└── FruitMerge.jar           # Prebuilt JAR (ready to install)
```

---

## ⚠️ Technical Notes

- `StringBuilder` / `StringBuffer` / `String +` **NOT USED** — custom `cat()` helper used instead
- `long`, `double`, `float` **NOT USED** — all math uses `int`
- `Thread.sleep()`, `Image.getRGB()`, `ByteArrayOutputStream` **NOT USED**
- `drawRegion()` and `Sprite` **NOT USED**
- Bytecode version patched to `0x2E` for Nokia compatibility
- ProGuard config includes `-microedition` flag
- Null bytes stripped from source before compile

---

## 👤 Author

**UmutK** — 2026  
License: MIT
