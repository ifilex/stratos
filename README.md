# Stratos

### Constructor de estrategias mediante parámetros para MetaTrader 4

Stratos es un Expert Advisor desarrollado en **MQL4** que permite configurar reglas de trading desde los parámetros de MetaTrader 4.

Este repositorio comparte la **versión 4.1**.

Incluye tres espacios de estrategia, condiciones basadas en indicadores y patrones de velas, acciones operativas y un panel de monitoreo.

## Contexto del proyecto

Como neuropsicólogo y desarrollador, me interesa cómo se transforma una observación en una regla explícita.

Stratos permite expresar esa relación de forma concreta:

    Cuando se cumplen estas condiciones, ejecutar esta acción.

El objetivo es facilitar la construcción y revisión de reglas sin modificar el código para cada combinación admitida.

Es un motor de reglas. No incorpora inteligencia artificial ni aprendizaje automático.

## Configuración de estrategias

Cada estrategia dispone de:

- Activación independiente.
- Nombre y símbolo.
- Una condición principal.
- Una segunda condición opcional.
- Una tercera condición opcional, preparada inicialmente para patrones.
- Acción de compra, venta o cierre.
- Lote base.
- Distancias de stop loss y take profit.
- Filtro opcional de noticias.

Todas las condiciones activadas se combinan mediante **AND**.

Los tres espacios están desactivados por defecto.

La configuración se modifica desde las propiedades del EA. El panel no incluye un editor interactivo de estrategias.

## Indicadores disponibles

| Selección | Valor utilizado por el motor |
|---|---|
| RSI | Valor del RSI |
| MACD | Línea principal, con señal de período 9 |
| Estocástico | Línea principal %K |
| ADX | Línea principal |
| CCI | CCI sobre precio típico |
| Williams %R | Valor de WPR |
| Bollinger | Banda central |
| EMA | Diferencia entre dos medias exponenciales |
| ATR | Valor del ATR |
| Precio | Bid actual |
| Patrón | Resultado de una detección de velas |

Los indicadores utilizan el período del gráfico donde está instalado el EA.

La selección Bollinger no representa automáticamente sobrecompra, sobreventa ni posición dentro de las bandas: devuelve su línea central.

## Operadores

Incluye:

- Mayor que.
- Menor que.
- Cruza hacia arriba.
- Cruza hacia abajo.
- Igualdad con tolerancia.
- Entra en zona.
- Sale de zona.
- Toca un valor con tolerancia.

En esta versión, “entra en zona” equivale a cruzar hacia abajo un umbral y “sale de zona” a cruzarlo hacia arriba. No representan un intervalo con dos límites.

Las condiciones actuales pueden utilizar la vela en formación.

## Patrones de velas

Implementa detecciones geométricas de:

- Pin bar alcista y bajista.
- Envolvente alcista y bajista.
- Doji.
- Hammer.
- Patrón denominado estrella.

Estas detecciones utilizan velas cerradas y fórmulas propias del código. Sus nombres no acreditan validación estadística ni equivalencia con todas las definiciones de análisis técnico.

## Acciones

Cada estrategia puede:

- Comprar.
- Vender.
- Cerrar compras.
- Cerrar ventas.
- Cerrar todas sus posiciones.

Los cierres se limitan al símbolo y al número mágico de esa estrategia. “Cerrar todo” no significa liquidar toda la cuenta.

Los identificadores asignados son:

| Estrategia | Número mágico |
|---|---:|
| E1 | `88001` |
| E2 | `88002` |
| E3 | `88003` |

Una estrategia de cierre independiente no comparte automáticamente el identificador de otra estrategia de entrada.

## Frecuencia y lotaje

Las aperturas exitosas se limitan a una por estrategia y barra del gráfico principal.

Si las condiciones siguen cumpliéndose, puede abrir nuevas posiciones en barras posteriores.

Cuando ya existen posiciones del mismo lado, el lote puede calcularse multiplicando el de una orden encontrada por `MultiplicadorLote`.

Su valor predeterminado es **1.5**. No depende de que la operación anterior haya cerrado con pérdida.

Esta versión no incorpora un máximo configurable de órdenes ni un techo agregado de riesgo monetario.

## Gestión de posiciones

Incluye:

- SL y TP configurados por estrategia.
- Trailing stop.
- Ajuste del stop hacia la entrada más un desplazamiento.
- Bloqueo por drawdown.

El trailing y el ajuste hacia breakeven actúan sobre órdenes cuyos números mágicos estén entre `88000` y `88100`, inclusive.

`AutoEjecutar=false` suspende la ejecución de estrategias, incluidas sus acciones de cierre. La gestión por trailing y breakeven continúa.

## Bloqueo por drawdown

Compara el equity de toda la cuenta con el balance registrado al inicializar el EA.

Al alcanzar el umbral, bloquea la ejecución de estrategias.

**No cierra posiciones existentes y también impide ejecutar las acciones de cierre configuradas como estrategias.**

El trailing y el breakeven siguen activos si están habilitados.

El bloqueo se restablece al reinicializar el EA.

## Noticias económicas

Incluye una consulta configurable mediante `WebRequest` a un calendario XML externo.

El parser intenta seleccionar eventos cuyo impacto contenga `High`.

