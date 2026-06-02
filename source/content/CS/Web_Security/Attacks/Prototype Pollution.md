#reference #Web_Security

**Prototype Pollution** = vulnerabilidad de JavaScript en la que un atacante inyecta propiedades en `Object.prototype`. Como casi todos los objetos heredan de ese prototipo, la propiedad contaminada **aparece en todos los objetos** de la app → cambia su comportamiento (DoS, bypass de lógica, XSS, RCE).

# Causa

Funciones de *merge*/clonado recursivo o asignación por ruta (`obj[a][b]=c`) que no filtran las claves `__proto__`, `constructor`, `prototype`.

```js
// merge recursivo vulnerable
merge({}, JSON.parse('{"__proto__": {"isAdmin": true}}'));
({}).isAdmin   // -> true   ¡todos los objetos ahora tienen isAdmin!
```

# Vectores

| Vector | Cómo |
|--------|------|
| **Client-side** | Parámetros URL / JSON parseados en el navegador → gadget → DOM XSS. |
| **Server-side (Node)** | Body JSON en una API que hace deep merge → afecta a la lógica del servidor. |

Payloads habituales:

```jsonc
{"__proto__": {"polluted": "yes"}}
{"constructor": {"prototype": {"polluted": "yes"}}}
```
```
?__proto__[admin]=true
?constructor[prototype][admin]=true
```

# De polución a impacto (gadgets)

La polución por sí sola no hace daño; necesita un **gadget**: código que luego lee esa propiedad.

| Gadget | Impacto |
|--------|---------|
| Opciones de plantilla / sink HTML | **XSS** (client-side) |
| Flags de autorización (`isAdmin`) | **Bypass de lógica** |
| `child_process` opciones (`shell`, `NODE_OPTIONS`, `env`) | **RCE** (server-side) |
| Parámetros que rompen funciones internas | **DoS** |

# Detección

- Client-side: **DOM Invader** (Burp) detecta sources/gadgets automáticamente.
- Probar `?__proto__[test]=test` y comprobar `Object.prototype.test` en consola.
- Server-side: enviar `{"__proto__":{"json spaces":10}}` y ver si cambia el formato de la respuesta (gadget conocido de Express).

# Mitigación

`Object.freeze(Object.prototype)`, crear objetos sin prototipo (`Object.create(null)`), usar `Map` en vez de objetos como diccionarios, validar/filtrar claves `__proto__`/`constructor`/`prototype`, librerías de merge seguras.

# Recursos
### [PortSwigger — Prototype pollution](https://portswigger.net/web-security/prototype-pollution)
### [PayloadsAllTheThings — Prototype Pollution](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Prototype%20Pollution)
### [Snyk — lección](https://learn.snyk.io/lesson/prototype-pollution/?ecosystem=javascript) · [Imperva](https://www.imperva.com/learn/application-security/prototype-pollution/) · [DEFCON](https://youtu.be/gCVTbfDecwI)
