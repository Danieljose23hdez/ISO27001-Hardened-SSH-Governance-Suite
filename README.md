# 🛡️ ISO 27001 Hardened SSH Governance Suite

Este repositorio documenta el despliegue, hardening y auditoría de un servidor seguro bajo **Kali Linux**, implementando un entorno **Chroot Jail** para transferencias SFTP, denegación de consolas interactivas y su alineación directa con los controles de la norma **ISO/IEC 27001**.

---

## 🏛️ Mapeo de Controles ISO/IEC 27001

| Control ISO 27001 | Nombre del Control | Implementación Técnica en Kali Linux |
| :--- | :--- | :--- |
| **A.9.2.1** | Registro y alta de usuarios | Creación del usuario restringido `auditor_sftp` asignado al grupo `sftp_users`. |
| **A.9.2.6** | Denegación de acceso interactivo | Bloqueo de Shell mediante `/usr/sbin/nologin`. |
| **A.9.4.1** | Restricción de acceso a la información | Confinamiento en jaula Chroot (`ChrootDirectory /home/%u`). |
| **A.9.4.3** | Sistema de gestión de contraseñas | Canal cifrado de autenticación bajo SSH/SFTP (Puerto 22). |
| **A.10.1.1** | Políticas criptográficas | Protocolo de transferencia de archivos 100% cifrado. |
| **A.12.1.2** | Gestión de permisos y cambios | Estructura estricta de propietarios (`root:root` en raíz y `755`). |

---

## ⚙️ Configuración Aplicada (`/etc/ssh/sshd_config`)

```text
Match Group sftp_users
    ChrootDirectory /home/%u
    ForceCommand internal-sftp
    AllowTcpForwarding no
    X11Forwarding no

Esquema de Permisos en el Sistema
Plaintext
/home/auditor_sftp/         ──> Propietario: root:root         (Permisos: 755 - Lectura/Ejecución)
└── uploads/                ──> Propietario: auditor:sftp_users (Permisos: 755 / 775 - Escritura)

🧪 Evidencias Prácticas y Auditoría de Seguridad
1. Validación de Estructura de Permisos y Propiedad (Control A.12.1.2)
Verificación de que la jaula pertenece estrictamente a root y que únicamente la subcarpeta /uploads permite la escritura del auditor.

Resultado: Propiedad de root validada correctamente.

2. Verificación de Acceso SFTP Aislado (Control A.9.4.1)
El usuario autentica correctamente y queda restringido dentro de su directorio raíz asignado.

Resultado: Conexión concedida únicamente al entorno SFTP aislante.

3. Prueba de Denegación de Shell SSH Interactivo (Control A.9.2.6)
Intento de inicio de sesión interactivo a la consola Bash del sistema.

Resultado: Acceso rechazado por políticas de /usr/sbin/nologin.

🚀 Script de Automatización
Se adjunta el archivo setup_sftp.sh para el despliegue automatizado de usuarios, permisos y reglas de hardening en sistemas basados en Debian/Kali Linux.

---

## 🛠️ ¿Cómo subir las fotos en GitHub?

1. En tu repositorio, crea la carpeta `img` (o cuando subas los archivos, GitHub te deja seleccionar la carpeta).
2. Sube las 3 fotos asegurándote de que tengan **los mismos nombres que pusimos en la tabla**:
   * `evidence-permissions-chroot.png
<img width="847" height="45" alt="image" src="https://github.com/user-attachments/assets/0830a77b-311b-475e-96f8-903f9151c2cd" />

   * `evidence-sftp-success.png`
<img width="727" height="406" alt="image" src="https://github.com/user-attachments/assets/646e1af4-2c43-4691-a324-4edf62447da2" />

   * `evidence-ssh-denied.png` <img width="644" height="452" alt="image" src="https://github.com/user-attachments/assets/ffb080a3-51ea-40db-b9c0-8b804528d8d9" />

