## Administración de Redes Linux

# Entregable 5: LDAP Básico

[![CC BY-SA 4.0][cc-by-sa-shield]][cc-by-sa]

**`[GITT]` `[Sem 3.2]` `[LNA]` `(Spring 2025)`**

**Ingeniería en Tecnologías de Telecomunicación** | *Universidad Carlos III de Madrid*

---

## Introducción

Este documento contiene el registro del desarrollo de la actividad, incluyendo
las instrucciones principales, las decisiones, y los resultados.

## Ejercicio 1: `finger`

Al conectarme por `ssh` a `vit100.lab.it.uc3m.es` y ejecutar `finger 0493990`,
la salida es la siguiente.

```text
Login: 0493990        			Name: GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION
Directory: /usr/lab/alum/0493990    	Shell: /bin/bash
On since Thu Mar 20 09:59 (CET) on pts/0 from 10.118.98.239
Last login Thu Mar 20 10:04 (CET) on pts/1 from 10.118.98.239
Mail last read Sat Dec  7 09:11 2024 (CET)
No Plan.
```

Al conectarme por `ssh` a `monitor02.lab.it.uc3m.es` y ejecutar `finger
0493990`, la salida es la misma:

```text
Login: 0493990        			Name: GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION
Directory: /usr/lab/alum/0493990    	Shell: /bin/bash
On since Thu Mar 20 10:33 (CET) on pts/0 from 10.118.98.239
   4 seconds idle
Mail last read Sat Dec  7 09:11 2024 (CET)
No Plan.
```

## Ejercicio 2: encontrar el `dn` de una asignatura

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "dc=lab,dc=it,dc=uc3m,dc=es" "(uid=labgdst)" dn
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (uid=labgdst)
# requesting: dn 
#

# labgdst, Asig, lab.it.uc3m.es
dn: uid=labgdst,ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```

En este caso, el `dn` es `uid=labgdst,ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es`.

## Ejercicio 3: mostrar y contar las asignaturas

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es" "(objectClass=*)"
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (objectClass=*)
# requesting: ALL
#

# Asig, lab.it.uc3m.es
dn: ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es
objectClass: organizationalUnit
ou: Asig

# ircit, Asig, lab.it.uc3m.es
dn: uid=ircit,ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es
uid: ircit
objectClass: account
objectClass: posixAccount
objectClass: top
objectClass: sambaSamAccount
objectClass: inetLocalMailRecipient
...

# search result
search: 2
result: 0 Success

# numResponses: 80
# numEntries: 79
```

El número de entradas es 79, pero la primera no es una asignatura sino que
corresponde a la unidad organizativa, por lo que el número real de asignaturas
es **78**.

## Ejercicio 4: mostrar `dn`

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es" "(objectClass=*)" dn
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (objectClass=*)
# requesting: dn 
#

# Asig, lab.it.uc3m.es
dn: ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es

# ircit, Asig, lab.it.uc3m.es
dn: uid=ircit,ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es

...

# labgsa, Asig, lab.it.uc3m.es
dn: uid=labgsa,ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 2
result: 0 Success

# numResponses: 80
# numEntries: 79
```

Con esto, obtenemos los datos que se piden.

## Ejercicio 5: mostrar `homeDirectory` y `cn`

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es" "(objectClass=*)" cn homeDirectory
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (objectClass=*)
# requesting: cn homeDirectory 
#

# Asig, lab.it.uc3m.es
dn: ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es

# ircit, Asig, lab.it.uc3m.es
dn: uid=ircit,ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es
homeDirectory: /usr/lab/asig/ircit
cn: Inteligencia en Redes de Comunicaciones (jvillena@it.uc3m.es)

...

# labgsa, Asig, lab.it.uc3m.es
dn: uid=labgsa,ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es
cn:: U2VydmljaW9zIEF1ZGlvdmlzdWFsZXMgR3JhZG8gZW4gSW5nZW5pZXLDg8KtYSBkZSBTb25pZ
 G8gZSBJbWFnZW4=
homeDirectory: /usr/lab/asig/labgsa

# search result
search: 2
result: 0 Success

# numResponses: 80
# numEntries: 79
```

