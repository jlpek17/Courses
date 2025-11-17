# JavaScript - Concepts Fondamentaux

## 1. Arrow Functions

### Définition
Les **arrow functions** sont une syntaxe concise pour écrire des fonctions en JavaScript. Elles permettent d'écrire du code plus court et lisible tout en offrant des règles de simplification progressives.

### Syntaxe

| Regular function | Anonymous version |
|:---|:---|
| `const username = 'john';` | `const username = 'john';` |
| `function capitalizeName(name) {`<br>`  return \`${name.charAt(0).toUpperCase()}${name.slice(1)}\`;`<br>`}` | `const capitalize = function(name) {`<br>`  return \`${name.charAt(0).toUpperCase()}${name.slice(1)}\`;`<br>`}` |

| Arrow function (primary) | With shorthands |
|:---|:---|
| `const capitalize = (name) => {`<br>`  return \`${name.charAt(0).toUpperCase()}${name.slice(1)}\`;`<br>`}` | `const capitalize = name => \`${name.charAt(0).toUpperCase()}${name.slice(1)}\`;` |

### Règles de simplification
1. **Parenthesis** : si un seul paramètre, les parenthèses sont optionnelles (0 ou 2+ paramètres : parenthèses obligatoires)
2. **Curlybraces** : si le corps ne contient qu'une seule expression, les accolades sont optionnelles (plusieurs expressions : accolades obligatoires)
3. **Return** : sans accolades, le `return` est implicite (la valeur est automatiquement retournée)
4. **Corps de fonction** : avec shorthands, tout peut tenir sur une seule ligne

---

## 2. Callback Functions

### Définition
Une **callback function** est une fonction passée en argument à une autre fonction. Elle sera exécutée à un moment précis par la fonction qui la reçoit.

### Caractéristiques
- Permet de passer une fonction comme argument à une autre fonction
- La fonction qui reçoit le callback est appelée **higher order function** (fonction d'ordre supérieur)
- Le callback est exécuté à l'intérieur de la fonction d'ordre supérieur

### Exemple

```javascript
function greetUser(name, callback) {
  return callback(capitalize(name));
}
```

**Version avec arrow function complète :**
```javascript
const result = greetUser(username, (name) => { 
  return `Hi there, ${name}!`;
});
```

**Version avec shorthand :**
```javascript
const result = greetUser(username, name => `Hi there, ${name}!`);
```

### Utilisation
La callback reçoit `capitalize(name)` comme argument et retourne le message formaté :

```javascript
console.log(result);
// > Hi there, John
```

---

## 3. Closures

### Définition
Une **closure** se produit lorsqu'une fonction interne conserve l'accès aux variables de sa fonction englobante (fonction extérieure), **même après que cette fonction extérieure a terminé son exécution**.

### Exemple

```javascript
function handleLikePost(step) {
  let likeCount = 0;

  return function addLike() {
    likeCount += step;
    return likeCount;
  }
}

const doubleLike = handleLikePost(2);

console.log(doubleLike());
console.log(doubleLike());
console.log(doubleLike());
```

### Fonctionnement

1. **Création du contexte (`handleLikePost(2)`)**
   - `handleLikePost` s'exécute une seule fois
   - Crée les variables locales : `likeCount = 0` et `step = 2` (paramètre)
   - Retourne la fonction interne `addLike`

2. **La closure est créée (`doubleLike`)**
   - `doubleLike` stocke la référence vers la fonction `addLike`
   - Cette fonction "capture" et conserve l'accès à `likeCount` et `step`
   - Ces variables restent en mémoire tant que `doubleLike` existe

3. **Utilisation de la closure**
   - Chaque appel à `doubleLike()` accède et modifie les **mêmes** variables capturées

| Appel | Action (`likeCount += step;`) | Valeur de `likeCount` (stockée) | Sortie |
|:---|:---|:---|:---|
| `doubleLike()` | $0 + 2$ | 2 | 2 |
| `doubleLike()` | $2 + 2$ | 4 | 4 |
| `doubleLike()` | $4 + 2$ | 6 | 6 |

**Point clé :** `likeCount` n'est pas réinitialisé entre les appels car il vit dans la closure. La variable persiste et maintient son état entre chaque exécution de `doubleLike()`.

---

## 4. Partial Application

### Définition
L'**application partielle** (partial application) est une technique qui consiste à **fixer certains paramètres d'une fonction** pour créer une nouvelle fonction plus spécialisée qui attend les paramètres restants.

### Exemple

```javascript
function getData(baseUrl) {
  return function(route) {
    fetch(`${baseUrl}${route}`)
      .then(response => response.json())
      .then(data => console.log(data));
  }
}

const getSocialMediaData = getData('https://jsonplaceholder.typicode.com');

getSocialMediaData('/posts');
```

### Fonctionnement

**Étape 1 : Création de la fonction spécialisée**

```javascript
const getSocialMediaData = getData('https://jsonplaceholder.typicode.com');
```

1. Appel de `getData` avec `baseUrl = 'https://jsonplaceholder.typicode.com'`
2. `getData` retourne la fonction interne `function(route) { ... }`
3. Une **closure** est créée : la fonction interne capture `baseUrl`
4. `getSocialMediaData` est maintenant une fonction spécialisée qui "connaît" déjà l'URL de base qui attend un parametre route

**Étape 2 : Utilisation de la fonction spécialisée**

```javascript
getSocialMediaData('/posts');
```

1. Appel de la fonction stockée dans `getSocialMediaData`
2. L'argument `'/posts'` est assigné au paramètre `route`
3. La fonction s'exécute avec :
   - `baseUrl` → provient de la closure (fixé lors de l'étape 1)
   - `route` → provient de l'appel actuel
4. Résultat final : `fetch('https://jsonplaceholder.typicode.com/posts')`

### Résumé

| Concept | Action | Résultat |
|:---|:---|:---|
| **Application Partielle** | `const getSocialMediaData = getData(...)` | Crée une fonction spécialisée avec `baseUrl` pré-configuré |
| **Closure** | La fonction interne est retournée | Elle garde en mémoire la valeur de `baseUrl` |
| **Exécution** | `getSocialMediaData('/posts')` | L'argument `/posts` est passé comme `route` |

**Avantage :** L'application partielle permet de créer des **fonctions spécialisées réutilisables**. `getData` est une fonction générique (factory), `getSocialMediaData` est une fonction spécialisée pour une API particulière.