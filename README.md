# ComposePlayground-apk

🇮🇹 Italiano (sotto) · 🇬🇧 [English]( #english )

---

Un editor di codice sicuro e sandboxato per scrivere ed eseguire codice Kotlin / Jetpack Compose direttamente sul tuo dispositivo Android. Niente PC, niente Android Studio: il compilatore Kotlin gira sul telefono.

## Funzionalità

**Editor**
- Syntax highlighting con numeri di riga
- Chiusura automatica delle parentesi: `()`, `[]`, `{}`
- Auto-indentazione intelligente (4 spazi dopo `{`, de-indentazione automatica su `}`)
- Salvataggio automatico: il codice viene salvato ~2 secondi dopo che smetti di digitare
- Due temi: **Dracula** (scuro) e **Light** — selezionabili nelle Impostazioni

**Esecuzione**
- Compilazione Kotlin on-device (compilatore K2)
- Anteprima sicura: il codice gira in un processo sandbox isolato
- Output su console tramite `print()` / `println()`
- Errori del compilatore riportati con il numero di riga **del tuo codice**
- Rilevamento dei loop infiniti: un'anteprima bloccata viene terminata automaticamente

**File**
- Salva e carica snippet di codice dalla schermata File
- Il salvataggio manuale fissa il nome del progetto; l'autosave continua ad aggiornare quello stesso file

## Come funziona

Puoi scrivere **Kotlin puro** oppure **codice Compose**. L'app rileva automaticamente quale dei due:

### Modalità Kotlin puro
Se il codice non contiene alcuna `@Composable`, viene eseguito come script e l'output di `print()` / `println()` viene mostrato in una console a schermo intero.

```kotlin
val numbers = (1..10).filter { it % 2 == 0 }
println("Numeri pari: $numbers")
```

### Modalità Compose
Se il codice contiene almeno una funzione `@Composable`, l'app renderizza un'anteprima live.

**Regole per l'entry point:**

1. Se definisci una funzione chiamata esattamente `Preview` (P maiuscola, senza parametri), quella è l'entry point:

```kotlin
@Composable
fun Greeting(name: String) {
    Text("Ciao, $name!")
}

@Composable
fun Preview() {
    Greeting("Mondo")
}
```

2. Se non esiste una funzione `Preview`, viene eseguita automaticamente **l'ultima funzione `@Composable` che hai definito**:

```kotlin
@Composable
fun Hello() {
    Text("Ciao!")   // questa viene eseguita
}
```

3. ⚠️ **Se l'ultima funzione `@Composable` ha dei parametri, devi definire tu una funzione `Preview()`.** L'app non può indovinare quali argomenti passare:

```kotlin
@Composable
fun Card(title: String, modifier: Modifier = Modifier) { ... }

// Obbligatoria — altrimenti non c'è nulla da renderizzare:
@Composable
fun Preview() {
    Card(title = "Demo")
}
```

Puoi usare `println()` anche dentro il codice Compose: in fondo all'anteprima appare una console di debug.

## Import

Gli import più comuni sono già inclusi — non serve scriverli. Disponibili di default:

```kotlin
// Runtime e State
import androidx.compose.runtime.*
import androidx.compose.runtime.saveable.*
import androidx.compose.runtime.getValue
import androidx.compose.runtime.setValue

// UI Core (Modifier, Alignment, Color, ecc.)
import androidx.compose.ui.*
import androidx.compose.ui.graphics.*
import androidx.compose.ui.text.*
import androidx.compose.ui.text.font.*
import androidx.compose.ui.text.style.*
import androidx.compose.ui.unit.*
import androidx.compose.ui.geometry.*

// Foundation (Box, Row, Column, Background, Shape)
import androidx.compose.foundation.*
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.*
import androidx.compose.foundation.lazy.grid.*
import androidx.compose.foundation.shape.*

// Material Design 3 (Button, Text, Card, ecc.)
import androidx.compose.material3.*

// Animazioni
import androidx.compose.animation.*
import androidx.compose.animation.core.*

// Input e Gesture (pointerInput, detectDragGestures, ecc.)
import androidx.compose.ui.input.pointer.*
import androidx.compose.foundation.gestures.*

// Disegno avanzato (Stroke, DrawScope, ecc.)
import androidx.compose.ui.graphics.drawscope.*

// Coroutines
import kotlinx.coroutines.*
```

## Limitazioni della sandbox

Il codice gira in un ambiente ristretto, applicato sia a **compile time** (plugin del compilatore custom) sia a **runtime** (ClassLoader ristretto). Quanto segue **non è consentito** — né chiamate a costruttori né a metodi:

| Categoria | Bloccato |
|---|---|
| Context / view Android | `LocalContext`, `LocalView`, `LocalConfiguration`, `LocalLifecycleOwner`, `AndroidView`, `AndroidViewBinding`, `android.os.*` |
| I/O su file e rete | `java.io.*`, `java.nio.*`, `java.net.*` |
| Processi e sistema | `java.lang.System`, `java.lang.Process`, `java.lang.Runtime`, `java.lang.Thread`, `android.app.ActivityThread` |
| Reflection e class loading | `java.lang.Class`, `java.lang.ClassLoader`, `java.lang.reflect.*`, `dalvik.*` |

Se il codice usa una API bloccata, la compilazione fallisce con un messaggio di errore.

## Segnalazione degli errori

Gli errori del compilatore mostrano il numero di riga relativo al **tuo** codice, così come lo vedi nell'editor. Gli errori che avvengono fuori dal tuo codice (per esempio nel codice di collegamento generato) vengono riportati senza numero di riga.

## Download

Scarica l'APK dalla sezione [Releases](../../releases).

> ⚠️ Potrebbe essere necessario consentire l'installazione da origini sconosciute.

## Stack tecnologico

- Kotlin · Jetpack Compose · Material 3
- Compilatore Kotlin K2 embedded (compilazione on-device in DEX)
- Plugin del compilatore Kotlin custom (filtro delle API a livello di AST)
- Sandboxing tramite ClassLoader custom a runtime
- Processo di anteprima isolato via `FileProvider` + Activity dedicata

## Feedback

Hai trovato un bug o vuoi proporre una funzionalità? Apri una [issue](../../issues).

---

<a name="english"></a>
# 🇬🇧 English

A safe, sandboxed code editor for writing and executing Kotlin / Jetpack Compose code directly on your Android device. No PC, no Android Studio — the Kotlin compiler runs on the phone.

## Features

**Editor**
- Syntax highlighting with line numbers
- Auto-closing brackets: `()`, `[]`, `{}`
- Smart auto-indentation (4 spaces after `{`, auto-dedent on `}`)
- Auto-save: your code is saved automatically ~2 seconds after you stop typing
- Two themes: **Dracula** (dark) and **Light** — switchable in Settings

**Execution**
- On-device Kotlin compilation (K2 compiler)
- Secure preview: your code runs in an isolated sandbox process
- Console output via `print()` / `println()`
- Compiler errors reported with the line number **of your code**
- Infinite loop detection: a stuck preview is killed automatically

**Files**
- Save and load code snippets from the File screen
- Manual save fixes the project name; auto-save keeps updating that same file

## How it works

Write either **pure Kotlin** or **Compose** code. The app detects which one you wrote:

### Pure Kotlin mode
If your code contains no `@Composable`, it is executed as a script and the output of `print()` / `println()` is shown in a full-screen console.

```kotlin
val numbers = (1..10).filter { it % 2 == 0 }
println("Even numbers: $numbers")
```

### Compose mode
If your code contains at least one `@Composable` function, the app renders a live preview.

**Entry point rules:**

1. If you define a function named exactly `Preview` (capital P, no parameters), that function is the entry point:

```kotlin
@Composable
fun Greeting(name: String) {
    Text("Hello, $name!")
}

@Composable
fun Preview() {
    Greeting("World")
}
```

2. If there is no `Preview` function, the **last `@Composable` function you defined** is executed automatically:

```kotlin
@Composable
fun Hello() {
    Text("Hi there!")   // this runs
}
```

3. ⚠️ **If your last `@Composable` function has parameters, you must define a `Preview()` function yourself.** The app cannot guess what arguments to pass:

```kotlin
@Composable
fun Card(title: String, modifier: Modifier = Modifier) { ... }

// Required — otherwise nothing can be rendered:
@Composable
fun Preview() {
    Card(title = "Demo")
}
```

You can use `println()` inside Compose code too: a debug console appears at the bottom of the preview.

## Imports

Common imports are already provided — you don't need to write them. Available out of the box:

```kotlin
// Runtime & State
import androidx.compose.runtime.*
import androidx.compose.runtime.saveable.*
import androidx.compose.runtime.getValue
import androidx.compose.runtime.setValue

// UI Core (Modifier, Alignment, Color, etc.)
import androidx.compose.ui.*
import androidx.compose.ui.graphics.*
import androidx.compose.ui.text.*
import androidx.compose.ui.text.font.*
import androidx.compose.ui.text.style.*
import androidx.compose.ui.unit.*
import androidx.compose.ui.geometry.*

// Foundation (Box, Row, Column, Background, Shape)
import androidx.compose.foundation.*
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.*
import androidx.compose.foundation.lazy.grid.*
import androidx.compose.foundation.shape.*

// Material Design 3 (Button, Text, Card, etc.)
import androidx.compose.material3.*

// Animations
import androidx.compose.animation.*
import androidx.compose.animation.core.*

// Input & Gestures (pointerInput, detectDragGestures, etc.)
import androidx.compose.ui.input.pointer.*
import androidx.compose.foundation.gestures.*

// Advanced drawing (Stroke, DrawScope, etc.)
import androidx.compose.ui.graphics.drawscope.*

// Coroutines
import kotlinx.coroutines.*
```

## Sandbox limitations

Code runs in a restricted environment, enforced both at **compile time** (custom compiler plugin) and at **runtime** (restricted ClassLoader). The following are **not allowed** — neither constructor calls nor method calls:

| Category | Blocked |
|---|---|
| Android context / views | `LocalContext`, `LocalView`, `LocalConfiguration`, `LocalLifecycleOwner`, `AndroidView`, `AndroidViewBinding`, `android.os.*` |
| File & network I/O | `java.io.*`, `java.nio.*`, `java.net.*` |
| Process & system | `java.lang.System`, `java.lang.Process`, `java.lang.Runtime`, `java.lang.Thread`, `android.app.ActivityThread` |
| Reflection & class loading | `java.lang.Class`, `java.lang.ClassLoader`, `java.lang.reflect.*`, `dalvik.*` |

If your code uses a blocked API, compilation fails with an error message.

## Error reporting

Compiler errors show the line number relative to **your** code as you see it in the editor. Errors that occur outside your code (for example in generated glue code) are reported without a line number.

## Download

Grab the APK from [Releases](../../releases).

> ⚠️ You may need to allow installation from unknown sources.

## Tech Stack

- Kotlin · Jetpack Compose · Material 3
- Embedded K2 Kotlin compiler (on-device compilation to DEX)
- Custom Kotlin compiler plugin (AST-level API filtering)
- Custom runtime ClassLoader sandboxing
- Isolated preview process via `FileProvider` + dedicated Activity

## Feedback

Found a bug or have a feature request? Open an [issue](../../issues).