Con esto, obtenemos los datos que se piden.

## Ejercicio 6: buscar tu usuario

El critero más preciso para filtrar será el `uid`

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "dc=lab,dc=it,dc=uc3m,dc=es" "(uid=0493990)"
```

Obtenemos la siguiente respuesta (truncada):

```text
# extended LDIF
#
# LDAPv3
# base <dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (uid=0493990)
# requesting: ALL
#

# 0493990, Alum, lab.it.uc3m.es
dn: uid=0493990,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es
uid: 0493990
cn: ALONSO HERREROS COPETE
objectClass: account
objectClass: posixAccount
objectClass: top
objectClass: sambaSamAccount
objectClass: inetLocalMailRecipient
loginShell: /bin/bash
uidNumber: 493990
gidNumber: 493990
homeDirectory: /usr/lab/alum/0493990
gecos: GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION
...
```

## Ejercicio 7: mostrar tu `homeDirectory` y tu `cn`

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "dc=lab,dc=it,dc=uc3m,dc=es" "(uid=0493990)" homeDirectory mailRoutingAddress cn
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (uid=0493990)
# requesting: homeDirectory mailRoutingAddress cn 
#

# 0493990, Alum, lab.it.uc3m.es
dn: uid=0493990,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es
cn: ALONSO HERREROS COPETE
homeDirectory: /usr/lab/alum/0493990
mailRoutingAddress: 100493990@alumnos.uc3m.es
...
```

Con esto, obtenemos los datos que se piden.

## Ejercicio 8: busca los estudiantes en tu titulación

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es" "(gecos=GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION )" none
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (gecos=GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION )
# requesting: none 
#

# 0284943, Alum, lab.it.uc3m.es
dn: uid=0284943,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

...

# 0522716, Alum, lab.it.uc3m.es
dn: uid=0522716,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 2
result: 0 Success

# numResponses: 518
# numEntries: 517
```

Al parecer, **hay 517 estudiantes que cumplen esta condición**

## Ejercicio 9: `ldapsearch -s base`

Según el manual (`ldapsearch(1)`), al incluir esta opción, la búsqueda se
limita a un objeto base en lugar de cualquier sub-árbol (por defecto).

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es" -s base "(gecos=GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION )"
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es> with scope baseObject
# filter: (gecos=GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION )
# requesting: ALL
#

# search result
search: 2
result: 0 Success

# numResponses: 1
```

Es decir, no hay ningun objeto que coincida con esta búsqueda. Esto es lógico,
ya que no hay un objeto 'base' en la unidad organizativa `Alum` con el campo
`gecos` que hemos pedido. Si ejecutamos el mismo comando omitiendo este filtro:

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es" -s base "(objectClass=*)"
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es> with scope baseObject
# filter: (objectClass=*)
# requesting: ALL
#

# Alum, lab.it.uc3m.es
dn: ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es
objectClass: organizationalUnit
ou: Alum

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```

Como podemos comprobar, obtenemos una entrada, que corresponde al objeto base
especificado: su `dn` es igual a la `base` que hemos especificado con el
parámetro `-b`.

## Ejercicio 10: `ldapsearch -s one ... 1.1`

Según el manual (`ldapsearch(1)`), al incluir la opción `-s one`, la búsqueda
se limita a objetos directamente hijos de la base especificada.

Usando el siguiente comando obtenemos todos los alumnos que sean hijos directos
de la unidad organizativa `Alum`:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es" -s one "(objectClass=*)"
```

Obtenemos la siguiente respuesta (truncada):

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es> with scope oneLevel
# filter: (objectClass=*)
# requesting: none
#

# 0027381, Alum, lab.it.uc3m.es
dn: uid=0027381,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

...

# 0553984, Alum, lab.it.uc3m.es
dn: uid=0553984,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 2
result: 0 Success

