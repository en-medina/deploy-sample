# GitOps & ArgoCD: from zero to production - Demo

Esta guía te guiará a través de la configuración de Minikube, la habilitación del controlador de Ingress NGINX, la implementación de ArgoCD y el despliegue de aplicaciones mediante ArgoCD.

## Requisitos previos

Asegúrate de tener instaladas las siguientes herramientas:

- **Minikube**: Instálalo siguiendo la [guía de configuración de Minikube](https://minikube.sigs.k8s.io/docs/start/)
- **Helm**: Instálalo siguiendo la [guía de instalación de Helm](https://helm.sh/docs/intro/install/)
- **Git Bash** (solo para usuarios de Windows)
- **Configuración de entradas DNS estáticas**: Sigue estas guías según tu sistema operativo:
  - [Windows](https://learn.microsoft.com/en-us/answers/questions/1689135/manually-adding-dns-resolution)
  - [Linux](https://stackoverflow.com/questions/19652555/add-static-dns-entry)
  - [MacOS](https://stackoverflow.com/questions/19652555/add-static-dns-entry)

## Paso 1: Habilitar el Controlador de Ingress NGINX en Minikube

Ejecuta el siguiente comando para habilitar el controlador de Ingress NGINX:

```sh
minikube addons enable ingress
```

Para verificar que el controlador de Ingress está habilitado, ejecuta:

```sh
minikube addons list
```

### Opcional: Ejecutar Minikube con un LoadBalancer

Si estás usando Minikube en un entorno de contenedores (como Docker), se recomienda exponer los servicios usando un túnel:

```sh
minikube tunnel
```

**Nota:** En Windows, esto podría fallar si el puerto 80 está en uso o bloqueado por el firewall.

---

## Paso 2: Desplegar ArgoCD

Instalaremos ArgoCD utilizando el Helm chart de la comunidad.

1. **Añadir el repositorio de Helm de ArgoCD:**

   ```sh
   helm repo add argo https://argoproj.github.io/argo-helm
   ```

2. **Crear un namespace para ArgoCD:**

   ```sh
   kubectl create ns argocd
   ```

3. **Instalar ArgoCD con Helm:**

   ```sh
   helm install -n argocd gitops argo/argo-cd --version 7.8.2 -f argocd/infra.deploy.yaml
   ```

4. **Crear una entrada DNS estática:** Agrega la siguiente entrada a tu archivo de hosts:

   ```
   127.0.0.1 argocd.example.com
   ```

5. **Verificar que ArgoCD sea accesible:** Asegúrate de poder conectarte a:

   ```
   https://argocd.example.com
   ```

---

## Paso 3: Desplegar Tu Aplicación

1. **Obtener las credenciales de inicio de sesión de ArgoCD:**

   - **Usuario:** `admin`
   - **Contraseña:** Ejecuta el siguiente comando para extraerla:

     ```sh
     kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode
     ```

2. **Desplegar el entorno de desarrollo:**

   ```sh
   kubectl apply -f argocd/dev.yaml
   ```

3. **Configurar entradas DNS estáticas para tu aplicación:**

   Agrega las siguientes entradas a tu archivo de hosts:

   ```
   127.0.0.1 login-app.dev.example.com
   127.0.0.1 login-app.qa.example.com
   127.0.0.1 login-app.prod.example.com
   ```

4. **Monitorear tu despliegue:**

   - Puedes hacer seguimiento al despliegue a través de la interfaz de ArgoCD.

---

### 🎉 ¡Todo listo! Ahora puedes usar ArgoCD para gestionar y desplegar tus aplicaciones de manera eficiente.
