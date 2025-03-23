---
title: "ABUSO DE LOLbins : imewdlb.exe y forfiles"
date: 2025-03-21 00:00:00 +0800
categorias: [Seguridad Ofensiva]
targ: [Lolbins]
---

# **Introducción**

Los **Living Off the Land Binaries (LOLBins)** son ejecutables legítimos de Windows que los atacantes pueden utilizar para evadir la detección y ejecutar código malicioso sin necesidad de archivos adicionales. En este artículo, analizaremos cómo **IMEWDBLD.exe**, una herramienta poco documentada de Windows, puede combinarse con **Forfiles** para ejecutar cargas maliciosas de manera sigilosa.

---

## ¿Qué es IMEWDBLD.exe?

**IMEWDBLD.exe** es una herramienta incluida en Windows que forma parte del *"Input Method Editor"* (IME). Si bien su propósito original es legítimo, se ha descubierto que permite ejecutar comandos arbitrarios, lo que la convierte en una herramienta atractiva para actividades ofensivas.

Este binario puede ser explotado para ejecutar scripts y comandos evadiendo la detección de soluciones de seguridad tradicionales.

---

## ¿Qué es Forfiles y por qué usarlo?

**Forfiles.exe** es una herramienta nativa de línea de comandos de Windows que ejecuta operaciones en archivos según su antigüedad o nombre. Si bien es útil para la administración de sistemas, los atacantes pueden aprovecharla para ejecutar comandos maliciosos en cadena.

Sus principales ventajas para ataques son:

- Ejecutar comandos sin dejar rastros evidentes.
- Utilizar scripts sin requerir archivos adicionales.
- Abusar de funcionalidades nativas de Windows para evadir controles de seguridad.

---


### Ejecución de Código con IMEWDBLD.exe

Para probar la ejecución de código, podemos utilizar IMEWDBLD.exe para lanzar un proceso arbitrario como **cmd.exe**:

```
IMEWDBLD.exe /t "cmd.exe /c calc"
```

Este comando hará que la calculadora se ejecute a través de IMEWDBLD.exe.

### Encadenando con Forfiles para Persistencia

Un atacante podría abusar de **Forfiles** para ejecutar un payload en combinación con IMEWDBLD.exe:

```
forfiles /p C:\Users\Public /m *.txt /c "cmd /c IMEWDBLD.exe /t 'powershell -ExecutionPolicy Bypass -NoProfile -File C:\Users\Public\script.ps1'"

```

Este comando:

1. Busca todos los archivos **.txt** en `C:\Users\Public`.
2. Utiliza **IMEWDBLD.exe** para ejecutar un script de PowerShell potencialmente malicioso.

---

## Técnicas de Evasión

### 1. **Ofuscación de Comandos**

Para evitar la detección, los comandos pueden ser ofuscados mediante PowerShell o el uso de caracteres especiales. Por ejemplo:

```
forfiles /p C:\Users\Public /m *.log /c "cmd /c IMEWDBLD.exe /t '^p^o^w^e^r^s^h^e^l^l^ -eXecUtIoNpOlIcY Bypass -wInDoWsTiLe HiDdEn -File C:\Users\Public\payload.ps1'"

```

### 2. **Uso de Archivos de Señuelo**

Los atacantes pueden crear archivos señuelo (como logs, imágenes o documentos) que, al ser modificados, desencadenen la ejecución de comandos maliciosos.

```
echo trigger > C:\Users\Public\update.log
forfiles /p C:\Users\Public /m update.log /c "cmd /c IMEWDBLD.exe /t 'cmd /c notepad'"

```

Cada vez que se modifique **update.log**, se ejecutará Notepad, aunque en un escenario real esto podría reemplazarse por una herramienta maliciosa.

---

## Mitigaciones

Para protegerse contra estos abusos, se recomiendan las siguientes medidas:

- **Deshabilitar o monitorear IMEWDBLD.exe** cuando no sea esencial para las operaciones.
- **Bloquear la ejecución de Forfiles.exe** en ambientes que no lo requieran.
- **Implementar reglas de detección en SIEM/Sysmon** para supervisar el uso inusual de LOLBins.
- **Utilizar herramientas como AppLocker o Windows Defender Application Control (WDAC)** para limitar la ejecución de binarios a rutas autorizadas.

---

## Conclusión

El abuso de **IMEWDBLD.exe** en combinación con **Forfiles** ilustra cómo los atacantes pueden utilizar herramientas nativas de Windows para ejecutar código malicioso de manera sigilosa. Este conocimiento es fundamental para que los equipos de seguridad puedan anticipar y mitigar estas amenazas antes de que sean explotadas.

¿Conoces otros LOLBins que puedan combinarse para realizar ataques sigilosos? ¡Comparte tus ideas en los comentarios! Si hacemos un post sobre ello