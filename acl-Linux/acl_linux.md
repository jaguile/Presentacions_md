---

## marp: true theme: default paginate: true
marp: true
theme: custom-theme
paginate: true
backgroundColor: white

# 🪟 Permisos i sistemes de fitxers a Windows
---
# 🪟 Permisos i ACL a Linux
---
## 🔍 1. Què són les ACL?

Les **ACL (Access Control Lists)** permeten assignar permisos **més detallats** que els permisos bàsics de Linux (lectura, escriptura, execució per usuari, grup i altres).

Amb ACL pots:
- Donar permisos a **diversos usuaris o grups** sobre un mateix fitxer o carpeta.
- Afegir **permisos addicionals** sense canviar el propietari ni el grup del fitxer.
- Controlar permisos **heretats** a noves subcarpetes i fitxers.

👉 És com l’equivalent a les **ACL de Windows (pestanya de Seguretat → Avançat)**.

---

## ⚙️ 2. Activació i verificació del suport ACL

La majoria de sistemes Ubuntu **ja tenen el suport ACL activat**. L'escriptori isard que farem servir, per exemple, ja el té.

Per assegurar-te’n (en l'exemple, `/dev/vda2` és on volem aplicar les *acl*):

```bash
isard@ubuntu-24:~$ sudo tune2fs -l /dev/vda2 | grep "Default mount options"
Default mount options:    user_xattr acl
```

Si no hi és, pots muntar el sistema amb suport ACL:
```bash
sudo mount -o remount,acl /
```

I per fer-ho persistent (a `/etc/fstab`):
```
UUID=XXXX-XXXX  /  ext4  defaults,acl  0  1
```

---

## 📦 3. Instal·lació del paquet `acl`

Si no el tens instal·lat (no seria el nostre cas):

```bash
sudo apt update
sudo apt install acl
```

Això et permetrà fer servir les comandes `setfacl` i `getfacl`.

---

## 📂 4. Comandes principals

### 🔹 `getfacl`
Mostra la llista de control d’accés (ACL) d’un fitxer o carpeta.

```bash
getfacl nom_fitxer
```

Exemple (fitxer sense ACL extres):
```bash
$ getfacl informe.txt
# file: informe.txt
# owner: anna
# group: anna
user::rw-
group::r--
other::---
```
---
## 📂 4. Comandes principals

### 🔹 `getfacl`

Exemple (si hi ha ACL extres):
```bash
user:marta:r--
group:redaccio:rw-
mask::rw-
```

---

### 🔹 `setfacl`
Assigna o modifica permisos ACL.

Sintaxi bàsica:
```bash
setfacl -m u:usuari:permisos fitxer
setfacl -m g:grup:permisos fitxer
```

Exemples:
```bash
sudo setfacl -m u:marta:rw informe.txt
sudo setfacl -m g:redaccio:r-- informe.txt
```

Per veure el resultat:
```bash
getfacl informe.txt
```

---

### 🔹 Esborrar ACL

```bash
setfacl -x u:marta fitxer
```

Per eliminar **totes** les ACL:
```bash
setfacl -b fitxer
```

---

## 🧱 5. Permisos per defecte (herència)

Les carpetes poden tenir **ACL per defecte**, que s’apliquen automàticament a nous fitxers o subcarpetes creades dins d’elles.

Assignar un permís per defecte:
```bash
setfacl -m d:g:redaccio:rwX /directori
```

📘 `d:` vol dir *default*  
Així, qualsevol fitxer nou dins `/directori` tindrà permisos de lectura i escriptura per al grup `redaccio`.

---

## ⚙️ 6. Resum d'opcions de `setfacl`

<!-- _class: taula_gran -->

| Opció                 | Descripció                                                         | Efecte sobre ACL existents                           |
| --------------------- | ------------------------------------------------------------------ | ---------------------------------------------------- |
| `-m` / `--modify`     | **Afegeix o modifica** entrades concretes a l’ACL                  | 🔸 **Manté** la resta d’entrades (no les elimina)    |
| `-x` / `--remove`     | Elimina entrades concretes (usuari, grup, etc.)                    | 🔸 Només esborra el que especifiques                 |
| `--set`               | **Reemplaça completament** l’ACL actual per la que indiques        | ⚠️ **Substitueix tota l’ACL existent**               |
| `--set-file=<fitxer>` | Llegeix ACLs d’un fitxer generat amb `getfacl` i **les reemplaça** | ⚠️ **Sobreescriu** l’ACL amb el contingut del fitxer |
| `-b` / `--remove-all` | Elimina **totes** les ACLs (menys els permisos bàsics UNIX)        | ❌ ACL neta                                           |

---
## 🧪 7. Exemple

### Podem ficar vàries ACL en una mateixa comanda (per un mateix fitxer)

```bash
setfacl -m u:anna:rw,u:joan:r,g:professors:r,d:u:anna:rw,d:u:joan:r,d:g:professors:r projecte/
```
---
## 🧪 8. Exemple

### Podem imortar ALCs d'un fitxer

```bash
getfacl model.txt > acl_model.txt
setfacl --set-file=acl_model.txt exemple.txt
```
---

## 🧮 9. Interpretació de les ACL

Una sortida de `getfacl` pot ser:

```
# file: projecte/
# owner: anna
# group: anna
user::rwx
user:marta:r--
group::r-x
group:redaccio:rw-
mask::rw-
other::r--
default:user::rwx
default:group::r-x
default:other::---
```
---

## 🧮 9. Interpretació de les ACL

Explicació:
- `user::` → propietari
- `group::` → grup del fitxer
- `other::` → altres usuaris
- `user:marta:r--` → permís explícit per a usuari “marta”
- `group:redaccio:rw-` → permís explícit per al grup “redaccio”
- `mask::` → màscara: limita els permisos màxims efectius dels usuaris/grups ACL
- `default:` → permisos que s’heretaran a nous fitxers o subcarpetes

---

## 🧾 11. Resum ràpid de comandes útils

| Acció | Comanda |
|-------|----------|
| Veure ACL | `getfacl fitxer` |
| Afegir permís a usuari | `setfacl -m u:usuari:permisos fitxer` |
| Afegir permís a grup | `setfacl -m g:grup:permisos fitxer` |
| Esborrar permís concret | `setfacl -x u:usuari fitxer` |
| Netejar totes les ACL | `setfacl -b fitxer` |
| Afegir ACL per defecte | `setfacl -m d:u:usuari:permisos carpeta` |

---

> 💡 **Truc:** Pots combinar ACL amb permisos tradicionals (`chmod`) per tenir un control molt més fi.  
> Això permet, per exemple, que un usuari fora del grup del fitxer tingui accés sense alterar la propietat del fitxer.

---

## 💡 12. Notes útils

- Les ACL són complementàries als permisos UNIX bàsics (rwx per usuari, grup i altres).
- Encara que modifiquis ACLs, els permisos base (del chmod) sempre actuen com a límit.
- Les ACL per defecte (d:) només tenen sentit en carpetes, no en fitxers individuals.
f- Si un fitxer nou es crea dins d’una carpeta amb ACL per defecte, aquestes es copien automàticament.
