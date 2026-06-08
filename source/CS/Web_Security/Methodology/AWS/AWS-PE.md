#reference #Ciberseguridad

**AWS Privilege Escalation** = abusar de permisos IAM mal configurados para pasar de credenciales limitadas a control total. La clave está en las **políticas IAM**: ciertos permisos permiten otorgarse más privilegios.

# Enumeración (¿qué puedo hacer?)

```sh
# Who am I?
aws sts get-caller-identity

# Políticas adjuntas a mi usuario
aws iam list-attached-user-policies --user-name <your-user>
aws iam list-user-policies --user-name <your-user>

# Las políticas inline revelan intención, no solo las managed
aws iam get-user-policy --user-name <your-user> --policy-name <policy-name>

# ¿Qué roles puedo asumir?
aws iam list-roles --query 'Roles[?AssumeRolePolicyDocument.Statement[?Principal.AWS]]'
```

# Vectores de escalada comunes

Permisos que permiten escalar (buscar en las políticas):

| Permiso peligroso | Abuso |
|-------------------|-------|
| `iam:CreateAccessKey` | Crear keys para otro usuario (más privilegiado). |
| `iam:CreatePolicyVersion` | Crear una versión de política con `*:*`. |
| `iam:AttachUserPolicy` | Adjuntarme `AdministratorAccess`. |
| `iam:PutUserPolicy` | Inyectar política inline admin. |
| `iam:PassRole` + `ec2:RunInstances` | Lanzar EC2 con un rol privilegiado. |
| `iam:PassRole` + `lambda:CreateFunction` | Ejecutar código con rol privilegiado. |
| `sts:AssumeRole` | Asumir un rol con más permisos. |
| `iam:UpdateAssumeRolePolicy` | Permitirme asumir un rol. |

```sh
# Ejemplo: política inline admin
aws iam put-user-policy --user-name me --policy-name esc \
  --policy-document '{"Version":"2012-10-17","Statement":[{"Effect":"Allow","Action":"*","Resource":"*"}]}'
```

# Herramientas

| Herramienta | Uso |
|-------------|-----|
| **enumerate-iam** | Forzar qué acciones permite la clave. |
| **Pacu** | Módulos de privesc IAM automatizados. |
| **PMapper** | Grafo de relaciones IAM y rutas de escalada. |
| **CloudGoat** | Lab para practicar. |

# Mitigación

Mínimo privilegio, evitar `iam:*` y `PassRole` amplios, revisar políticas inline, alertar sobre cambios IAM, usar permission boundaries.

# Recursos
### [[AWScli]] · [[!Proxy Bypass]]
### [Rhino Security — AWS IAM privesc](https://rhinosecuritylabs.com/aws/aws-privilege-escalation-methods-mitigation/) · [HackTricks Cloud — AWS](https://cloud.hacktricks.xyz/pentesting-cloud/aws-security)
