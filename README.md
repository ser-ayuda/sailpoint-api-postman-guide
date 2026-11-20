# Guía Práctica: Pruebas de APIs REST de SailPoint Identity Security Cloud con Postman

Guía paso a paso para la configuración, autenticación OAuth 2.0 (Client Credentials Grant) y pruebas de integración con las APIs V3 de SailPoint Identity Security Cloud (ISC) utilizando Postman.

---

## 📋 Requisitos Previos

* **Postman Desktop** o **Postman Web**.
* Acceso a un *tenant* o entorno de pruebas de SailPoint Identity Security Cloud (ISC).
* Credenciales de acceso personal (**Personal Access Token - PAT**).

---

## 🚀 1. Generación del Personal Access Token (PAT)

1. En la consola de SailPoint ISC, dirígete a **Preferences** (Preferencias) > **Personal Access Tokens**.
2. Haz clic en **Create Token** y asigna un nombre descriptivo (ej. `Postman-Lab`).
3. Selecciona el alcance (**Scope**): `sp:scopes:all`.
4. Copia y guarda inmediatamente los dos valores generados:
   * **Client ID**
   * **Client Secret**

---

## 📦 2. Importación de la Colección de Postman

1. Descarga la colección oficial V3 de OpenAPI desde el portal de desarrolladores: [developer.sailpoint.com](https://developer.sailpoint.com).
2. En Postman, haz clic en **Import** y selecciona el archivo JSON descargado.

---

## ⚙️ 3. Configuración del Entorno (Environment) en Postman

Crea un nuevo entorno en Postman llamado `SailPoint-ISC-Env` con las siguientes variables:

| Variable | Valor de Ejemplo | Descripción |
| :--- | :--- | :--- |
| `tenant` | `mi-empresa` | Subdominio de tu tenant en SailPoint. |
| `baseUrl` | `https://{{tenant}}.api.identitynow.com` | URL base de la API V3. |
| `clientId` | `[Tu Client ID]` | Client ID del PAT. |
| `clientSecret` | `[Tu Client Secret]` | Client Secret del PAT. |
| `accessToken` | *(se completa automáticamente)* | Token Bearer devuelto por OAuth. |

---

## 🔐 4. Autenticación OAuth 2.0

* **Método**: `POST`
* **URL**: `https://{{tenant}}.api.identitynow.com/oauth/token`
* **Body (`x-www-form-urlencoded`)**:
  * `grant_type`: `client_credentials`
  * `client_id`: `{{clientId}}`
  * `client_secret`: `{{clientSecret}}`

Para guardar automáticamente el `access_token` en tu entorno de Postman, añade este código en la pestaña **Tests**:

```javascript
var jsonData = pm.response.json();
pm.environment.set("accessToken", jsonData.access_token);
```

---

## 🔍 5. Consultas Principales a la API V3

Añade el encabezado `Authorization: Bearer {{accessToken}}` a cada solicitud:

* **Listar Identidades**: `GET {{baseUrl}}/v3/identities`
* **Consultar Cuentas**: `GET {{baseUrl}}/v3/accounts`
* **Consultar Perfiles de Acceso**: `GET {{baseUrl}}/v3/access-profiles`
* **Consultar Roles (RBAC)**: `GET {{baseUrl}}/v3/roles`

---

## 🛠️ Diagnóstico de Errores Comunes

* **`401 Unauthorized`**: El token de acceso ha expirado. Vuelve a ejecutar la petición de autenticación `POST /oauth/token`.
* **`403 Forbidden`**: Revisa que el PAT tenga asignados los permisos o alcances adecuados (`sp:scopes:all`).
* **`429 Too Many Requests`**: Se ha superado el límite de peticiones simultáneas del gateway (100 peticiones por ventana).

---
*Autor: Jesús (`jcm_sailpoint`) | Docente & IAM Advocate*
