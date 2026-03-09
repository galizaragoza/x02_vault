# **1. ¿Cómo conseguir la APK de Waze?**

**Método recomendado (más fiable para análisis):**

## **Usando Aurora Store (sin necesidad de cuenta Google):**

1. Descargar Aurora Store desde F-Droid o su web oficial
    
2. Abrir Aurora Store (funciona como cliente anónimo de Google Play)
    
3. Buscar "Waze"
    
4. Seleccionar versión estable más reciente
    
5. Descargar APK directamente
    

## **Alternativas:**

### **Desde dispositivo Android:**

bash

# Con ADB conectado (necesitas la app ya instalada)
adb shell pm path com.waze
adb pull /data/app/com.waze-1/base.apk waze.apk

### **Usando sitios web:**

- **APKMirror** (más confiable, verifica firmas)
    
- **APKPure**
    
- **Evozi APK Downloader**
    

### **Herramientas específicas:**

bash

# Usando gplaycli (requiere Python)
pip install gplaycli
gplaycli -d com.waze

# O con apkeep
apkeep -a com.waze .

**Recomendación para el proyecto:** Usar **APKMirror** o **Aurora Store** para garantizar que obtienes la versión oficial no modificada.

---

# **2. Resumen Conciso de Recomendaciones y Metodología**

## **APKs Recomendadas:**

### **A) Vulnerable Intencional:**

1. **Damn Vulnerable Hybrid Mobile App (DVHMA)** - Cubre múltiples vulnerabilidades OWASP
    
2. **InsecureBankV2** - Específico para bancos, muy completo
    
3. **OMTG-Android** - Del proyecto OWASP oficial
    

### **B) Repositorio No Oficial:**

- **Fuente:** APKPure o Aptoide
    
- **App ejemplo:** "Spotify Mod" o "YouTube Vanced"
    
- **Alternativa:** Cualquier app popular con versión "mod" o "premium unlocked"
    

### **C) Tienda Oficial:**

- **Waze** - Excelente opción, permisos sensibles (ubicación)
    
- **Alternativas:** Twitter Lite, Facebook Lite, aplicaciones de bancos regionales
    

---

## **Metodología Resumida:**

### **FASE 1: RECOLECCIÓN DE INFORMACIÓN**

**Propósito:** Reconocimiento inicial y definición de alcance  
**Herramienta óptima:** **MobSF** (análisis automático completo)  
**Metodología:**

1. Subir APK a MobSF
    
2. Revisar reporte automático de:
    
    - Permisos peligrosos
        
    - Componentes expuestos
        
    - Configuraciones inseguras
        
    - Librerías vulnerables
        
3. Definir puntos de enfoque para análisis profundo
    

### **FASE 2: ANÁLISIS ESTÁTICO**

**Propósito:** Encontrar vulnerabilidades en código sin ejecutar  
**Herramienta óptima:** **Jadx + MobSF + grep**  
**Metodología:**

1. **Jadx** para decompilar a código Java legible
    
2. **MobSF** para búsquedas automáticas de:
    
    - Hardcoded secrets
        
    - URLs inseguras (http://)
        
    - Algoritmos criptográficos débiles
        
3. **Búsquedas manuales** con grep:
    
    bash
    

grep -r "password\|key\|token\|secret" ./
grep -r "http://" ./ --include="*.java"

1. Revisar AndroidManifest.xml para componentes expuestos
    

### **FASE 3: ANÁLISIS DINÁMICO**

**Propósito:** Encontrar vulnerabilidades durante ejecución  
**Herramienta óptima:** **Frida/Objection + Burp Suite**  
**Metodología:**

1. **Configurar entorno:**
    
    - Emulador Genymotion con root
        
    - Burp Suite como proxy
        
    - Frida server instalado
        
2. **Análisis de tráfico:**
    
    - Interceptar comunicaciones
        
    - Bypass SSL pinning con Objection
        
3. **Instrumentación:**
    
    bash
    

objection -g com.paquete.app explore
android sslpinning disable
android root disable

1. **Pruebas específicas:**
    
    - Almacenamiento inseguro
        
    - Logs sensibles
        
    - Validación de entrada en formularios
        

---

## **Flujo de Trabajo Express:**

1. **Día 1:** MobSF → Reporte inicial → Jadx decompilación → Búsquedas básicas
    
2. **Día 2:** Configurar Genymotion + Burp → Interceptar tráfico → Objection/Frida
    
3. **Día 3:** Validar hallazgos → Documentar vulnerabilidades → Preparar reporte
    

---

## **Esencial Recordar:**

✅ **Triple verificación:** Estático + Dinámico + Manual  
✅ **Documentar todo:** Capturas, comandos, hallazgos  
✅ **Priorizar:** Enfocarse en OWASP Mobile Top 10  
✅ **Reproducible:** Usar herramientas open-source disponibles públicamente

Esta metodología garantiza cobertura completa mientras mantiene eficiencia para un proyecto académico.