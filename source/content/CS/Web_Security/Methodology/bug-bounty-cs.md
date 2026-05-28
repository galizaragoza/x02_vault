# Recon
## DNS
| Subdomain | Root Domain | TLD  | Name         |
| --------- | ----------- | ---- | ------------ |
| app       | .example    | .com | Example, Inc |
### Identificar subdominios en scope
- Wayback machine
- Censys
- Virustotal
- crt.sh
- Shodan
- **subfinder**

> [!important] Si el target es un wildcard, queremos encontrar también otros root domains que puedan no estar listados en el scope

```sh
subfinder [opciones] [dominio] -o output
## Obtener subdominios y guardarlos en una lista

cat output | puredns resolver | anew resolved
```

### DNS Fuzzing
Probar con subdomains conocidos puede revelear subdomains de los subdomains
```
puredns bruteforce [wordlist] [root domain]
```

### DNS Permutations
**admin**-sub.domain.tls
```
cat output | alterx | puredns resolve | anew resolved
```

### Root domain  discovery
- [Crunchbase](https://www.crunchbase.com/)
- Whois / reverse whois
- Whoxy
- Shodan

## Exposición
```sh
cat resolved | httpx -title -status-code -location -ip -cname -tech-detect | anew metadata 
```

- Eyewitness
- HTTP
- gowitness
- httpscreenshot

# Vulns
### Nuclei
### Fuzzing
### Wayback Machine
### OSINT