# Instalación del paquete de riego

## Carga correcta

En `configuration.yaml` debe figurar **solo** una de estas formas:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

Copia a `config/packages/`:

- `riego_inteligente.yaml` — lógica de riego (helpers, scripts, automations, template)
- `mantenimiento.yaml` — reinicio Tailscale (opcional)
- **No** copies `riego.yaml` a `packages/` (es un dashboard Lovelace, no un paquete)

## Error: `integration '…' has duplicate key 'name'` / `'alias'`

Home Assistant muestra esto cuando **las mismas entidades ya existen en otro YAML** y el paquete intenta volver a definirlas.

Es exactamente el síntoma de cargar el paquete **dos veces**, o de tener los mismos `input_*` / `group` / `script` en:

- `configuration.yaml`, o
- `scripts.yaml` / `groups.yaml` / `input_number.yaml` / etc., o
- **otro** fichero dentro de `packages/` con el mismo contenido (p. ej. `riego_inteligente_old.yaml`)

### Cómo comprobarlo

1. Busca en `/config` (fuera de un único `packages/riego_inteligente.yaml`) IDs como:
   - `riego_objetivo_cherry`
   - `mi_flora_cherry`
   - `notificar_riego`
   - `riego_programado_zona1`
2. Elimina las definiciones duplicadas; deja **solo** las del paquete.
3. Reinicia Home Assistant (no basta con recargar solo automations).

### Qué no hace falta tocar

- Helpers creados solo en la UI (`.storage`) con **otros** IDs no provocan este error de merge.
- `automation` y `template` del paquete se fusionan como listas; el choque típico es en `group`, `input_*` y `script`.
