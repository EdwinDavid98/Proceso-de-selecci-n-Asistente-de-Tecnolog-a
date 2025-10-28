# 🧩 B) SQL – Modelo mínimo y consultas analíticas (40 pts)

## 📘 Enunciado

Diseñar y ejecutar un modelo relacional mínimo con las siguientes tablas y datos de ejemplo:

```sql
CREATE TABLE DimPais (
  PaisCode VARCHAR(10) PRIMARY KEY,
  PaisNombre VARCHAR(100)
);

INSERT INTO DimPais VALUES 
('EC','Ecuador'),
('CO','Colombia'),
('PE','Peru');

CREATE TABLE Ventas (
  FactID INT IDENTITY PRIMARY KEY,
  PaisCode VARCHAR(10) NOT NULL,
  Fecha DATE NOT NULL,
  NetUSD DECIMAL(18,2) NOT NULL,
  TaxUSD DECIMAL(18,2) NOT NULL,
  FOREIGN KEY (PaisCode) REFERENCES DimPais(PaisCode)
);

INSERT INTO Ventas (PaisCode, Fecha, NetUSD, TaxUSD) VALUES
('EC','2024-05-01',1000,120),
('CO','2024-05-02',650,123.5),
('PE','2024-05-03',800,144),
('EC','2024-05-10',900,108);
```

---

## 🧱 1️⃣ Creación de tablas

Se generaron dos tablas relacionales:

- **DimPais**: tabla de dimensión con los códigos y nombres de país.  
- **Ventas**: tabla de hechos con ventas netas y montos de impuesto.

### 🔍 Modelo relacional

| Tabla | Descripción | Clave primaria | Clave foránea |
|--------|-------------|----------------|----------------|
| DimPais | Catálogo de países | `PaisCode` | — |
| Ventas | Registro de transacciones | `FactID` | `PaisCode → DimPais` |

---

## 📊 2️⃣ Consultas analíticas

### 🔹 (1) Totales por país
```sql
SELECT
  p.PaisNombre,
  SUM(v.NetUSD) AS TotalNetUSD,
  SUM(v.TaxUSD) AS TotalTaxUSD,
  SUM(v.NetUSD + v.TaxUSD) AS TotalUSD
FROM Ventas v
JOIN DimPais p ON v.PaisCode = p.PaisCode
GROUP BY p.PaisNombre;
```

**Resultado:**

| PaisNombre | TotalNetUSD | TotalTaxUSD | TotalUSD |
|-------------|--------------|--------------|-----------|
| Colombia | 650.0 | 123.5 | 773.5 |
| Ecuador | 1900.0 | 228.0 | 2128.0 |
| Peru | 800.0 | 144.0 | 944.0 |

---

### 🔹 (2) Totales mes a mes (YYYY-MM) por país
```sql
SELECT
  p.PaisNombre,
  STRFTIME('%Y-%m', v.Fecha) AS Periodo,
  SUM(v.NetUSD) AS NetUSD_Mes,
  SUM(v.TaxUSD) AS TaxUSD_Mes
FROM Ventas v
JOIN DimPais p ON v.PaisCode = p.PaisCode
GROUP BY p.PaisNombre, STRFTIME('%Y-%m', v.Fecha)
ORDER BY p.PaisNombre, Periodo;
```

**Resultado:**

| PaisNombre | Periodo | NetUSD_Mes | TaxUSD_Mes |
|-------------|----------|-------------|-------------|
| Colombia | 2024-05 | 650.0 | 123.5 |
| Ecuador | 2024-05 | 1900.0 | 228.0 |
| Peru | 2024-05 | 800.0 | 144.0 |

---

### 🔹 (3) Ratio Tax/Net por país (evitando división por cero)
```sql
SELECT
  p.PaisNombre,
  ROUND(SUM(v.TaxUSD) / NULLIF(SUM(v.NetUSD), 0), 4) AS Ratio_Tax_Net
FROM Ventas v
JOIN DimPais p ON v.PaisCode = p.PaisCode
GROUP BY p.PaisNombre;
```

**Resultado:**

| PaisNombre | Ratio_Tax_Net |
|-------------|----------------|
| Colombia | 0.19 |
| Ecuador | 0.12 |
| Peru | 0.18 |

> 💡 Se usa `NULLIF(SUM(v.NetUSD), 0)` para evitar la división por cero en caso de que no existan valores de ventas netas.

---

### 🔹 (4) Creación de índice en la columna Fecha
```sql
CREATE INDEX idx_fecha_ventas ON Ventas(Fecha);
```

# **Resultado:**

<img width="1353" height="941" alt="image" src="https://github.com/user-attachments/assets/237470f9-e850-461d-aa8c-caf5cdf489a6" />


<img width="1359" height="940" alt="image" src="https://github.com/user-attachments/assets/6a47b595-a56a-49d8-8ad3-40e1753755ca" />


