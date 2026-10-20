---
marp: true
theme: default
paginate: true
---

# Introducció a PHP  
## Presentació bàsica

---

# Índex

1. Què és PHP  
2. PHP com a llenguatge d’scripting  
3. Instal·lació i versions  
4. Hola món  
5. Executar PHP per terminal  
6. Comentaris  
7. Variables  
8. Variables predefinides  
9. Imprimir i depurar  
10. Veure errors al navegador  
---

11. Control de flux i bucles  
12. switch vs match  
13. Arrays  
14. foreach  
15. Strings i funcions útils  
16. Enums  
17. Incloure fitxers  
18. header()  
19. Sessions i cookies  
20. Errors i excepcions  

---

# 1. Què és PHP?

- Llenguatge d’scripting per backend
- S’executa al servidor i genera HTML dinàmic
- Molt usat en formularis, sessions, APIs i CMS
- PHP = “PHP: Hypertext Preprocessor”

---

# 2. PHP com a llenguatge d’scripting

- No cal compilar — és interpretat
- S’incrusta dins HTML amb `<?php ... ?>`

---

# 3. Instal·lació i versions

- Comprovar versió: `php -v`
- Recomanat usar PHP 8.0+ per funcions modernes i seguretat

---

# 4. Primer “Hola món”

```php
<?php
echo "Hola món!";
?>
```

Recorda: PHP sempre dins `<?php … ?>` quan s’usa en fitxers .php

---

# 5. Executar PHP per terminal (CLI)

Executar:

```
php fitxer.php
```

Útil per scripts, tests i petits programes CLI.

---

# 6. Comentaris

```php
// Comentari d’una línia
# També vàlid

/* Comentari
   de diverses línies */
```

---

# 7. Variables

- Sempre comencen amb `$`
- Tipus dinàmics: `int`, `float`, `bool`, `string`, `array`, `object`

```php
$nom = "Anna";
$edat = 30;
const PI = 3.14;
define("APP", "demo");
echo APP;
```
---

# 8. Superglobals

Variables disponibles globalment. Algunes comunes:

```php
$_GET, $_POST, $_SERVER, $_SESSION, $_COOKIE, $_FILES
```

---

# 9. Imprimir i depurar

Imprimir:

```php
echo "Hola";
print "Adeu";
```

Depurar: `var_dump($var)`, `print_r($arr, true)`

Linea nova en CLI: `echo "Hola" . PHP_EOL;`

---

# 10. Veure errors en el navegador

Per desenvolupar, al principi del fitxer:

```php
ini_set("display_errors", 1); // Temporal i localment (script)
ini_set("display_startup_errors", 1); // Errors abans d'executar l'script
error_reporting(E_ALL); // Tipus d'errors a mostrar
```

A `php.ini`: `display_errors = On` (canvis permanents. A entorn de desenvolupament)

---

# 11. Control de flux

If/else:

```php
if ($edat >= 18) {
    echo "Major";
} else {
    echo "Menor";
}
```
---

# 11. Bucles

Bucles comuns:

```php
for ($i = 0; $i < 10; $i++) {
    // ...
}

$i = 0;
while ($i < 10) {
    $i++;
}

do {
    // ...
} while ($i < 10);
```

Operadors: `==, === (comparació estricta. Mateix valor i tipus), >, <, &&, ||, !`

---

# 12. `switch` vs `match`

`switch` (clàssic):

```php
switch ($dia) {
 case 1: echo "Dilluns"; break;
 default: echo "Altres";
}
```

`match` (PHP 8.0+), retorna valor i és expression:

```php
echo match($dia) {
  1 => "Dilluns",
  default => "Altres",
};
```

---

# 13. Arrays

Arrays indexats i associatius:

```php
$fruites = ["poma", "pera"];
$persona = ["nom" => "Anna", "edat" => 22];

$tamany = array_push($fruites, "plàtan", "kiwi");
echo count($fruites) . PHP_EOL; // tamany
count($fruites) . PHP_EOL; // tamany
```

---

# 13.1 Funcions per Arrays

Funcions útils: `array_push`, `array_merge`, `array_map`, `array_filter`, `array_values`.

- `array_merge($arr1, $arr2)`: fusiona diversos arrays. Retorna nou array.
- `array_map(fn($x) => $x * 2, $arr)`: aplica funció a cada element. Retorna nou array.
- `array_values ($arr)`: Retorna nou array indexat numèricament i amb els mateixos valors.

---

# 13.2 Exemples de funcions per Arrays

```php
$numeros = [1, 2, 3, 4, 5, 6];

$parells = array_filter($numeros, function($n) {
    return $n % 2 === 0; // Retorna true només si és parell
});

print_r($parells);
```

---

# 14. Recórrer arrays

```php
foreach ($fruites as $f) {
    echo $f . "\n";
}

// per arrays associatius:
foreach ($persona as $clau => $valor) {
    echo "$clau: $valor\n";
}
```

---

# 15. Strings i funcions útils

Interpolació i concatenació:

```php
echo "Hola $nom";
echo "Hola " . $nom;
```
---
# 15.2. Funcions útils per a strings
Funcions comunes:

```php
strlen($s);
strpos($s, $needle); // retorna posició de $needle o false si no trobat
substr($s, 0, 5);
explode(",", $s); //divideix string en array segons delimitador (primer argument)
implode(",", $array);
```
Exemple amb funció `implode`:
```php
$array = ["poma", "pera", "plàtan"];
$string = implode(", ", $array); // "poma, pera, plàtan"
```
---

# 16. Enums (PHP 8.1+)

Definir enums:

```php
enum Rol {
 case Admin;
 case Usuari;
}

Rol::from("Admin"); // retorna Rol::Admin
Rol::tryFrom("X"); // retorna null si no existeix
```

Exemple d’ús:

```php
$rol = Rol::Admin;
if ($rol === Rol::Admin) {
    echo "És administrador";
}
```

---

# 17. Incloure fitxers

```php
include "util.php";      // avisa si falta
require "config.php";    // error fatal si falta
require_once "db.php";   // evita múltiples inclusions
```
En el taller (activitat 02) veurem alternatives.

---

# 18. Afegir capçaleres HTTP amb `header()`

Usos de `header()`: 
- redireccionar
- enviar tipus de contingut
- controlar cache
- afegir cookies
- etc.

---

# 18.2 Exemples de `header()`

Redireccionar:
```php
header("Location: index.php");
exit;
```
Enviar tipus de contingut JSON:
```php
header("Content-Type: application/json");
echo json_encode($data);
```

**Assegura’t de no haver enviat sortida abans d’encapçalats.**

---

# 19. Sessions i cookies

Ho veurem en el taller.

---

# 20. Errors i excepcions

Try/catch::

```php
try {
    risky();
} catch (Exception $e) {
    echo $e->getMessage();
}
```

Llançar excepcions personalitzades:

```php
throw new Exception("Error personalitzat");
```

---

# Recursos

- Documentació oficial: https://www.php.net/
- Tutorials: https://www.w3schools.com/php/