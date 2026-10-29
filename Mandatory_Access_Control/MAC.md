---
marp: true
theme: custom
paginate: true
---

# AppArmor
## Una implementació de Mandatory Access Control

---

# Índex

0. ACL vs MAC

---
# :zero: ACL vs MAC

## Mandatory Access Control (MAC) ofeix una capa més de control d'accés als recursos del sistema

## Serveix per controlar a què accedeixen les aplicacions

**Exemple**

> Sense MAC, Si jo sóc `root`, qualsevol aplicació que executi podria tenir accés a tot.

> Amb MAC, puc engabiar aquesta aplicació i controlar l'accés als seus recursos encara que l'usuari que llença l'palicació tingui permisos o acl amb privilegis.

---

# :zero: ACL vs MAC

| Concepte | acl i permisos | MAC
|----------|----------------|----|
| **Filosofia**| "Són els meus fitxers, jo decideixo qui entra."| "El sistema operatiu decideix què pot fer cada programa." |
| **Qui té el poder**| El propietari del fitxer i qui tingui permisos| L'administrador del sistema |


---

# :zero: ACL vs MAC

| Concepte | acl i permisos | MAC
|----------|----------------|----|
| **En què es basa**| En la identitat de qui llança el programa.| En regles estrictes per a l'aplicació  |
| **Principal perill**| Si un virus infecta un programa que has obert tu, té el mateix accés que tu.| Si un virus infecta un programa obert per tu, el virus es queda en una "gàbia"   |

---

# :one: AppArmor

## AppArmor és un mòdul de seguretat del nucli (kernel) de Linux que serveix per limitar les accions que pot fer un programa.

## AppArmor utilitza un sistema de control d'accés obligatori (MAC).

---

<!-- _class: important -->

# :one: AppArmor

## Encara que executis un programa com a root (superusuari), si AppArmor diu que aquest programa no pot tocar la carpeta Documents, el programa serà bloquejat.

---

# :two: AppArmor. Conceptes clau

## 1. Els perfils

> Els perfils són fitxers de text pla que es troben a la ruta /etc/apparmor.d/. 

> Per exemple, el perfil per al navegador web es pot dir usr.bin.firefox. Dins d'aquest fitxer s'especifica de forma molt senzilla quins fitxers pot llegir (r), quins pot escriure (w) o quins pot executar (x).

---

# :two: AppArmor. Conceptes clau

## 2. Els modes de treball de AppArmor

Quan apliques un perfil a un programa, aquest pot estar en un d'aquests dos estats:

* **Mode Complaining (Aprenentatge/Proves)**: El programa té llibertat total, però AppArmor registra en un log (un historial) tot el que fa. 
* **Mode Enforcing (Bloqueig/Estricte)**: AppArmor aplica les regles de forma estricta. Si el programa intenta fer una acció que no està escrita al seu perfil, el bloqueja. 

---

# :two: AppArmor. Conceptes clau

## 3. Comandes bàsiques de gestió

> Veure aplicacions que tenen perfil a AppArmor
```bash
sudo aa-status
```
> Crear un perfil de manera interactiva per una aplicació
```bash
sudo aa-genprof <aplicació>
```
> Posa un programa en mode aprenentatge (1) o estricte (2)
```bash
sudo aa-complain <aplicació>    # (1) mode aprenentatge
sudo aa-enforce <aplicació>     # (2) mode estricte
```
