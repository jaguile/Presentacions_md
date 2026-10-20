---
marp: true
theme: custom
paginate: true
---

# :one: Iptables

**Definició:**

- Les iptables són una eina que permet configurar les regles de filtratge del tallafoc que implementa el Kernel Linux. 

- Per poder fer operacions amb les iptables és necessari tenir privilegis d’administració del sistema, o fent servir l’usuari primari (root user) o fent servir permisos donats per l’administrador de l’equip mitjançant sudo.

---

# :two: Com generem una regla

## Sintaxi general d'una regla

```bash
iptables -t <taula> -A <cadena> <opcions de filtrat> -j <acció>
```

---

# :two: Com generem una regla

## Com esborrar regles anteriors

```bash
iptables -t <taula> -F
```

Exemple

```bash
iptables -F     # equivalent a iptables -t filter -F
iptables -t nat -F
```

---

<!-- _class: important -->

# :three: Taules 

```bash
iptables -t <taula> -A <cadena> <opcions de filtrat> -j <acció>
```

Iptables separa cada regla segons la seva funcionalitat i ho fa separant-les en taules. Exemple de taules que existeixen a `iptables` (les que més ens interessa). Per ordre de procesat:

1. **Filter**    - Taula per defecte. Si no diem la taula, és la *filter*
2. **nat**       - Taula que gestiona les regles **NAT**

## EN AQUESTA PRESENTACIÓ ENS CENTRAREM EN LA TAULA FILTER

---

# :five: Taula Filter

```bash
iptables -t <taula> -A <cadena> <opcions de filtrat> -j <acció>
```

## CADENA

Una cadena en una taula és una agrupació de regles d'un determinat tipus. **A la taula filter**, aplica regles de filtratge sobre un paquet. 

Cadenes a la taula filter (per ordre de procesament):

1. Entra al firewall - cadena **INPUT**
2. Surt del firewall - cadena **OUTPUT**
3. Passa pel firewall - cadena **FORWARD** 

---

# :five: Taula Filter

```bash
iptables -t <taula> -A <cadena> <opcions de filtrat> -j <acció>
```
## ACCIONS

Accions que podem fer sobre un paquet

- El deixem passar - **ACCEPT**
- El descartem - **DROP**
- El rebutgem i avisem (icmp) - **REJECT**

---

# :five: Taula Filter

```bash
iptables -t <taula> -A <cadena> <opcions de filtrat> -j <acció>
```
## OPCIONS DE FILTRAT

Algunes opcions útils

- `-s (IP origen) / -d (IP destinació)`: especificar IP d’origen/destí. 
- `-i (interfície d’entrada) / -o (interfície de sortida)`.
- `-p (protocol)`: TCP, UDP, ICMP, ALL, etc.
- `--sport / --dport`: port d’origen/destí.

---

# :five: Taula Filter

```bash
iptables -t <taula> -A <cadena> <opcions de filtrat> -j <acció>
```
## OPCIONS DE FILTRAT

Opció per veure la relació que té el paquet amb una connexió:

- `-m  conntrack --ctstate <llista d'estats>`
    - **NEW**: El paquet inicia una connexió
    - **ESTABLISHED**: El paquet és una resposta o és dins d'una connexió establerta
    - **RELATED**: El paquet no és de la connexió, però està relacionat (exemple: FTP data). 

---

# :five: Taula Filter

```bash
iptables -t <taula> -A <cadena> <opcions de filtrat> -j <acció>
```
## EXEMPLE opció -m

```bash
iptables -P INPUT DROP
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -m conntrack --ctstate NEW -p tcp --dport 22 -j ACCEPT
```

---

<!-- _class: important -->

# :six: Taula Filter

```bash
iptables -t <taula> -A <cadena> <opcions de filtrat> -j <acció>
```
**Exemples**

```bash
iptables -A INPUT -s 10.0.0.0/8 -p udp --dport 138 -j ACCEPT
```

## SI NO S'ESPECIFICA TAULA (`-t <taula>`) ÉS LA TAULA PER DEFECTE

---

# :six: Taula Filter

## Exemples

```bash
iptables –A INPUT --dport 53 –j REJECT
```

---

# :six: Taula Filter

## Com configurar una política

### Política permisiva

A l'inici de l'script:

```bash
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
```

---

# :six: Taula Filter

## Com veure el llistat TOTAL de regles configurades

```bash
iptables -t <taula> -L
iptables -t <taula> -L -n   # No resol DNS
```

**Exemple**:

```bash
iptables -t nat -L -n
iptables -L -n              # taula filter
```

---

# :six: Taula Filter

## Còmput de paquets que fan match amb alguna regla

```bash
iptables -t <taula> -L -v -n
```

**Exemple**:

```bash
iptables -t -L -v -n
```

---


# :six: Taula Filter

## Còmput de paquets que fan match amb alguna regla


```bash
iptables -t -L -v -n

Chain INPUT (policy DROP 10 packets, 800 bytes)
 pkts bytes target     prot opt in  out  source      destination
   15  1200 ACCEPT     tcp  --  *   *    192.168.1.50  anywhere tcp dpt:22
    8   640 DROP       tcp  --  *   *    anywhere      anywhere tcp dpt:23
```

- `-v` mostra nompre de paquets, nombre de bytes, cadena, ...
- Podem afegir `--line-numbers` per veure números de línia
- `iptables -Z INPUT` per posar comptadors a zero (a una cadena o a totes si no especifiquem)

---

# :six: Taula Filter

## Exemple d'un script amb vàries regles

```bash
#!/bin/bash
# ARA NO MIREU AQUESTA LÍNEA DE SOTA:
echo 1 > /proc/sys/net/ipv4/ip_forward

# ==== REGLES IPTABLES ====
iptables -F
iptables -t nat F
iptables -t nat -A POSTROUTING -o enp0s3 -j SNAT  # NO INTERESSA ARA
iptables -P FORWARD REJECT
iptables -A FORWARD -s 172.16.0.0 -p tcp --dport 80 -j ACCEPT
iptables -A FORWARD -s 172.16.0.0 -p tcp --dport 53 -j ACCEPT
iptables -L -n
iptables -t nat -L -n
```

---

# :six: Taula Filter

## COM CREAR LES NOSTRES CADENES

Podem crear les nostres pròpies cadenes i invocar-les des d'una cadena *pare*. Exemple:

```bash
# 1. Creem cadena CHECK_SSH a la taula filter
iptables -N CHECK_SSH

# 2. Afegim regla a la nostra cadena
iptables -A CHECK_SSH -s 192.168.1.50 -p tcp --dport 22 -j DROP

# 3. Si arriba un paquet i no fa match amb la regla anterior, tornem a la cadena que ens ha cridat
iptables -A CHECK_SSH -j RETURN

# 4. Cridem a la cadena CHECK_SSH des de la cadena INPUT
iptables -A INPUT -p tcp -j CHECK_SSH
```


--- 

https://gist.github.com/rxaviers/7360908