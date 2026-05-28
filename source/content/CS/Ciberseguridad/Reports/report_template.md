# Prefacio
- ¿Qué es este documento?
- ¿Quién elabora este documento?
	- Información, experiencia técnica
- Definición del scope y límites
- Fecha de inicio

# Índice


# Resumen ejecutivo
- Que se pretendía
- Que se ha encontrado
- Como se ha encontrado
- Como se arregla
- Que significa eso a nivel operacional
- Valoración general del riesgo
- Diagnóstico general del ejercicio (¿Acciones urgentes?)
- Roadmap y puntos a mejorar
- Cumplimiento (normativas, certificados...)


# Contexto y metodología
- Cómo se ha llegado al momento del ejercicio
- De que información se parte
- Cuáles son los objetivos
- Cómo se pretende lograr esos objetivos
	- Lo que se va a intentar y lo que no
	- Cuál es la metodología
	- Herramientas utilizadas
	- Cuáles son los pasos previstos
		- OSINT
		- Reconocimiento
		- PoC
		- (Si procede por scope definido)
			- Social engineering
			- Evasión
			- Pivoting
			- Escalada
			- Persistencia
- Estructura y explicación de las distintas secciones del reporte


# Resumen técnico
- Tecnologías utilizadas
- Referencias
- Tabla de todas las vulnerabilidades/CWEs encontrados 

| Enlace | Gravedad                                                    | Síntesis | Codes                     | CVSS | Riesgo                                                                                       |
| ------ | ----------------------------------------------------------- | -------- | ------------------------- | ---- | -------------------------------------------------------------------------------------------- |
|        | Informativo<br>Leve<br>Medio<br>Grave<br>Urgente<br>Crítico |          | CVE<br>EUVD<br>CWE<br>KEV | 0-10 | Reducido(0-20%)<br>Moderado(20-40%)<br>Alto(40-60%)<br>Inminente(60-80%)<br>Crítico(80-100%) |


# Informe
## Diagnóstico general
- Claves del ejercicio
- Problemas a grandes rasgos
- Vectores comunes, puntos débiles


## Reconocimiento por fuentes abiertas
- DNS
- OSINT empleados
- OSINT empresa
- Motores de búsqueda

## Reconocimiento de la infraestructura
- Reconocimiento y mapeo de la infraestructura
	- DNS: Dominios, subdominios...
	- Servidores: IPs, ubicaciones, firewall, load balancer, certificados, fingerprinting...
	- Web: Tecnologías utilizadas, WAF, fingerprinting...
- Diagrama

## Vulnerabilidades halladas
### Máquina x (IP, dominio)
Resumen de como se llega a la máquina, que se identifica de ella y análisis a rasgos generales
#### Problema x

| Codes | Síntesis | CVSS |
| ----- | -------- | ---- |


- Evaluación de riesgo
- Gravedad
- Síntesis
- CWE/CVE asociado
- PoC
- Mitigación / prevención


# Conclusión
- Roadmap y cronograma
- Siguientes acciones


# Referencias y apéndice


# Glosario