Cada estrategia puede evitar ejecutar acciones dentro de una ventana anterior y posterior al evento. El filtro se aplica a todos los eventos cargados, sin comprobar si su moneda corresponde al símbolo.

La disponibilidad del proveedor, el formato XML y la interpretación de fechas deben validarse en el entorno de uso.

Si no se cargan eventos, la ausencia de datos no bloquea por sí misma la operativa.

## Visualización

El panel muestra:

- Configuración y estado de las estrategias.
- Valores de condiciones.
- Indicadores del gráfico principal.
- Noticias cargadas.
- Posiciones y datos de cuenta.

También incluye dibujos de soportes y resistencias, Fibonacci y una aproximación visual ABCD.

Esos dibujos no intervienen automáticamente en las condiciones de las estrategias.

## Parámetros generales

| Parámetro | Valor predeterminado |
|---|---:|
| `AutoEjecutar` | `true` |
| `MaxDrawdownPct` | `30.0` |
| `HabilitarNoticias` | `true` |
| `RefreshNewsMin` | `30` |
| `UsarTrailing` | `true` |
| `TrailingPips` | `15.0` |
| `UsarBreakeven` | `true` |
| `BreakevenPips` | `15.0` |
| `MultiplicadorLote` | `1.5` |
| `BarrasAnalisis` | `200` |
| `FuerzaSwing` | `5` |
| E1, E2 y E3 activas | `false` |

Los valores describen el archivo publicado. No son recomendaciones.

## Instalación para pruebas

1. Abrir MetaTrader 4 en una cuenta demo.
2. Seleccionar **Archivo → Abrir carpeta de datos**.
3. Copiar `STRATOS.mq4` dentro de `MQL4/Experts`.
4. Abrirlo en MetaEditor y compilar.
5. Revisar errores y advertencias.
6. Cargar el EA en un gráfico.
7. Configurar los nombres exactos de los símbolos del bróker.
8. Revisar y activar la estrategia que se desea evaluar.

Para probar noticias, autorizar la dirección configurada en `NewsURL` dentro de las opciones de Expert Advisors del terminal.

El procesamiento depende de los ticks del gráfico principal.

## Ejemplo de configuración

La primera estrategia incluye como punto de partida:

- Símbolo: `EURUSD`.
- Condición: RSI de 14 períodos menor que 30.
- Acción: comprar.
- Lote base: `0.01`.
- TP configurado: `30` pips.
- SL configurado: `20` pips.

Es una configuración ilustrativa del archivo, no una estrategia validada.

## Estado y limitaciones conocidas

Puntos identificados mediante lectura del código:

- **Número de estrategias:** existen estructuras para cinco, pero esta versión carga tres desde los parámetros.
- **Acumulación:** puede abrir una operación por barra mientras persista la condición, sin máximo de órdenes configurado.
- **Multiplicación de lote:** depende del orden de recorrido, sin seleccionar explícitamente la operación más reciente.
- **Lotes:** el redondeo por decimales no garantiza ajuste a todos los pasos permitidos.
- **Cierres entre estrategias:** cada espacio tiene un número mágico distinto; una regla de cierre no administra automáticamente las entradas de otro espacio.
- **Gestión compartida:** el rango de números mágicos del trailing puede abarcar operaciones de otras instancias.
- **Noticias:** el parser presupone un formato de fecha y no realiza conversión explícita entre la zona horaria del calendario y la del servidor.
- **Formato XML:** requiere revisar tratamiento de CDATA y formatos alternativos.
- **Respuesta HTTP:** no valida de forma completa el estado HTTP ni la integridad del calendario antes de marcar la consulta como correcta.
- **Datos de noticias:** un fallo de actualización puede dejar información previa; una lista vacía permite continuar sin ese bloqueo.
- **WebRequest:** la consulta es síncrona y puede demorar el procesamiento del EA.
- **Monitor:** la evaluación de condiciones se realiza durante la ejecución de estrategias; al pausar o bloquearlas, sus valores pueden quedar desactualizados.
- **Tolerancias:** “igual” y “toca” utilizan valores absolutos que no se adaptan a la escala de cada indicador.
- **Stops y unidades:** deben validarse las conversiones de pip y las restricciones del contrato; algunos valores pueden dejar SL o TP en cero.
- **Ejecución:** faltan comprobaciones integrales de margen, datos históricos y resultados de solicitudes.

Los indicadores del panel utilizan parámetros propios y no necesariamente coinciden con los de cada estrategia.

## Resultados y validación

La documentación se basa en la lectura del código fuente. No acredita compilación, ejecución ni resultados de backtesting.

El repositorio no incluye un historial auditado ni una validación independiente de rendimiento.

## Autor

Desarrollado por **Emanuel**, neuropsicólogo y desarrollador del proyecto **WineBOX**.

- https://winebox.cloud
- https://wineboxtool.cloud

## Contribuciones

Se reciben reportes de errores y propuestas mediante Issues y Pull Requests.

Incluir versión de MetaTrader 4, símbolo, período del gráfico, parámetros y registros relevantes, sin credenciales ni datos personales.

## Licencia

Este proyecto se distribuye bajo la licencia **Apache License 2.0**.

Consultar el archivo [LICENSE](LICENSE) para conocer los términos completos.

## Aviso

Material técnico y educativo. No constituye asesoramiento financiero ni una promesa de resultados.
