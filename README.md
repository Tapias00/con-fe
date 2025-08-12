    MySQL 8 instalado y el servicio corriendo.

    Node.js 18+ y npm.

    (Opcional) Postman para probar la API.

Comprueba versiones (en PowerShell o CMD):

mysql --version
node -v
npm -v

1) Descomprimir el proyecto

    Descarga y descomprime daniel_tapias_ritchie.zip en una carpeta simple, por ejemplo:

        C:\proyectos\daniel_tapias_ritchie\project\

    Abre CMD o PowerShell en esa carpeta project\.

2) Crear la base de datos y las tablas (MySQL)

En la terminal, dentro de project\ ejecuta:

mysql -u root -p < sql\mysql_schema.sql

    Si mysql no se reconoce, usa la ruta completa, por ejemplo:
    "C:\Program Files\MySQL\MySQL Server 8.0\bin\mysql.exe" -u root -p < sql\mysql_schema.sql

Esto crea la BD pd_daniel_tapias_ritchie con todas las tablas, claves e índices.
3) Configurar el backend (API)

    Entra al backend:

cd backend-mysql

    Copia el archivo de entorno:

    PowerShell:

cp .env.sample .env

CMD:

    copy .env.sample .env

    Abre .env y revisa:

MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_USER=root
MYSQL_PASSWORD=TU_CLAVE
MYSQL_DATABASE=pd_daniel_tapias_ritchie
PORT=3001

4) Instalar dependencias y arrancar la API

npm install
npm run start

Deberías ver: MySQL API running on http://localhost:3001
5) Cargar datos desde CSV (carga masiva)

Abre otra terminal, entra a backend-mysql y corre:

cd C:\proyectos\daniel_tapias_ritchie\project\backend-mysql
node load_csv.js

    Es idempotente: si lo ejecutas de nuevo, no duplica.

6) Probar en el navegador (UI) y salud de la API

    UI (dashboard de Customers):
    http://localhost:3001

    Salud de la API:
    http://localhost:3001/api/health

7) (Opcional) Probar CRUD con Postman

Importa postman_collection.json (está en project\).
Endpoints clave (todos en http://localhost:3001):

    Customers: GET /api/customers, POST /api/customers, PUT /api/customers/:id, DELETE /api/customers/:id

    Invoices: GET /api/invoices, POST /api/invoices, PUT /api/invoices/:id, DELETE /api/invoices/:id

    Transactions: GET /api/transactions, POST /api/transactions, PUT /api/transactions/:id, DELETE /api/transactions/:id

Ejemplos de body (JSON):

Crear customer

{ "national_id": 123456789, "full_name": "Cliente Demo", "email": "demo@correo.com" }

Crear invoice (usa un customer_id existente)

{ "invoice_number": "INV-001", "billing_period": "2025-07", "amount_billed": 100000, "customer_id": 1 }

Crear transaction (asegúrate de tener platform_id, invoice_id, customer_id válidos)

{
  "transaction_code": "TX-001",
  "occurred_at": "2025-08-01 12:00:00",
  "amount": 50000,
  "status": "Aprobada",
  "type": "Pago",
  "platform_id": 1,
  "invoice_id": 1,
  "customer_id": 1
}

8) Parar y reiniciar

    Para detener la API: vuelve a la terminal donde corre y presiona Ctrl + C.

    Para reiniciar, ejecuta otra vez npm run start.

Problemas comunes (y solución rápida)

    mysql no se reconoce → usa la ruta completa a mysql.exe o agrega MySQL al PATH.

    Access denied al correr el .sql → verifica usuario/clave en .env y en el comando mysql -u root -p.

    Puerto 3001 en uso → cambia PORT en .env (p.ej., 3002) y vuelve a npm run start.

    FK impide borrar (invoice con transacciones) → primero elimina/actualiza las transacciones relacionadas (las FKs están en RESTRICT).

    Fechas: occurred_at debe ir como 'YYYY-MM-DD HH:MM:SS' (24h).
