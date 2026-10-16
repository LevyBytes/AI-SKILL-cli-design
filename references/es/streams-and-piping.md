# Flujos y tuberías

Uso de stdin/stdout/stderr para que un comando se pueda combinar con otros. Los datos van a stdout, los diagnósticos a stderr y `-` significa «usar un flujo». Diseñar para tuberías permite usar una CLI como componente de otros procesos.

**Cuándo se aplica:** al decidir qué enviar a stdout o stderr, admitir tuberías, leer datos canalizados o usar el operando `-`.

## Principios

- **stdout = los datos del programa; stderr = mensajes sobre el programa** (registros, progreso, advertencias y errores). Así, `tool | other` transmite datos limpios mientras la persona sigue viendo el estado.
- **Diseña la salida como entrada del siguiente programa**. El texto organizado en líneas se combina con `grep`/`awk`/`sort`; ofrece `--json` para datos estructurados. Consulta [Salida y formato (en inglés)](../output-and-formatting.md).

## Reglas de decisión

### Qué va en cada flujo

```text
$ tool export > data.csv      # los datos de stdout se redirigen sin mezclarse
Exporting... done             # este mensaje de estado va a stderr y sigue visible
```

- La información auxiliar (indicadores animados, progreso de acciones y solicitudes de entrada) va a stderr, para que al redirigir stdout los datos permanezcan limpios. Consulta [Progreso y respuesta (en inglés)](../progress-and-feedback.md).

### Lectura y escritura de flujos

- **Admite `-` como operando** para indicar stdin al leer o stdout al escribir: `curl … | tar xzf -`; `tool fmt -` lee stdin.
- Si el programa necesita entrada canalizada pero stdin es una TTY, muestra la ayuda o termina; no te quedes esperando datos que no llegan. Consulta [Interactividad, TTY y CI (en inglés)](../interactivity-tty-and-ci.md).
- Detecta si stdin/stdout son TTY para alternar entre los modos para personas y para programas.

### Comportamiento adecuado para tuberías

- Mantén stdout redirigido libre de color y animación. Evalúa stderr por separado antes de mostrar progreso con estilo; el modo de texto simple o no interactivo desactiva las animaciones en ambos destinos. Consulta [Color y estilo (en inglés)](../color-and-styling.md) y [Progreso y respuesta (en inglés)](../progress-and-feedback.md).
- Vacía los búferes de salida con prontitud para que los consumidores posteriores reciban datos; ten en cuenta el almacenamiento en búfer completo cuando stdout sea una tubería.
- Gestiona `SIGPIPE`/`EPIPE` correctamente: si un lector posterior, como `head`, cierra la tubería, termina sin una excepción no controlada. Conserva la política de salida documentada: la terminación predeterminada por SIGPIPE no devuelve cero; omitir un diagnóstico no convierte cualquier fallo de escritura en un éxito.

## Consideraciones para Windows y PowerShell

- Las tuberías de Unix transportan bytes; las tuberías entre cmdlets de PowerShell transportan objetos .NET. Los comandos nativos conservan flujos de bytes, a menudo convertidos a texto al pasar por PowerShell. PowerShell 7.4+ conserva los bytes en tuberías directas entre comandos nativos y al redirigir stdout nativo. Emite texto o JSON documentado para `... | ConvertFrom-Json`; no trates datos binarios arbitrarios como texto.
- En Windows PowerShell 5.1, redirigir stderr de un programa nativo puede convertir sus líneas en registros de error. No uses el tratamiento de stderr para controlar el flujo: el código de salida sigue siendo la referencia. Consulta [Errores y códigos de salida (en inglés)](../errors-and-exit-codes.md). En 5.1, `tool --json | Out-File -LiteralPath out.json -Encoding utf8` escribe UTF-8 con BOM; `Out-File` y `>` usan UTF-16LE de forma predeterminada. Asegura primero la decodificación correcta del texto nativo. El operador `>` no tiene un parámetro `-Encoding`.

## Casos especiales y prácticas que evitar

- **No** envíes los datos principales a stderr ni los mensajes de estado a stdout: se mezclan con los datos de las tuberías.
- **No** deduzcas que hay una persona solo porque existe una TTY; CI puede asignar pseudoterminales. Comprueba los tres flujos y respeta el modo no interactivo explícito.
- Mezclar stdout y stderr en una terminal puede intercalar sus mensajes; conserva los datos para programas exclusivamente en stdout.

## Qué hacer y qué evitar

- **Haz:** conserva stdout exclusivamente para los datos y usa stderr para lo demás.
- **Haz:** admite `-` para stdin/stdout y detecta TTY.
- **Evita:** quedarte esperando una entrada stdin que no se está canalizando.
- **Evita:** fallar de forma abrupta cuando se cierre una tubería posterior.

## Referencias relacionadas

[Salida y formato](../output-and-formatting.md) · [Interactividad, TTY y CI](../interactivity-tty-and-ci.md) · [Progreso y respuesta](../progress-and-feedback.md) · [Errores y códigos de salida](../errors-and-exit-codes.md). Estas referencias están en inglés.

Fuentes: [PowerShell redirection](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_redirection?view=powershell-7.5) · [PowerShell encoding](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_character_encoding?view=powershell-5.1).
