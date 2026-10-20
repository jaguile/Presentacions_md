---
marp: true
theme: default
paginate: true
---

# :one: PKI - Infraestructura de clau pública

:raising_hand: És el conjunt de normes, rols, procediments i tecnologia que permet:

- crear certificats digitals
- associar una clau pública amb una identitat
- verificar aquesta associació
- gestionar revocacions

---

# :one: PKI - Infraestructura de clau pública

:bulb:La PKI és el sistema que permet confiar que una clau pública realment pertany a qui diu pertànyer.

Perquè en criptografia:
- Generar claus és fàcil
- Confiar en elles és el difícil

---

# :two: Parts d'una PKI

- ## **Certificat digital**
- ## **Autoritat de Certificació (CA)**
- ## **Autoritat de Registre (RA)**
- ## **Autoritat de Validació (VA)**
- ## **Repositori**

---

# :three: Certificat digital

## És un document signat digitalment per **algú confiable**

Conté:
:key:Una clau pública 
:bust_in_silhouette:Una identitat (un domini, una persona, una empresa, ...)
:date:Dates de validesa
:office:La signatura d'una **autoritat certificadora**

---

# :three: Certificat digital

## **X.509** és l’estàndard internacional que defineix:

### El format i l’estructura dels certificats digitals dins d’una PKI.

```
Certificat
 ├─ Subject (qui és)
 ├─ Clau pública
 ├─ Emissor (CA)
 ├─ Validesa
 ├─ Extensions (Per exemple, per a què serveix)
 └─ Signatura de la CA
```

---

# :four: Autoritat de Certificació (CA)

## **És una entitat reconeguda i confiable**

- verifica identitats
- emet certificats
- els signa amb la seva clau privada

---

# :four: Autoritat de Certificació (CA)

**Per exemple**

- Quan entres a un web HTTPS:
    - El servidor envia el seu certificat
    - El navegador comprova:
    :white_check_mark:està signat per una CA?
    :white_check_mark:la CA és de confiança (tenim el certificat de la CA emmagatzemada al nostre navegador)? 
    :white_check_mark:Certificat de la web caducat?
    :white_check_mark:no està revocat?

👉 Si tot quadra → cadenat verd :green_circle: → s’estableix connexió xifrada (TLS)

---

# :five: Autoritat de Registre (RA)

- Verifica la identitat del sol·licitant
- Aprova o rebutja sol·licituds
- Fa la validació documental o presencial

👉 Actua com a “oficina d’atenció” de la CA.

**Per exemple**: Quan et treus un certificat emès per la FNMT has d'anar abans a demanar-lo a una oficina de l'agència tributària.

---

# :six: Autoritat de Validació (VA)

## **La VA respon consultes OCSP**
## **Conté una llista de certificats revocats - CRL**

- *CRL* (Certificate Revocation List)
- *OCSP* (Online Certificate Status Protocol)

Permeten saber si un certificat ja no és vàlid, si està revocat o si està caducat.

---

# :information_desk_person: Escenari: Maria vol fer la declaració de la renda online

## **Per identificar-se i signar digitalment, necessita un certificat digital personal.**

---

# PAS 1 - SOL·LICITUD DE CERTIFICAT

1. Entra a la web de FNMT
2. Des del seu ordinador genera un **parell de claus**
    - Clau privada (queda al seu ordinador ben guardada)
    - Clau pública (s'enviarà a la CA)
3. Enviament de la sol·licitud
    - Clau pública
    - DNI i dades personals
4. La FNMT genera un codi de sol·licitud

---

# PAS 2 - Autorització de Registre (RA)

1. Maria va presencialment a una oficina de l'Agència tributària (RA)
2. Mostra el DNI i verifiquen la seva identitat.

---

# PAS 3 - Emissió del certificat (CA)

1. La FNMT genera un certificat que conté
    - El nom i el DNI de Maria
    - La seva clau pública
    - Una data de validesa
    - La signatura de la FNMT (CA)

---

# PAS 4 - Ús del certificat 

1. Maria introdueix el certificat al seu navegador
2. Maria entra a la web de l'Agència tributària per fer la declaració de la renta.
3. Navegador envia certificat de Maria
4. Servidor comprova certificat
    - Signat per una CA?
    - Caducat? Revocat?
5. Si tot ok, fa la declaració.

---

# PAS 5 - Signatura de la declaració

1. Maria prem *Presentar declaración*
2. El sistema genera un *hash* de la declaració
3. El navegador demana accés a la clau privada (la tenim ben guardada al nostre ordinador)
4. Es genera la signatura
5. Servidor comprova signatura.

---

# Veure certificats emmagatzemats en el navegador

---

https://gist.github.com/rxaviers/7360908