# numResponses: 2846
# numEntries: 2845
```

Si se añade el parámetro `-s one` al comando del ejercicio 8, obtenemos el
mismo resultado, ya que todos los alumnos eran hijos directos de la unidad
organizativa `Alum`. Con el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es" -s one "(gecos=GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION )" dn
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es> with scope oneLevel
# filter: (gecos=GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION )
# requesting: dn 
#

# 0284943, Alum, lab.it.uc3m.es
dn: uid=0284943,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

...

# 0522716, Alum, lab.it.uc3m.es
dn: uid=0522716,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 2
result: 0 Success

# numResponses: 518
# numEntries: 517
```

Otro uso de este parámetro puede ser obtener los subgrupos 'hermanos' de
`Alum`, aplicando la búsqueda a una base de un nivel anterior. Usando el
siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "dc=lab,dc=it,dc=uc3m,dc=es" -s one "(objectClass=*)"
```

Obtenemos la siguiente respuesta (truncada):

```text
# extended LDIF
#
# LDAPv3
# base <dc=lab,dc=it,dc=uc3m,dc=es> with scope oneLevel
# filter: (objectClass=*)
# requesting: ALL
#

# admin, lab.it.uc3m.es
dn: cn=admin,dc=lab,dc=it,dc=uc3m,dc=es
objectClass: simpleSecurityObject
objectClass: organizationalRole
cn: admin
description: LDAP administrator

# Roaming, lab.it.uc3m.es
dn: ou=Roaming,dc=lab,dc=it,dc=uc3m,dc=es
objectClass: organizationalUnit
ou: Roaming

# Group, lab.it.uc3m.es
dn: ou=Group,dc=lab,dc=it,dc=uc3m,dc=es
objectClass: organizationalUnit
ou: Group

# Alum, lab.it.uc3m.es
dn: ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es
objectClass: organizationalUnit
ou: Alum

# Asig, lab.it.uc3m.es
dn: ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es
objectClass: organizationalUnit
ou: Asig

...

# search result
search: 2
result: 0 Success

# numResponses: 22
# numEntries: 21
```

## Ejercicio 11: alumnos con el mismo (primer) nombre

Siendo mi nombre Alonso Herreros Copete, el nombre a buscar es 'ALONSO'. Usando
el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es" -s one "(cn=ALONSO *)" dn
```

Obtenemos la siguiente respuesta (reducida):

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es> with scope oneLevel
# filter: (cn=ALONSO *)
# requesting: dn 
#

...

# 0493990, Alum, lab.it.uc3m.es
dn: uid=0493990,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

...

# search result
search: 2
result: 0 Success

# numResponses: 6
# numEntries: 5
```

Como podemos ver, **hay 5 alumnos** con el primer nombre 'Alonso' en las
cuentas de `lab.it.uc3m.es`.

## Ejercicio 12: estudiantes que contengan 'GRA'

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es" "(cn=*GRA*)" dn
```

Obtenemos la siguiente respuesta (reducida):

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (cn=*GRA*)
# requesting: dn 
#

# 0384107, Alum, lab.it.uc3m.es
dn: uid=0384107,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

...

# 0523045, Alum, lab.it.uc3m.es
dn: uid=0523045,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 2
result: 0 Success

# numResponses: 19
# numEntries: 18
```

Como vemos, hay 18 entradas que cumplen esta condición

## Ejercicio 13: estudiantes con 'JU' y 'MAR' en su nombre

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es" "(&(cn=*JU*)(cn=*MAR*))" dn
```

Obtenemos la siguiente respuesta (reducida):

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (&(cn=*JU*)(cn=*MAR*))
# requesting: dn 
#

# 0066665, Alum, lab.it.uc3m.es
dn: uid=0066665,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

...

# 0475063, Alum, lab.it.uc3m.es
dn: uid=0475063,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 2
result: 0 Success

# numResponses: 13
# numEntries: 12
```

## Ejercicio 14: estudiantes con 'MAR' pero sin 'JU'

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es" "(&(!(cn=*JU*))(cn=*MAR*))" dn
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (&(!(cn=*JU*))(cn=*MAR*))
# requesting: dn 
#

# 0060781, Alum, lab.it.uc3m.es
dn: uid=0060781,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

...

# 0428734, Alum, lab.it.uc3m.es
dn: uid=0428734,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 2
result: 0 Success

# numResponses: 411
# numEntries: 410
```

