# Kubernetes The Hard Way - Ansible Automation 🚀

Este proyecto automatiza por completo el despliegue del famoso laboratorio [Kubernetes The Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way) de Kelsey Hightower utilizando **Ansible**.

La meta es transformar un proceso manual tedioso en un despliegue reproducible, modular y robusto.

## 🏗️ Arquitectura del Proyecto

El despliegue está organizado en roles de Ansible para mayor claridad y mantenibilidad:

- **common**: Configuración base de los nodos (hostname, hosts, rsync).
- **jumpbox_setup**: Preparación del entorno de control, descarga de binarios y clonación del repo KTHW.
- **pki**: Generación automática de la CA y todos los certificados TLS.
- **kubeconfig**: Generación de los archivos de configuración para todos los componentes.
- **encryption**: Creación de la clave de encriptación y configuración de secretos.
- **etcd**: Bootstrapping del cluster de base de datos distribuida.
- **control_plane**: Instalación y configuración del API Server, Scheduler y Controller Manager.
- **worker**: Configuración de nodos worker (`containerd`, `kubelet`, `kube-proxy`).
- **kubectl_config**: Configuración de acceso remoto desde el jumpbox.
- **pod_routes**: Configuración de rutas estáticas para la red de Pods.

## 🚀 Cómo empezar

### Requisitos previos
1. Tener Ansible instalado en tu máquina local.
2. Acceso SSH a todos los nodos (Jumpbox, Controllers y Workers).
3. Actualizar el archivo `inventory.ini` con las IPs de tus máquinas.

### Despliegue Completo (Cero a Héroe)
Para ejecutar todo el proceso de punta a punta:

```bash
ansible-playbook -i inventory.ini site.yml
```

### Ejecución por Etapas
Si preferís ir paso a paso:

1. **Base**: `ansible-playbook -i inventory.ini 00_setup_common.yml`
2. **Jumpbox**: `ansible-playbook -i inventory.ini 01_setup_jumpbox.yml`
3. **PKI/TLS**: `ansible-playbook -i inventory.ini 03_setup_pki.yml`
4. ... (y así sucesivamente con los playbooks numerados)

## 🛠️ Decisiones de Diseño Importantes

- **Eficiencia de Memoria**: Usamos el módulo `synchronize` (rsync) para distribuir los binarios grandes de Kubernetes, evitando errores de Out-Of-Memory (OOM) comunes con `copy` o `fetch`.
- **Idempotencia**: Todas las tareas están diseñadas para ejecutarse varias veces sin causar efectos secundarios no deseados.
- **Modularidad**: Cada componente de K8s tiene su propio rol, lo que facilita el troubleshooting.

## 📜 Referencias
Basado en [Kubernetes The Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way).
