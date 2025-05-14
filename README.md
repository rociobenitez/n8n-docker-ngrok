# n8n Self-Hosted con Docker y Ngrok

Esta guía describe cómo configurar una instancia de n8n autoalojada utilizando Docker y Ngrok, permitiendo ejecutar n8n localmente sin necesidad de un servidor dedicado.

## Requisitos

- [Docker](https://www.docker.com/products/docker-desktop/)
- [Ngrok](https://ngrok.com/)
- [Homebrew](https://brew.sh/) (solo macOs)

## 1. Instalar Nngrok

Para [instalar Ngrok](https://dashboard.ngrok.com/get-started/setup/macos) en macOS:

```bash
brew install ngrok
```

Autentica tu cuenta en Ngrok:

```bash
ngrok config add-authtoken <tu_authtoken>
```

## 2. Crear un túnel con Ngrok

Elige **Static Domain** para desplegar la aplicación online:

```
ngrok http --url=<tu_subdominio>.ngrok-free.app 80
```

Anota la URL proporcionada por Ngrok (por ejemplo, `https://<subdominio>.ngrok-free.app`).

## 3. Configurar Docker para n8n

**Crea un directorio para tu proyecto y entrar en él:**

```bash
mkdir ~/n8n
cd ~/n8n
```

**Descarga la imagen de `n8n` desde Docker:**

- Abre **Docker Desktop** y ve a la pestaña **Images**.
- Busca la imagen oficial de n8n (`n8nio/n8n`) y haz clic en **Pull** para descargarla.

  ![Docker Desktop - Imagen para n8n](./src/image-n8n-docker.webp)

- Crea, dentro del directorio del proyecto, la carpeta `volume`.

  ```bash
  mkdir ~/n8n/volume
  ```

**Configura el contenedor con variables de entorno personalizadas:**

- Haz clic en **Run** para generar un contenedor basado en la imagen n8nio/n8n.

  ![Docker Desktop - Generar contenedor](./src/activar-imagen-n8n-docker.webp)

- En la ventana de configuración avanzada, especifica las siguientes opciones:

  ![Nuevo contenedor a partir de la imagen n8n](./src/new-container.webp)

  - **Nombre del contenedor:** `n8n-demo` _(puedes cambiarlo por el nombre que prefieras)_
  - **Puerto (Ports):** Asocia el puerto 5555 del contenedor con el puerto 5555 de tu máquina local (esto permitirá acceder a la app a través de localhost:5555).
  - **Volúmenes (Volumes):**

    - En **Host path**, selecciona la carpeta que has creado previamente (`~/n8n/volume`).
    - En **Container path**, ingresa la ruta `/home/node/.n8n` para que los datos de n8n se almacenen en el volumen.

  - **Variables de entorno (Environment variables):**

    - `N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true`
    - `N8N_EDITOR_BASE_URL=https://<tu_subdominio_ngrok>.ngrok-free.app` _(Reemplaza `<tu_subdominio_ngrok>` por el subdominio que Ngrok te proporciona)_
    - `WEBHOOK_URL=https://<tu_subdominio_ngrok>.ngrok-free.app`
    - `WEEBHOOK_TUNNEL_URL=https://<tu_subdominio_ngrok>.ngrok-free.app`
    - `N8N_DEFAULT_BINARY_DATA_MODE=filesystem`

    <br>
    > [!NOTE]
    > Es importante añadir `https://` al inicio de las URLs y asegurarte de poner el dominio proporcionado por Ngrok.

    <br>

- Haz clic en **Run** para crear el contenedor y ejecutarlo.

![Contenedor - Docker Desktop](./src/contenedor.webp)

## 4. Desplegar la aplicación con Ngrok

Abre la terminal y ejecuta el siguiente comando para exponer el puerto 5555 (o el que hayas especificado) de tu máquina local a Internet a través de Ngrok:

```bash
ngrok http --url=<tu_subdominio_ngrok>.ngrok-free.app 5555
```

> [!NOTE]
> Asegúrate de usar el mismo puerto que configuraste en el contenedor (en este caso, 5555).

Después de ejecutar el comando, Ngrok te proporcionará una URL (por ejemplo, https://`<tu_subdominio_ngrok>.ngrok-free.app`):

<img src="./src/terminal.webp" alt="Desplegar aplicación con Ngrok" width="500"/>

Ahora, puedes acceder a tu instancia de n8n en el navegador utilizando la URL de Ngrok proporcionada:

```arduino
https://<tu_subdominio_ngrok>.ngrok-free.app/home/workflows
```

**Regístrate en n8n** si es la primera vez que lo usas, y verás el **panel de Workflows** de n8n:

<img src="./src/Workflows-n8n.webp" alt="Panel de Workflows de n8n" width="600" style="margin-bottom: 24px;"/>

---

<br>

> **_¡Genial!_** 🎉 Ahora tienes una **instancia de n8n autoalojada** gracias Docker y Ngrok. Puedes empezar a crear tus flujos de trabajo (workflows) y automatizar procesos directamente desde tu máquina local sin necesidad de un servidor externo o dominio.
>
> **¿Qué podemos hacer ahora?** 🤔
>
> - Explorar opciones de personalización de n8n para integrar más herramientas y servicios.
> - Considerar migrar a un entorno de producción con un dominio fijo y un servidor dedicado.

<br>

## Referencias

- [Docker Installation](https://docs.n8n.io/hosting/installation/docker/)
- [n8n Docs](https://docs.n8n.io/)
- [n8n Docs - Deployment environment variables](https://docs.n8n.io/hosting/configuration/environment-variables/deployment/)
- [Ngrok](https://ngrok.com/)
- [Model Context Protocol](https://modelcontextprotocol.io/introduction)
- [Model Context Protocol - GitHub](https://github.com/modelcontextprotocol)