## Ejercicio 15: estudiantes con 'JU' pero sin 'MAR'

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es" "(&(cn=*JU*)(!(cn=*MAR*)))" dn
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (&(cn=*JU*)(!(cn=*MAR*)))
# requesting: dn 
#

# 0072703, Alum, lab.it.uc3m.es
dn: uid=0072703,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

...

# 0553673, Alum, lab.it.uc3m.es
dn: uid=0553673,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 2
result: 0 Success

# numResponses: 90
# numEntries: 89
```

## Ejercicio 16: `ldapsearch -z 7`

Según el manual (`ldapsearch(1)`), al incluir esta opción, la búsqueda se
limita a un número de entradas, en este caso 7.

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es" -z 7 "(&(cn=*JU*)(!(cn=*MAR*)))" dn
```

Obtenemos la siguiente respuesta (reducida):

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (&(cn=*JU*)(!(cn=*MAR*)))
# requesting: dn 
#

# 0072703, Alum, lab.it.uc3m.es
dn: uid=0072703,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

...

# 0383446, Alum, lab.it.uc3m.es
dn: uid=0383446,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 2
result: 4 Size limit exceeded

# numResponses: 8
# numEntries: 7
```

## Ejercicio 17: estudiantes con mismo primer o segundo apellido

Las dos cadenas que debo buscar en mi caso son 'HERREROS' y 'COPETE'. Usando el
siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es" "(|(cn=*HERREROS*)(cn=*COPETE*))" dn
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter: (|(cn=*HERREROS*)(cn=*COPETE*))
# requesting: dn 
#

# 0493990, Alum, lab.it.uc3m.es
dn: uid=0493990,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```

Por lo que no hay ningún otro alumno en las cuentas de `lab.it.uc3m.es` que
tenga mi primer ni mi segundo apellido en su nombre.

## Ejercicio 18: ldapsearch con un fichero

Se pueden encontrar los detalles sobre estas opciones en `ldapsearch(1)`.

Tras crear el fichero `batches/18-batch-uids.txt` con el siguiente contenido:

```text
0493990
0496080
labgdst
```

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.lab.it.uc3m.es -b "dc=lab,dc=it,dc=uc3m,dc=es" "(uid=%s)" dn -f batches/18-batch-uids.txt
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <dc=lab,dc=it,dc=uc3m,dc=es> with scope subtree
# filter pattern: (uid=%s)
# requesting: dn 
#

#
# filter: (uid=0493990)
#
# 0493990, Alum, lab.it.uc3m.es
dn: uid=0493990,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1

#
# filter: (uid=0496080)
#
# 0496080, Alum, lab.it.uc3m.es
dn: uid=0496080,ou=Alum,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 3
result: 0 Success

# numResponses: 2
# numEntries: 1

#
# filter: (uid=labgdst)
#
# labgdst, Asig, lab.it.uc3m.es
dn: uid=labgdst,ou=Asig,dc=lab,dc=it,dc=uc3m,dc=es

# search result
search: 4
result: 0 Success

# numResponses: 2
# numEntries: 1
```

Como se esperaba, se han realizado 3 peticiones con la misma base y distintos
filtros.

## Ejercicio 19: petición al LDAP de la universidad

> **Nota**
>
> El LDAP de la universidad se negó a responder a mis peticiones desde mi
> máquina virtual, por lo que este ejercicio lo hice desde la máquina
> `vit100.lab.it.uc3m.es`.

Para averiguar la estructura del LDAP, primero realizaré una petición buscando
mi `uid`, con la mímima restricción de base:

```sh
ldapsearch -x -H ldaps://ldap.uc3m.es -b "o=Universidad Carlos III,c=es" "(uid=100493990)" dn
```

Obteniendo la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <o=Universidad Carlos III,c=es> with scope subtree
# filter: (uid=100493990)
# requesting: dn 
#

# 100493990, GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION, Alumnos, G
 ente, Universidad Carlos III, es
dn: uid=100493990,ou=GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION,ou
 =Alumnos,ou=Gente,o=Universidad Carlos III,c=es

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```

