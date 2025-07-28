# 🌳 ÁRBOL DE DECISIÓN ESTRUCTURADO PARA PREDICCIÓN DE PRECIOS
## Sistema de Predicción de Precios - Wayakit vs Competencia

---

## 📋 DESCRIPCIÓN DEL PROYECTO

Este sistema implementa un **árbol de decisión estructurado** para predecir precios de productos Wayakit basándose en la información de la competencia. El sistema está diseñado para servir como **base de entrenamiento para un modelo de Machine Learning** en la Fase 2 del proyecto.

### 🎯 Objetivos
- **Predicción automática** de precios para productos Wayakit
- **Comparación estructurada** con productos de la competencia
- **Generación de dataset** para entrenamiento de ML
- **Trazabilidad completa** de las decisiones tomadas

---

## 🏗️ ARQUITECTURA DEL SISTEMA

### **Jerarquía de Casos (de mayor a menor prioridad):**

1. **CASO 1:** Información Completa - Competencia Directa
2. **CASO 2:** Tipo de Producto Existe, pero Subindustria Diferente
3. **CASO 3:** Mismo Generic Product Type, Diferente Tipo de Producto
4. **CASO 4:** Tipo de Producto No Existe, pero Subindustria e Industria Sí Existen
5. **CASO 5:** Solo Existe la Industria
6. **CASO 6:** Información Insuficiente - Sin Predicción

---

## 📁 ESTRUCTURA DE ARCHIVOS

```
ai-pricingmodel/
├── decision_tree_pricing_model.py    # Sistema principal del árbol de decisión
├── test_decision_tree.py             # Script de pruebas y demostración
├── wayakit_products.csv              # Dataset de productos Wayakit
├── products_competition.csv          # Dataset de productos de competencia
├── predictions_dataset.csv           # Dataset generado para ML (se crea automáticamente)
└── README.md                         # Este archivo
```

---

## 🚀 INSTALACIÓN Y USO

### **Requisitos:**
```bash
pip install pandas numpy
```

### **Uso Básico:**
```python
from decision_tree_pricing_model import DecisionTreePricingModel

# Inicializar modelo
modelo = DecisionTreePricingModel('wayakit_products.csv', 'products_competition.csv')

# Predecir precio para un producto
resultado = modelo.predecir_precio('FP-SPO-00100', 1000)  # ID, volumen en ml

# Ver resultado
print(f"Caso: {resultado['caso']}")
print(f"Precio: {resultado['precio_predicho']} SAR")
print(f"Mensaje: {resultado['mensaje']}")

# Guardar dataset de predicciones
modelo.guardar_dataset_predicciones()
```

### **Ejecutar Pruebas:**
```bash
python test_decision_tree.py
```

---

## 🔧 FUNCIONALIDADES PRINCIPALES

### **1. Cálculos Previos Automáticos**
El sistema ejecuta automáticamente todos los cálculos necesarios:

- **Medianas del Caso 1:** Para cada combinación (tipo_producto, subindustria, industria)
- **Promedios por Subindustria:** Para calcular factores de ajuste
- **Factores de Ajuste:** Entre diferentes subindustrias
- **Medianas por Generic Product Type:** Separadas por canal (B2B/B2C)
- **Medianas Generales:** Por subindustria e industria

### **2. Árbol de Decisión Inteligente**
El sistema sigue una jerarquía clara de casos:

```python
# Ejemplo de flujo de decisión
if existe_tipo and existe_subindustria and existe_industria:
    # CASO 1: Competencia directa
elif existe_tipo and not existe_subindustria:
    # CASO 2: Subindustria diferente
elif not existe_tipo and existe_generic:
    # CASO 3: Generic product type
# ... y así sucesivamente
```

### **3. Dataset de Predicciones para ML**
Cada predicción se guarda automáticamente con:

- **Datos del producto Wayakit**
- **Verificación de existencia en competencia**
- **Valores de referencia usados**
- **Resultado de la predicción**
- **Metadatos de ejecución**

---

## 📊 ESTRUCTURA DEL DATASET DE PREDICCIONES

El archivo `predictions_dataset.csv` contiene:

| Campo | Descripción |
|-------|-------------|
| `wayakit_product_id` | ID del producto Wayakit |
| `wayakit_product_type` | Tipo de producto |
| `wayakit_generic_product_type` | Generic product type |
| `wayakit_subindustry` | Subindustria |
| `wayakit_industry` | Industria |
| `competition_product_type_exists` | ¿Existe tipo en competencia? |
| `competition_generic_type_exists` | ¿Existe generic type en competencia? |
| `case_executed` | Caso del árbol ejecutado (1-6) |
| `predicted_total_price_sar` | Precio total predicho |
| `predicted_price_per_ml` | Precio por ml predicho |
| `prediction_message` | Mensaje explicativo |
| `timestamp` | Fecha y hora de predicción |

---

## 🎯 CASOS DE USO DETALLADOS

