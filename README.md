# Session 4 - Split Flask App

## Run locally
```bash
pip install -r requirements.txt
python -m src.main
```

## Predict
```bash
curl -X POST http://127.0.0.1:8080/api/predict -H "Content-Type: application/json" -d '{"instances":[{"Age": 35, "EstimatedSalary": 45000}]}'
```
---

## Nota sobre `python -m`
Puedes iniciar la aplicación de dos formas:

1. **Recomendada (con `-m`):**
   ```bash
   python -m src.main
   ```
   Esto ejecuta el módulo `src.main` dentro del paquete `src`. Es más robusto porque respeta la estructura de paquetes de Python y asegura que los `import` relativos funcionen correctamente.

2. **Alternativa (directo al archivo):**
   ```bash
   python src/main.py
   ```
   Esto también funciona, pero en algunos casos puede dar problemas con `import` relativos, ya que se ejecuta el archivo como script independiente.

👉 Usa siempre `python -m` cuando tu proyecto está organizado en paquetes (como aquí, con `src/`). 
## Entrenamiento vía API
### Entrenar con datos sintéticos (fallback)
```bash
curl -X POST http://127.0.0.1:8080/api/train -H "Content-Type: application/json" -d '{}'
```

### Entrenar desde CSV (columnas esperadas: Age, EstimatedSalary, y target Purchased/label)
```bash
curl -X POST http://127.0.0.1:8080/api/train   -H "Content-Type: application/json"   -d '{"csv_path":"data/SocialNetworkAds.csv"}'
```

### Entrenar desde JSON inline
```bash
curl -X POST http://127.0.0.1:8080/api/train   -H "Content-Type: application/json"   -d '{"instances":[{"Age":35,"EstimatedSalary":45000,"label":0},
                    {"Age":52,"EstimatedSalary":120000,"label":1}]}'
```

## Uso con Docker
### Construir imagen
```bash
docker build -t ml-session4:latest .
```
### Ejecutar contenedor
```bash
docker run --rm -p 8080:8080 -e MODEL_PATH=/app/model.pkl ml-session4:latest
```
### Reentrenar dentro del contenedor
```bash
curl -X POST http://127.0.0.1:8080/api/train -H "Content-Type: application/json" -d '{}'
```
*(El nuevo modelo sobrescribe `/app/model.pkl`. Monta un volumen para persistirlo:)*
```bash
docker run --rm -p 8080:8080 -v $(pwd)/artifacts:/app -e MODEL_PATH=/app/model.pkl ml-session4:latest
```
