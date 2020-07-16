---
title: Le Langage C, Introduction moderne
layout: default.liquid
---

# Le langage C

Le but de ce document est de proposer une approche moderne et accessible,
du langage C dans l'optique de la programmation système, l'objectif n'est pas
de pousser à développer de nouveaux projets en C mais de pouvoir contribuer à
l'existant et à s'interfacer avec.

Pour développer de nouveaux projets systèmes vous pouvez jetez un œil au langage
[Rust](https://www.rust-lang.org/) vous en ferrez en 4ème et 5ème année.
Ou bien C++ ou Go.

Dans ce document, la pratique et les exemples auront une grande importante.

## Contributions

- Merci à Oumaima El kati pour des corrections orthographiques et de grammaire.

## Introduction

Le langage C, est un langage compilé c’est-à-dire vous avez besoin d'un programme  qui transforme votre fichier de code source écrit avec des
caractères en fichier binaire: un « compilateur ». Dans le cas du C le binaire peut-être
directement exécutable. Plusieurs langages ont ce fonctionnement notamment C++
et Rust.

**Note: gestion de la mémoire**: Rust, et C++ n'utilisent pas de gestionnaire automatique
de la mémoire _"Gabarge collector"_. Des langages comme JavaScript, Python, Go,
Ocaml, Java on besoin d'un tel programme. On reviendra dessus plus-tard.

## Vue d'ensemble: Hello World

Commençons donc directement par fait notre premier programme en C.

Le programme suivant va afficher deux chaines de caractères sur
la sortie standard `stdout` de votre terminal. Il est très largement
commenté comme le seront les exemples car pour parler de code, rien de
mieux que le code!

Pour compiler vous pouvez faire: `gcc -Werror -Wextra -Wall hello.c -o hello`.

- `-Wall`: Active des avertissements usuels
- `-Wextra`: Active des avertissements avancées
- `-Werror`: Transforme les avertissements en erreurs

Il existe un riche panel d'avertissements, *warning* très intéressants,
vous pouvez regarder le `man gcc` ou bien
[la documentation](https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html),
des avertissements sur la doc en ligne du compilateur gcc.

**Documentation pour aller plus loin:** Voici un article de [Krister Walfridsson](https://kristerw.blogspot.com/2017/09/useful-gcc-warning-options-not-enabled.html) proposant des avertissements utiles.

```c
// Fichier hello.c
#include <stdlib.h>
#include <stdio.h>
// `include` sert à inclure les prototypes (signatures)
// de fonctions d'une autre bibliothèque ou d'autres fichiers

// Anatomie de la fonction en C:

// Le type que renvois notre fonction ici un `int`
// ↙        ↙ Entre les parenthèses vous retrouvez la liste des paramètres.
int main(void) { // Ici notre fonction n'en prends pas.
  // ↗
  // main est le nom où identifiant de notre fonction
  // Ce nom permet de l'appeler plus tard.

  // Entre les `{ }` on a le corps de notre fonction.

  // `puts` est une fonction que nous n'avons pas écrite.
  // ↙ définie dans `stdio.h` et disponible dans la libC.
  puts("Hello World");
  // Sa doc est disponible avec la commande:
  // man 3 puts

  // Ici on définit une chaine constante immuable
  // ⬇ Sa valeur ni son pointeur ne peut changer c'est
  // ⬇ utile pour définir des chaines qui changeront jamais.
  // ⬇ Et on souhaite l'écrire sur la sortie standard avec `printf`.
  const char[] name = "Axel";
  printf("My name is %s.\n", name); // ⬅ name ici est un paramètre.
  //                ↗
  // Le `%s` de formatage, indique qu'on souhaite inclure une chaine
  // de caractères dans notre message.
  // `\n` indique un retour à la ligne sur un système Unix.

  return EXIT_SUCCESS;
  //    ↗
  //  On utilise ici une macro définie dans `stdlib.h`
  //  ici elle permet de dire de façon *portable*
  //  tout s'est bien passé dans notre programme.
  //  Sous linux `EXIT_SUCCESS` vaut `0`.
}
```

Pour récapituler dans le programme suivant on a vu comment déclarer une fonction
ici `main`, `main` est cependant une fonction particulière car c'est le _point d'entrée_
du programme, c'est la fonction par laquelle commencent vos programmes C.

Comment inclure des signatures de fonctions `include`, comment
déclarer une variable avec son type avec un qualificateur ici `const`
qui garantie que votre valeur ne va pas changer, comment appelle
des fonctions tel que `puts`, `printf`.

### Entrainements: erreurs de compilation

En C on met toujours un `;` entre deux instructions essayer d'enlever les
points virgules du code ci-dessous pour vous habituer avec les erreurs de syntaxe.

Enlevez le type `char[]` sur la ligne `const char[] name` pour observer une
erreur de compilation assez courante au début: Oublier le type d'une variable.

Retirez `return EXIT_SUCCESS;` que se passe-t-il?

## Bindings / Liaisons / variables

En C l'anatomie d'une liaison ou binding d'un nom à une valeur est
la suivante:

```c
//   ↙ int est le type de notre variable `a`.
// ↙   On appelle la partie à droite du `=`: « lvalue ».
int a = 3;
//     ↗
//    3 est la valeur de la variable `a`.
//    Cette partie à gauche du `=` s'appelle « rvalue ».
```

Vous pouvez depuis C99 lier une valeur à un nom, partout dans votre code par exemple
dans un `if` ou une boucle `for`, ou une instruction est possible.
C'est bien plus clair de lier au plus près de l'usage que en sommet de fonction
comme vous pourrez le voir dans du C historique C89 par exemple.

Une liason ou variable peut-être dite constante si sa valeur ne change pas.

Par exemple:

```c
// On lie 42 à the_absolute_response.
const int the_absolute_response = 42;
// Puis on se dit pourquoi pas plutôt 2.
the_absolute_response = 2; // error!
```

Ici le compilateur GCC vous affichera une erreur car vous contredisez
votre engagement initial, ou vous vous engagiez à ne pas changer
la valeur de `the_absolute_response`.

```txt
obviously_42.c: In function ‘main’:
obviously_42.c:3:5: error: assignment of read-only variable ‘a’
   a = 2;
     ^
```

**Note:** Pour les pointeurs rappelez-vous que seule la valeur est constante pas ce qui est référé. 😉

Si vous voulez que votre liaison _binding_ où variable soit modifiable il suffit
d'écrire:

```c
int somme = 0; // On lie somme à 0.
// On affecte (symbole `=`) à somme: somme + 1.
somme = somme + 1;
// Aussi on peut faire une addition, affectation avec `+=`.
somme += 1;
// Question: quelle est la valeur de somme?
```




### Types usuels en C

Par defaut en C voici un tableau de types natifs du C:

| Type    | Nom complet       |
| :------ | :---------------: |
| `char`    | byte              |
| `int`     | Entier            |
| `float`   | Nombre en précision flottante simple |
| `double`  | Nombre en précision flottante double |
| `void`    | Void              |

Il est possible parfois de voir qualifier ses types par les *qualificateurs* suivants:

| Type     | Qualificateurs possibles |
| :----    | ------------------------ |
| `char`   | `signed`, `unsigned`     |
| `int`    | `short`, `long`, `signed`, `unsigned` |
| `float`  |                          |
| `double` | `long`                   |
| `void`   |                          |

Pour leurs « tailles » en bits voici un récapitulatif vrai pour une architecture
64 bits comme celle de vos ordinateurs _x86\_64_.

| type       | taille en bits | Valeurs possibles |
| :--------- | :------------: | :------------- |
| `char`       | 8              | [ -128; 127 ]   |
| `unsigned char` | 8           | [ 0, 255 ]      |
| `short int`  | 16             | [ -32768; 32767 ]
| `int`        | 32             | [-2147483648; 2147483647 ] |
| `long int`   | 64             | [ `2^64 - 1`; `2^(64 - 1) - 1`] |

Les valeurs maximales possibles sont obtenables avec les formules suivantes:

- Si votre type est préfixé par `unsigned`: `2^n - 1`
- Si votre type est sans prefix ou avec `signed`: `2^(n - 1) - 1`

Les valeurs minimales:

- Si votre type est préfixé par `unsigned`: `0`
- Si votre type est sans préfixe ou avec `signed`: `-2^(n - 1)`

Ou `n` est le nombre de bit en terme de taille pour représenter votre
type.

**Note:** Il existe l'entête `stdint.h` qui propose des types pour le C
d'entiers plus précis, il a été introduit par le standard C99.

## Pointeurs

## Fonctions

### Passage des paramètres par référence vs copie

## Pointeurs sur fonctions

## <a name="struct"></a>Structures: `struct`

En C souvent nous aurons besoin de manipuler plusieurs types regroupés ensembles.
Pour faire cela il existe une construction du langage les structures `struct`.

On va définir une structure `point_t` et des fonctions pour travailler avec.

```c
#include <stdint.h> // On utilise les types d'entiers standards de C99

// Typedef permet de faire un alias de nom
/// Structure représentant un point dans un espace 2D.
typedef struct {
  int32_t x;
  int32_t y;
} point_t;

/// Cette fonction permet d'afficher un point sur la sortie standard.
/// @param {point_t} p point à afficher.
///
void point_display(const point_t * p) {
  // `->` dans `p->x` permet si p est un pointeur
  // sur une structure d'accéder à ces champs.
  printf("point_t { %d, %d }", p->x, p->y);
  // La syntaxe équivalente est `(*p).x`.
}
```

### Exercices

Question 0.0: Écrire la fonction `point_new` de signature suivante:
```c
point_t point_new(int32_t x, int32_t y)
``` 
Qui permet de créer un `point_t` le retourne.

Question 0.1 (bonus): Comment peut-on éviter la copie et pour autant garder la
praticité de la fonction? Indice: «inline»

Question 1.0: Écrire une fonction add qui prends deux `point_t` de signature suivante
et renvoie un nouveau point.

```c
point_t point_add(const point_t * a, const point_t * b);
```

Question 1.1: Ici qu'est ce qui est passé en arguments? Le pointeur ou la valeur de
`a`?

Question 1.2: À quoi sert le mot-clef `const` ici dans la signature? Expérimentez en
tentant de modifier les champs de `a`.

Question 0: Notre point est-il retourné par copie ou par pointeur
avec `point_new`? Quel est le problème de retourner par copie avec une grande structure?

## Enumerations: `enum`

## Debuggage - GDB

A venir
