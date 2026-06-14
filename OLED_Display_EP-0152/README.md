# OLED Display EP-0152

[![Version](https://img.shields.io/badge/version-1.0.6-blue.svg)](CHANGELOG.md)
[![Stage](https://img.shields.io/badge/stage-experimental-orange.svg)]()
[![Architecture](https://img.shields.io/badge/arch-armhf%20|%20armv7%20|%20aarch64-green.svg)]()

Add-on para Home Assistant que habilita y configura la pantalla OLED integrada en la **Fan Expansion Board Plus 0.91 OLED V1.0** (basada en el controlador SSD1306), diseñada para la Raspberry Pi 4 del proyecto EP-0152.

---

## ¿Qué hace este add-on?

- Activa el bus **I2C** en el sistema (modificando `config.txt` de la partición de arranque) si no está habilitado.
- Habilita los módulos de kernel necesarios (`i2c-dev`, `i2c-bcm2708`) tanto en HassOS como en Raspberry Pi OS (Raspbian).
- Expone una pequeña interfaz web de estado accesible vía **Ingress** en el puerto `8099`.
- Utiliza la librería [`rpi_i2c_oled`](https://github.com/crismc/rpi_i2c_oled) (v1.0.4) para comunicarse con el display SSD1306.

> ⚠️ **Importante:** después de la primera ejecución es necesario realizar **dos reinicios** completos del sistema (apagado total, no solo reinicio de software) para que I2C quede activo.

---

## Requisitos

| Componente | Detalle |
|---|---|
| Hardware | Raspberry Pi 4 con Fan Expansion Board Plus 0.91 OLED V1.0 |
| Controlador OLED | SSD1306 (I2C) |
| Home Assistant | HassOS o Raspberry Pi OS |
| Arquitectura | `armhf`, `armv7`, `aarch64` |

---

## Instalación

1. Ve a **Supervisor → Add-on Store → Repositorios** y añade la URL de este repositorio.
2. Busca **OLED Display EP-0152** e instálalo.
3. Inicia el add-on (configurado como `boot: manual`, debe arrancarse manualmente).
4. Revisa el log para confirmar que I2C fue configurado correctamente.
5. Realiza un **apagado total** (power-off) y vuelve a encender.
6. Si el log indica que I2C sigue sin detectarse, realiza un **segundo apagado total**.

---

## Permisos requeridos

Este add-on requiere permisos elevados para poder configurar el hardware:

| Permiso | Motivo |
|---|---|
| `privileged: SYS_ADMIN` | Necesario para montar particiones del sistema |
| `gpio: true` | Acceso al bus GPIO / I2C |
| `full_access: true` | Acceso completo al hardware de la RPi |
| `hassio_api: true` | Integración con la API de Home Assistant |

> El perfil AppArmor está incluido pero deshabilitado por defecto (`app_armor: false`). Puede activarse manualmente si se desea mayor confinamiento.

---

## Configuración

Este add-on no requiere opciones de configuración adicionales. El archivo `config.json` define:

```json
{
  "startup": "once",
  "boot": "manual",
  "options": {},
  "schema": {}
}
```

El add-on se ejecuta una sola vez (`startup: once`) y no arranca automáticamente con el sistema.

---

## Solución de problemas

**I2C no detectado tras el primer reinicio**
El add-on requiere dos ciclos de apagado total. Comprueba el log: si indica `"This Configurator did it's job"`, realiza el segundo apagado.

**Mensaje "Protection Mode enabled"**
Deshabilita el Modo Protección desde la pantalla de Información de Home Assistant Supervisor antes de ejecutar el add-on.

**El add-on indica "nothing to do"**
El modo protección está activo y bloquea el acceso a las particiones. Desactívalo y reinicia.

**I2C ya detectado (`/dev/i2c-1` presente)**
El log mostrará `"Found i2c access! Nothing to do!"`. El add-on ya no es necesario y puede desinstalarse.

---

## Tecnologías utilizadas

- [rpi_i2c_oled](https://github.com/crismc/rpi_i2c_oled) v1.0.4 — librería Python para displays OLED SSD1306 vía I2C
- Alpine Linux (imagen base de Home Assistant add-ons)
- Python 3, Pillow, smbus

---

## Changelog

| Versión | Cambio |
|---|---|
| 1.0.6 | Corrección de un bug |
| 1.0.3 | Eliminación de errores |
| 1.0.1 | Primera versión |

---

