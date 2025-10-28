# 🐍 **README -- ETL ligero y limpio con Python (40 pts)**

## 📋 **Enunciado**

Desarrollar un proceso ETL (Extract--Transform--Load) en **Python**
utilizando un archivo CSV con información de ventas.\
El proceso debe cumplir con los siguientes pasos:

1.  **Extraer** los datos desde el archivo `ventas.csv`.\
2.  **Limpiar** y estandarizar las columnas.\
3.  **Convertir** los montos a dólares (USD) según el tipo de cambio.\
4.  **Agregar** los resultados por país.\
5.  **Exportar** el archivo `resumen_pais.csv` y mostrar un log final
    del proceso.

------------------------------------------------------------------------

## 💾 **Archivo fuente (`ventas.csv`)**

``` csv
invoice_id,entity,country,issue_date,currency,net_amount,tax_amount
1,E001,ECUADOR,2024-05-03,USD,1000,120
2,E002,COLOMBIA,2024-05-04,COP,2500000,475000
3,E003,PERU,2024-05-05,PEN,3500,630
4,E001,ECUADOR,2024-05-06,USD,800,96
```

------------------------------------------------------------------------

## ⚙️ **Tipo de cambio**

``` python
fx_to_usd = {"USD":1.0, "COP":0.00026, "PEN":0.27}
```

------------------------------------------------------------------------

## 🧩 **Desarrollo del proceso ETL**

### 1️⃣ EXTRACT -- Lectura del CSV

``` python
import pandas as pd

print("Leyendo archivo ventas.csv...")
df = pd.read_csv("ventas.csv")
```

------------------------------------------------------------------------

### 2️⃣ CLEAN -- Limpieza y estandarización

``` python
print("Limpieza de datos...")
df.columns = df.columns.str.strip().str.lower()
df['country'] = df['country'].str.strip().str.upper()
df['currency'] = df['currency'].str.strip().str.upper()
```



| nvoice_id | entity | country  | issue_date | currency | net_amount | tax_amount |
|------------|---------|-----------|-------------|-----------|-------------|-------------|
| 1 | E001 | ECUADOR  | 2024-05-03 | USD | 1000 | 120 |
| 2 | E002 | COLOMBIA | 2024-05-04 | COP | 2500000 | 475000 |
| 3 | E003 | PERU     | 2024-05-05 | PEN | 3500 | 630 |
| 4 | E001 | ECUADOR  | 2024-05-06 | USD | 800 | 96 |

------------------------------------------------------------------------

### 3️⃣ TRANSFORM -- Conversión a USD

``` python
print("Convirtiendo montos a USD...")
df['net_usd'] = df.apply(lambda x: x['net_amount'] * fx_to_usd.get(x['currency'], 1), axis=1)
df['tax_usd'] = df.apply(lambda x: x['tax_amount'] * fx_to_usd.get(x['currency'], 1), axis=1)
df['total_usd'] = df['net_usd'] + df['tax_usd']
```
| nvoice_id | entity | country  | issue_date | currency | net_amount | tax_amount | net_usd | tax_usd | total_usd |
| --------- | ------ | -------- | ---------- | -------- | ---------- | ---------- | ------- | ------- | --------- |
| 1         | E001   | ECUADOR  | 2024-05-03 | USD      | 1000       | 120        | 1000.0  | 120.0   | 1120.0    |
| 2         | E002   | COLOMBIA | 2024-05-04 | COP      | 2500000    | 475000     | 650.0   | 123.5   | 773.5     |
| 3         | E003   | PERU     | 2024-05-05 | PEN      | 3500       | 630        | 945.0   | 170.1   | 1115.1    |
| 4         | E001   | ECUADOR  | 2024-05-06 | USD      | 800        | 96         | 800.0   | 96.0    | 896.0     |


------------------------------------------------------------------------

### 4️⃣ LOAD -- Agregación y exportación

``` python
print("Agregando totales por país...")
resumen = df.groupby('country')[['net_usd', 'tax_usd', 'total_usd']].sum().reset_index()

resumen.to_csv("resumen_pais.csv", index=False)
print("Archivo 'resumen_pais.csv' generado exitosamente.")
```

| country   | net_usd | tax_usd | total_usd |
|------------|----------|----------|-----------|
| COLOMBIA  | 650.0 | 123.5 | 773.5 |
| ECUADOR   | 1800.0 | 216.0 | 2016.0 |
| PERU      | 945.0 | 170.1 | 1115.1 |

------------------------------------------------------------------------

### 5️⃣ LOG -- Reporte del proceso

``` python
print("\n============== LOG DEL PROCESO ============")
print(f"Registros procesados: {len(df)}")
print("Países incluidos:", ", ".join(resumen['country']))
print("Totales por país:")
print(resumen)
print("==========================================")
```

------------------------------------------------------------------------

## 📊 **Resultados obtenidos**

### ➤ Archivo generado: `resumen_pais.csv`
``` text
  Country        Net_USD   Tax_USD   Total_USD
  -------------- --------- --------- -----------
  **COLOMBIA**   650.0     123.5     773.5
  **ECUADOR**    1800.0    216.0     2016.0
  **PERU**       945.0     170.1     1115.1
```
------------------------------------------------------------------------

### ➤ Log en consola

``` text
============== LOG DEL PROCESO ============
Registros procesados: 4
Países incluidos: COLOMBIA, ECUADOR, PERU
Totales por país:
country   net_usd   tax_usd   total_usd
COLOMBIA  650.0     123.5     773.5
ECUADOR   1800.0    216.0     2016.0
PERU      945.0     170.1     1115.1
==========================================
```