### **CASO 1: Competencia Directa**
```python
# Cuando existe el mismo tipo de producto, subindustria e industria
resultado = {
    'caso': 1,
    'precio_predicho': 150.0,
    'mensaje': 'Precio basado en competencia directa - mediana de precios por ml/unidad: 0.150 SAR/ml'
}
```

### **CASO 2: Subindustria Diferente**
```python
# Cuando el tipo existe pero la subindustria es diferente
resultado = {
    'caso': 2,
    'precio_predicho': 132.0,
    'mensaje': 'Precio estimado por diferencia entre subindustrias - productos de Facilities Management son 12% más baratos que Home'
}
```

### **CASO 3: Generic Product Type**
```python
# Cuando no existe el tipo pero sí el generic product type
resultado = {
    'caso': 3,
    'precio_predicho': [115.0, 117.0],  # B2B y B2C
    'mensaje': 'Precio basado en la mediana generic product type - B2B: 115.00 SAR / B2C: 117.00 SAR'
}
```

---

## 🔍 MONITOREO Y ANÁLISIS

### **Estadísticas del Modelo:**
```python
stats = modelo.obtener_estadisticas()
print(stats)
# Output:
# {
#     'total_productos_wayakit': 150,
#     'total_productos_competencia': 2500,
#     'medianas_caso1_calculadas': 45,
#     'predicciones_realizadas': 75
# }
```

### **Análisis de Casos Ejecutados:**
```python
# Contar casos ejecutados
casos = {}
for prediccion in modelo.predictions_dataset:
    caso = prediccion['case_executed']
    casos[caso] = casos.get(caso, 0) + 1
```

---

## 🛠️ PERSONALIZACIÓN Y EXTENSIÓN

### **Agregar Nuevos Casos:**
```python
def _caso7_nuevo_caso(self, parametros):
    """Implementar nuevo caso de decisión"""
    # Lógica del nuevo caso
    return {
        'caso': 7,
        'precio_predicho': precio,
        'mensaje': 'Descripción del nuevo caso'
    }
```

### **Modificar Factores de Ajuste:**
```python
# En _calcular_factores_subindustria()
factor = self.promedios_subindustria[sub1] / self.promedios_subindustria[sub2]
# Ajustar rango según necesidades
factor = max(0.3, min(3.0, factor))  # Rango más amplio
```

---

## 📈 PREPARACIÓN PARA ML (FASE 2)

### **Features Generadas:**
- **Categóricas:** Tipo de producto, subindustria, industria, generic type
- **Numéricas:** Volumen, precios de referencia, factores de ajuste
- **Binarias:** Existencia en competencia (tipo, generic, subindustria, industria)
- **Target:** Precio predicho (para validación del modelo ML)

### **Validación Cruzada:**
```python
# El dataset de predicciones permite:
# - Validar precisión del árbol de decisión
# - Comparar con precios reales (cuando estén disponibles)
# - Identificar casos problemáticos
# - Optimizar parámetros del árbol
```

---

## 🚨 CONSIDERACIONES IMPORTANTES

### **Límites Mínimos:**
- **Caso 1:** Mínimo 3 productos para calcular mediana
- **Promedios:** Mínimo 5 productos por subindustria
- **Generic Type:** Mínimo 3 productos por canal

### **Factores de Ajuste:**
- **Rango limitado:** 0.5 - 2.0 para evitar valores extremos
- **Simetría:** factor(A,B) = 1/factor(B,A)

### **Manejo de Errores:**
- **Producto no encontrado:** Retorna error descriptivo
- **Datos insuficientes:** Pasa al siguiente caso
- **Sin información:** Caso 6 con referencias de otras industrias

---

## 🔄 MANTENIMIENTO Y ACTUALIZACIONES

### **Actualizar Datasets:**
```python
# Simplemente reemplazar los archivos CSV
modelo = DecisionTreePricingModel('wayakit_products_v2.csv', 'products_competition_v2.csv')
```

### **Regenerar Cálculos:**
```python
# Los cálculos se ejecutan automáticamente al inicializar
modelo._calcular_datos_previos()  # Si es necesario regenerar manualmente
```

---

## 📞 SOPORTE Y CONTACTO

Para preguntas o problemas con el sistema:

1. **Revisar logs:** El sistema imprime información detallada durante la ejecución
2. **Verificar datos:** Asegurar que los CSV tengan el formato correcto
3. **Ejecutar pruebas:** Usar `test_decision_tree.py` para diagnóstico

---

## 🎉 CONCLUSIÓN

Este sistema proporciona una **base sólida y estructurada** para la predicción de precios, con:

- ✅ **Trazabilidad completa** de decisiones
- ✅ **Dataset preparado** para ML
- ✅ **Escalabilidad** para nuevos casos
- ✅ **Robustez** en manejo de errores
- ✅ **Documentación** completa

**¡Listo para producción y preparado para la Fase 2 de ML!** 🚀 