Una vez sabida la base relevante, puedo realizar otra petición especificando la
condición de coincidencia de nombre. Usando el siguiente comando:

```sh
[2025-03-20, 13:03:51] ldapsearch -x -H ldaps://ldap.uc3m.es -b "ou=Alumnos,ou=Gente,o=Universidad Carlos III,c=es" "(cn=ALONSO *)" dn
# extended LDIF
#
# LDAPv3
# base <ou=Alumnos,ou=Gente,o=Universidad Carlos III,c=es> with scope subtree
# filter: (cn=ALONSO *)
# requesting: dn 
#

# 100036232, LICENCIATURA EN ADMINISTRACION Y DIRECCION DE EMPRESAS, Alumnos, G
 ente, Universidad Carlos III, es
dn: uid=100036232,ou=LICENCIATURA EN ADMINISTRACION Y DIRECCION DE EMPRESAS,ou
 =Alumnos,ou=Gente,o=Universidad Carlos III,c=es

...

# search result
search: 2
result: 4 Size limit exceeded

# numResponses: 51
# numEntries: 50
```

Ya que el número de entradas es mayor que 50, repetiré la búsqueda con más
restricciones. Al incluir la primera letra de mi apellido, la cadena de
búsqueda resulta 'ALONSO H*'.

Usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.uc3m.es -b "ou=Alumnos,ou=Gente,o=Universidad Carlos III,c=es" "(cn=ALONSO H*)" dn
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alumnos,ou=Gente,o=Universidad Carlos III,c=es> with scope subtree
# filter: (cn=ALONSO H*)
# requesting: dn 
#

# 100493990, GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION, Alumnos, G
 ente, Universidad Carlos III, es
dn: uid=100493990,ou=GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION,ou
 =Alumnos,ou=Gente,o=Universidad Carlos III,c=es

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```

Es decir, que solo hay un estudiante en la UC3M cuyo primer nombre sea 'Alonso'
y cuyo segundo nombre o primer apellido empiece por 'H' (soy yo).

Se puede hacer una búsqueda menos estricta para encontrar a los alumnos que
tengan algún apellido que empiece por 'H' usando el siguiente comando:

```sh
ldapsearch -x -H ldaps://ldap.uc3m.es -b "ou=Alumnos,ou=Gente,o=Universidad Carlos III,c=es" "(cn=ALONSO* H*)" dn
```

Obtenemos la siguiente respuesta:

```text
# extended LDIF
#
# LDAPv3
# base <ou=Alumnos,ou=Gente,o=Universidad Carlos III,c=es> with scope subtree
# filter: (cn=ALONSO* H*)
# requesting: dn 
#

# 100493990, GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION, Alumnos, G
 ente, Universidad Carlos III, es
dn: uid=100493990,ou=GRADO EN INGENIERIA EN TECNOLOGIAS DE TELECOMUNICACION,ou
 =Alumnos,ou=Gente,o=Universidad Carlos III,c=es

# 100525807, DOBLE GRADO EN DERECHO Y ECONOMIA, Alumnos, Gente, Universidad Car
 los III, es
dn: uid=100525807,ou=DOBLE GRADO EN DERECHO Y ECONOMIA,ou=Alumnos,ou=Gente,o=U
 niversidad Carlos III,c=es

# search result
search: 2
result: 0 Success

# numResponses: 3
# numEntries: 2
```

Es decir, que hay un alumno cuyo primer nombre es 'Alonso' y cuyo segundo
apellido (o primer apellido en caso de tener segundo nombre) empieza por 'H'.

## Licencia

El uso de esta obra está sujeto a la licencia
[Creative Commons Attribution-ShareAlike 4.0 International License][cc-by-sa].

[![CC BY-SA 4.0][cc-by-sa-image]][cc-by-sa]

[cc-by-sa]: http://creativecommons.org/licenses/by-sa/4.0/
[cc-by-sa-image]: https://licensebuttons.net/l/by-sa/4.0/88x31.png
[cc-by-sa-shield]: https://img.shields.io/badge/License-CC%20BY--SA%204.0-lightgrey.svg
