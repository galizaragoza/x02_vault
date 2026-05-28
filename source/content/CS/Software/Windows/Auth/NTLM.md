# NTLMv1 vs NTLMv2 vs Kerberos

|                                               | **NTLMv1**                 | **NTLMv2**                 | **Kerberos**                                                                 |
| --------------------------------------------- | -------------------------- | -------------------------- | ---------------------------------------------------------------------------- |
| **Security**                                  | Bad                        | Better                     | Best- no password is stored or sent over the network                         |
| **Performance**                               | Slower authentication      | Slower authentication      | Faster authentication                                                        |
| **Delegation Support**                        | Support just impersonation | Support just impersonation | Supports impersonation and delegation of authentication                      |
| **Multi-Factor Authentication – Smart Cards** | Does not support           | Does not support           | Support                                                                      |
| **Cryptography**                              | Symmetric cryptography     | Symmetric cryptography     | Supports both symmetric and asymmetric cryptography                          |
| **Trusted third party**                       | DC                         | DC                         | DC, KDC (and Windows Enterprise Certification Authority in Kerberos PKINIT). |
| **Mutual authentication**                     | Does not support           | Does not support           | Support                                                                      |
# Pass-the-Hash
Consiste en explotar el hábito de centralizar la administración utilizando una misma contraseña en cuentas privilegiadas
![[pass-the-hash.png|522]]
https://en.hackndo.com/pass-the-hash/#pass-the-hash
# NTLM relay
El atacante actúa como un nodo de relay, interceptando la primer solicitud del cliente habiendo logrado una posición MiTM, intercepta y reenvía todos los pasos de la autenticación al servidor, haciéndose pasar a ojos de este por cliente, y luego reenviando al cliente real los mensajes del servidor, efectivamente interceptando cada paso y robando las credenciales.
https://en.hackndo.com/ntlm-relay/
![[ntlm-relay.png|823]]

## Exploit
[ntmrelayx.py](https://github.com/fortra/impacket/blob/master/examples/ntlmrelayx.py)