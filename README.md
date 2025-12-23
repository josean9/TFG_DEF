# TFG_DEF

## Métricas y *features* para LSTM de predicción cripto a corto plazo

### 1. Métricas de evaluación del modelo

Usar varias métricas en conjunto para evaluar bien el modelo:

- **RMSE (Root Mean Squared Error)**  
  - Estándar en series temporales; penaliza más los errores grandes.

- **MAE (Mean Absolute Error)**  
  - Promedio de errores absolutos; menos sensible a *outliers* que RMSE.

- **MAPE (Mean Absolute Percentage Error)**  
  - Mide el error porcentual; permite comparar entre distintas criptos o escalas.

- **R² (R-Squared)**  
  - Indica cuánta varianza explica el modelo (0–1); valores cercanos a 1 son mejores.

- **Directional Accuracy (DA)**  
  - Porcentaje de veces que el modelo acierta la dirección (sube/baja); muy relevante para trading.

- **Sharpe Ratio / retorno de la estrategia**  
  - Evalúa la estrategia basada en las predicciones (riesgo vs. retorno), no solo el error numérico.

---

### 2. *Features* de entrada recomendadas

#### 2.1 Datos básicos

Siempre incluir:

- **OHLC**: `open`, `high`, `low`, `close`.
- **Volumen de trading**.
- (Opcional) Número de trades, volumen *taker buy/sell* si el *exchange* lo ofrece.

#### 2.2 Indicadores técnicos clave (corto plazo)

Mezcla de tendencia + momentum:

- **RSI (Relative Strength Index)**  
  - Momentum; niveles típicos 30 (sobreventa) y 70 (sobrecompra).

- **SMA (Simple Moving Average)**  
  - Tendencia suave a medio plazo (ej. SMA 10, 20).

- **EMA (Exponential Moving Average)**  
  - Similar a SMA pero da más peso a los datos recientes (ej. EMA 5, 10).

- **MACD (Moving Average Convergence/Divergence)**  
  - Combinación de medias móviles para detectar cambios de tendencia y momentum.

- **Stochastic Oscillator (StochK, StochD)**  
  - Útil para reversiones a corto plazo en rangos laterales.

Indicadores secundarios (añadir solo si no saturan el modelo):

- Bollinger Bands (desviación estándar / volatilidad).
- ADX (fuerza de tendencia).
- CCI, Chaikin A/D, etc.

---

### 3. *Features* avanzadas (opcional pero útiles)

Incluir solo si tienes acceso a las fuentes de datos:

- **Volatilidad**:  
  - Desviación estándar móvil, ATR, etc.

- **Sentimiento**:  
  - *Sentiment score* agregado de Twitter, Reddit, noticias.

- **Métricas *on-chain*** (para BTC/ETH y similares):  
  - Número de transacciones, *hash rate*, direcciones activas, etc.

- **Datos macro / de otros mercados**:  
  - Índices bursátiles, tipos de interés, etc., si se busca correlación.

- **Google Trends / interés de búsqueda**:  
  - Interés público en el activo o palabras clave relacionadas.

- **Lag features**:  
  - Valor de precio/indicadores en `t-1`, `t-2`, …; a veces ayudan al LSTM a capturar dinámica local.

---

### 4. Configuración temporal y del modelo

#### 4.1 Ventana temporal (*lookback window*)

- Predicción de **1–5 velas** (ej. 1–5 días/horas):  
  - Usar ventana de **3–15 velas** como punto de partida.
- Regla práctica: ventana ≈ horizonte de predicción (ej. predices 5 días → usar ~5–15 días).
- Para *backtests* más serios: probar ventanas más largas (ej. 30–100 velas) y comparar resultados.

#### 4.2 Arquitectura LSTM (orientativa)

- 1–2 capas LSTM (ej. 64–256 unidades).
- `Dropout`: ~0.2 para reducir *overfitting*.
- Optimizador: `Adam`.
- Función de pérdida: `MSE` (o Smooth L1 si hay muchos *outliers*).
- Normalizar o estandarizar los *features* (muy importante para estabilidad del entrenamiento).

---

### 5. Limitaciones y buenas prácticas

- El mercado cripto es muy volátil y no lineal:  
  - El modelo puede funcionar bien en ciertos rangos de tiempo y romperse en eventos extremos.

- La mayoría de indicadores técnicos son **lagging**:  
  - Mejor para confirmar que para anticipar giros.

- Alto riesgo de **overfitting**:  
  - Usar *train/validation/test* separados por tiempo (no mezclar el futuro en el entrenamiento).
  - Monitorizar la diferencia entre error de entrenamiento y validación.

- Evaluar siempre la **estrategia de trading** (DA, Sharpe, *drawdown*) además de las métricas puramente estadísticas.

- No confiar únicamente en el modelo:  
  - Combinar con gestión de riesgo, *stop-loss*, *position sizing* y sentido común.
