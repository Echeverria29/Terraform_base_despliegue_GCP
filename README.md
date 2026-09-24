# 🚀 Modern Data Platform en GCP con Medallion Architecture, Terraform.

Repositorio de Infraestructura como Código (IaC) enfocado en la implementación automatizada de una plataforma de datos moderna en Google Cloud Platform (GCP). El proyecto implementa una **Arquitectura Medallion** en BigQuery.

---

## 🏗️ Diagrama de Arquitectura.

El flujo despliega de forma modular recursos de almacenamiento estructurado, procesamiento analítico, seguridad a nivel de columna y calidad de datos:

```mermaid
graph TD
    A[Terraform Root] --> B[Module: BigQuery Dataset]
    A --> C[Module: BigQuery Table & Schemas]
    A --> D[Module: BigQuery Routines / SP]
    A --> E[Module: BigQuery Views]

    B -->|Capas Medallion: Bronze / Silver / Gold
    C --> D
    C --> E
```

---

## 🏛️ Enfoque de Arquitectura Medallion

* **Capa Bronze (Ingesta Cruda):** Recepción de datos transaccionales con trazabilidad y esquemas versionados mediante JSON.
* **Capa Silver (Limpieza y Transformación):** Consolidación, limpieza de datos y estandarización mediante procedimientos almacenados (Stored Procedures).
* **Capa Gold(Negocio):** Modelado analítico y final para el consumo.

---

## 📂 Estructura del Proyecto

```text
.
├── IAC/
│   ├── environment/
│   │   └── dev/
│   │       └── env.tfvars.json        # Variables de entorno y configuración de despliegue
│   ├── modules/
│   │   ├── bigquery_dataset/          # Gestión de datasets por capas (Bronze, Silver, Gold, QA)
│   │   ├── bigquery_routine/          # Procedimientos almacenados SQL para transformaciones
│   │   ├── bigquery_table/            # Creación de tablas e inyección dinámica de esquemas JSON
│   │   ├── bigquery_view/             # Creación de vistas analíticas
│   ├── resources/
│   │   └── bigquery/                  
│   │       ├── routines/              # Scripts SQL de Stored Procedures
│   │       ├── schemas/               # Esquemas JSON tabulares (Bronze/Silver)
│   │       └── views/                 # Consultas de vistas de negocio
│   ├── main.tf                        # Orquestador principal de módulos Terraform
│   ├── provider.tf                    # Configuración del proveedor oficial de GCP
│   ├── variables.tf                   # Variables globales del sistema
│   └── outputs.tf                     # Salidas y outputs de recursos desplegados
├── LICENSE.md
└── README.md
```

---

## 🛠️ Prerrequisitos e Instalación (Ubuntu / WSL)

### 1. Instalar Terraform
Ejecuta los siguientes comandos en tu terminal de Ubuntu para configurar el repositorio de HashiCorp y realizar la instalación:

```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt-get update && sudo apt-get install terraform
```

### 2. Autenticación en Google Cloud (GCP)
Inicia sesión en tu cuenta y configura las credenciales por defecto (ADC):

```bash
gcloud auth login
gcloud auth application-default login
```

---

## ⚙️ Configuración y Despliegue

### 1. Habilitar APIs requeridas en el proyecto GCP
Activa los servicios necesarios para Data Catalog, BigQuery y Dataplex:

```bash
gcloud services enable bigquery.googleapis.com --project=NOMBRE_PROYECTO
```

### 2. Inicializar Terraform
Entra al directorio de infraestructura e inicializa los proveedores:

```bash
cd IAC
terraform init
```

### 3. Planificar la Infraestructura
Valida los cambios a desplegar utilizando el archivo de variables del entorno:

```bash
terraform plan -var-file="environment/dev/env.tfvars.json"
```

### 4. Aplicar el Despliegue
Ejecuta la creación de recursos en GCP:

```bash
terraform apply -var-file="environment/dev/env.tfvars.json"
```

---
