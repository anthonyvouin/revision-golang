# FICHE DE RÉVISION GO (GOLANG) - VERSION DÉTAILLÉE

## Variables et Types de données

### Déclaration de variables
En Go, les variables sont typées statiquement. Il existe trois principales manières de déclarer des variables :

```go
// 1. Déclaration complète avec type explicite
var nom_variable type = valeur
var x int = 10

// 2. Déclaration avec inférence de type
var y = 20        // Le type est déduit de la valeur (ici int)

// 3. Déclaration courte (uniquement dans les fonctions)
z := 30           // Équivalent à var z = 30
```

### Types de base
Go propose un ensemble complet de types de données fondamentaux :

- **Numériques entiers**: 
  - `int`, `int8`, `int16`, `int32`, `int64` (signés)
  - `uint`, `uint8`, `uint16`, `uint32`, `uint64`, `uintptr` (non signés)
  - `int` et `uint` dépendent de l'architecture (32 ou 64 bits)

- **Numériques décimaux**: 
  - `float32`, `float64` (flottants)
  - `complex64`, `complex128` (nombres complexes)

- **Texte**: 
  - `string` (chaînes UTF-8 immuables)

- **Booléen**: 
  - `bool` (`true` ou `false`)

- **Types spéciaux**: 
  - `byte` (alias pour `uint8`, utilisé pour les données binaires)
  - `rune` (alias pour `int32`, représente un point de code Unicode)

### Constantes
Les constantes sont des valeurs fixées à la compilation :

```go
const PI = 3.14159

// Le mot-clé iota permet de créer des suites de constantes incrémentales
const (
    LUNDI = iota    // 0
    MARDI           // 1
    MERCREDI        // 2
    JEUDI           // 3
    VENDREDI        // 4
)

// iota peut être manipulé
const (
    KB = 1 << (10 * iota)  // 1 << 0 = 1
    MB                     // 1 << 10 = 1024
    GB                     // 1 << 20 = 1048576
    TB                     // 1 << 30 = 1073741824
)
```

### Conversion de types
Go exige des conversions explicites entre les types :

```go
i := 42
f := float64(i)           // Conversion int vers float64
s := strconv.Itoa(i)      // Int vers string
i2, err := strconv.Atoi("42")  // String vers int avec gestion d'erreur
```

## Fonctions

### Déclaration basique
Les fonctions sont des blocs de code réutilisables :

```go
func nomFonction(param1 type1, param2 type2) typeRetour {
    // Corps de la fonction
    return valeur
}

// Exemple
func add(a int, b int) int {
    return a + b
}

// Paramètres de même type peuvent être regroupés
func add(a, b int) int {
    return a + b
}
```

### Retours multiples
Une caractéristique puissante de Go est la capacité à retourner plusieurs valeurs :

```go
func divMod(a, b int) (int, int) {
    return a / b, a % b
}

quotient, reste := divMod(10, 3)  // quotient = 3, reste = 1

// On peut ignorer des valeurs avec _
quotient, _ := divMod(10, 3)      // Ignore le reste
```

### Retours nommés
Les valeurs de retour peuvent être nommées, ce qui simplifie le code :

```go
func divMod(a, b int) (quotient, reste int) {
    quotient = a / b
    reste = a % b
    return  // Retourne automatiquement quotient et reste
}

// Équivalent à:
func divMod(a, b int) (int, int) {
    quotient := a / b
    reste := a % b
    return quotient, reste
}
```

### Fonctions comme valeurs et Closures
Les fonctions sont des valeurs de première classe en Go :

```go
// Fonction comme type
var add = func(a, b int) int {
    return a + b
}
resultat := add(5, 3)  // 8

// Fonction qui retourne une fonction (closure)
func compteur() func() int {
    i := 0  // Cette variable est "capturée" par la closure
    return func() int {
        i++
        return i
    }
}

c := compteur()
fmt.Println(c())  // 1
fmt.Println(c())  // 2
fmt.Println(c())  // 3

// Chaque appel à compteur() crée un nouvel état
c2 := compteur()
fmt.Println(c2())  // 1
```

### Fonctions variadiques
Les fonctions variadiques acceptent un nombre variable d'arguments :

```go
func somme(nombres ...int) int {
    total := 0
    for _, nombre := range nombres {
        total += nombre
    }
    return total
}

// Appels avec différents nombres d'arguments
somme(1, 2)           // 3
somme(1, 2, 3, 4)     // 10
somme()               // 0

// Passage d'un slice avec l'opérateur d'expansion
nombres := []int{1, 2, 3, 4, 5}
somme(nombres...)     // 15
```

## Pointeurs

### Déclaration et utilisation
Les pointeurs contiennent l'adresse mémoire d'une variable :

```go
var p *int         // Déclare un pointeur vers un int (valeur initiale nil)
i := 42
p = &i             // p pointe vers i (&i donne l'adresse de i)
fmt.Println(*p)    // Déréférencement: affiche 42 (*p accède à la valeur pointée)
*p = 21            // Change la valeur de i via le pointeur
fmt.Println(i)     // Affiche 21
```

### Pointeurs et fonctions
Les pointeurs permettent de modifier les paramètres d'une fonction :

```go
func double(x *int) {
    *x *= 2        // Modifie la valeur pointée
}

n := 5
double(&n)         // Passe l'adresse de n
fmt.Println(n)     // n vaut maintenant 10

// Sans pointeur, la valeur serait copiée et ne serait pas modifiée
func doubleInefficient(x int) int {
    return x * 2
}
```

### Pointeurs comme valeur zéro
```go
var p *int          // p vaut nil (valeur zéro pour les pointeurs)
fmt.Println(p == nil)  // true

// Attention aux déréférencements de nil
if p != nil {
    fmt.Println(*p)  // Sécurisé
}
```

### Pourquoi utiliser des pointeurs ?
1. Modifier des variables en dehors de la fonction
2. Éviter de copier de grandes structures (performance)
3. Implémenter des structures de données (listes, arbres...)
4. Compatibilité avec les méthodes qui modifient le récepteur

## Structures (struct)

### Définition et création
Les structures regroupent des champs de différents types :

```go
type Personne struct {
    Nom      string
    Age      int
    Adresse  string
}

// Différentes façons de créer une structure
p1 := Personne{"Alice", 30, "Paris"}  // Ordre des champs important
p2 := Personne{
    Nom: "Bob", 
    Age: 25, 
    Adresse: "Lyon",  // Virgule obligatoire
}
p3 := Personne{Nom: "Charlie"}  // Age et Adresse initialisés à 0/""
var p4 Personne                 // Tous les champs à leur valeur zéro
p5 := new(Personne)             // Retourne un pointeur vers une Personne vide
```

### Accès aux champs
```go
fmt.Println(p1.Nom)   // Alice
p2.Age = 26           // Modification d'un champ

// Avec un pointeur, pas besoin d'utiliser (*p).champ
ptrP := &p1
fmt.Println(ptrP.Nom)  // Alice (équivalent à (*ptrP).Nom)
ptrP.Age++             // Incrémente l'âge
```

### Structures imbriquées
Les structures peuvent contenir d'autres structures :

```go
type Adresse struct {
    Rue     string
    Ville   string
    CodePostal string
}

type Contact struct {
    Nom     string
    Email   string
    Adresse Adresse  // Structure imbriquée
}

c := Contact{
    Nom: "David",
    Email: "david@exemple.com",
    Adresse: Adresse{
        Rue: "123 Rue Principale",
        Ville: "Paris",
        CodePostal: "75001",
    },
}

fmt.Println(c.Adresse.Ville)  // Paris
```

### Structures avec méthodes
Les méthodes sont des fonctions attachées à un type :

```go
// Méthode avec récepteur valeur (ne modifie pas l'instance)
func (p Personne) Présentation() string {
    return fmt.Sprintf("Je m'appelle %s et j'ai %d ans", p.Nom, p.Age)
}

fmt.Println(p1.Présentation())

// Méthode avec récepteur pointeur (peut modifier l'instance)
func (p *Personne) AnniversairePlus() {
    p.Age++
}

p1.AnniversairePlus()  // Modifie p1.Age, même si p1 n'est pas un pointeur
                       // Go convertit automatiquement p1 en &p1
```

## Boucles et conditions

### If / Else
Les conditions en Go sont simples, sans parenthèses :

```go
// Forme basique
if x > 0 {
    // code
} else if x < 0 {
    // code
} else {
    // code
}

// If avec instruction d'initialisation (portée limitée)
if valeur := fonction(); valeur > 10 {
    // valeur accessible ici
} else {
    // et ici
}
// valeur n'est plus accessible ici
```

### Switch
Le `switch` en Go est flexible et n'a pas besoin de `break` :

```go
// Switch sur une valeur
switch jour {
case "lundi", "mardi":    // Plusieurs valeurs possibles
    // code
case "mercredi":
    // code
    fallthrough          // Continue à l'étiquette suivante
case "jeudi":
    // code
default:
    // code
}

// Switch sans expression (comme des if/else)
switch {
case x > 0:
    // code
case x < 0:
    // code
default:
    // code
}

// Switch avec instruction d'initialisation
switch n := rand.Intn(10); {
case n < 3:
    fmt.Println("Petit:", n)
case n < 7:
    fmt.Println("Moyen:", n)
default:
    fmt.Println("Grand:", n)
}
```

### For (classique)
Go n'a qu'un seul mot-clé pour les boucles : `for`

```go
// Boucle traditionnelle
for i := 0; i < 10; i++ {
    // code exécuté 10 fois
}

// Avec plusieurs variables
for i, j := 0, 10; i < j; i, j = i+1, j-1 {
    fmt.Println(i, j)
}
```

### For (comme while)
```go
// Équivalent de "while" dans d'autres langages
i := 0
for i < 10 {
    // code
    i++
}
```

### For (infini)
```go
// Boucle infinie
for {
    // code
    if condition {
        break       // Sortie de boucle
    }
    if autreCondition {
        continue    // Passe à l'itération suivante
    }
}
```

### For range (parcours)
`for range` est utilisé pour itérer sur des collections :

```go
// Pour un tableau/slice
for index, valeur := range tableau {
    fmt.Println(index, valeur)
}

// Pour une map
for clé, valeur := range maMap {
    fmt.Println(clé, valeur)
}

// Pour une chaîne (itère sur les runes, pas les octets)
for index, runeValue := range "Go语言" {
    fmt.Printf("%d: %c\n", index, runeValue)
}

// Si on n'a pas besoin de l'index/clé
for _, valeur := range collection {
    // code
}

// Si on n'a pas besoin de la valeur
for clé, _ := range maMap {
    // code
}
// ou plus simplement
for clé := range maMap {
    // code
}
```

## Collections

### Arrays (tableaux à taille fixe)
Les tableaux ont une taille fixe définie à la compilation :

```go
var a [5]int             // Tableau de 5 entiers (initialisé à 0)
b := [3]int{1, 2, 3}     // Initialisation avec valeurs
c := [...]int{1, 2, 3, 4} // Taille déterminée par le nombre d'éléments

// Accès aux éléments
fmt.Println(a[0])        // Premier élément (index 0)
a[1] = 10                // Modification

// Propriétés
fmt.Println(len(a))      // 5 (longueur)

// Comparaison
fmt.Println(b == [3]int{1, 2, 3})  // true (même contenu)
```

### Slices (tableaux dynamiques)
Les slices sont des références à des tableaux sous-jacents, avec taille dynamique :

```go
var s []int              // Slice d'entiers (nil)
s1 := []int{1, 2, 3}     // Création avec valeurs
s2 := make([]int, 5)     // Crée un slice de 5 éléments (0)
s3 := make([]int, 3, 5)  // Longueur 3, capacité 5

// Création à partir d'un tableau/slice existant
a := [5]int{1, 2, 3, 4, 5}
s4 := a[1:4]             // [2, 3, 4] (éléments d'index 1 à 3)
s5 := a[:3]              // [1, 2, 3] (du début à l'index 2)
s6 := a[2:]              // [3, 4, 5] (de l'index 2 à la fin)
s7 := a[:]               // [1, 2, 3, 4, 5] (tous les éléments)

// Ajout d'éléments
s = append(s, 1, 2, 3)   // Ajoute les éléments à la fin
s = append(s, s1...)     // Ajoute tous les éléments de s1

// Propriétés
fmt.Println(len(s1))     // 3 (longueur)
fmt.Println(cap(s1))     // 3 (capacité)

// Modification 
s1[0] = 10               // Modifie le premier élément

// Copier des slices
dest := make([]int, len(s1))
copied := copy(dest, s1)  // Copie s1 dans dest, retourne le nombre d'éléments copiés
```

### Maps (dictionnaires)
Les maps sont des collections de paires clé-valeur :

```go
var m map[string]int                // Déclare une map nil
m1 := make(map[string]int)          // Crée une map vide
m2 := map[string]int{               // Initialisation
    "un": 1, 
    "deux": 2,
}

// Opérations
m1["trois"] = 3                     // Ajout/modification
valeur, existe := m1["quatre"]      // Vérification d'existence
                                    // valeur = 0, existe = false si la clé n'existe pas
delete(m1, "trois")                 // Suppression

// Parcours
for clé, valeur := range m2 {
    fmt.Println(clé, valeur)
}

// Propriétés
fmt.Println(len(m2))                // 2 (nombre de paires)
```

## Gestion d'erreurs

### Création et retour d'erreurs
Go utilise le type `error` pour gérer les erreurs :

```go
import "errors"

func division(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("division par zéro")
    }
    return a / b, nil  // nil signifie absence d'erreur
}

// Utilisation avec vérification
resultat, err := division(10, 0)
if err != nil {
    fmt.Println("Erreur:", err)
} else {
    fmt.Println("Résultat:", resultat)
}

// Création d'erreur avec format
import "fmt"
err := fmt.Errorf("erreur dans %s: %v", "fonction", valeur)
```

### Erreurs personnalisées
On peut créer des types d'erreur personnalisés :

```go
type MonErreur struct {
    Message string
    Code    int
}

// Implémentation de l'interface error
func (e *MonErreur) Error() string {
    return fmt.Sprintf("Erreur %d: %s", e.Code, e.Message)
}

// Utilisation
func fonctionRisquée() error {
    return &MonErreur{
        Message: "quelque chose s'est mal passé",
        Code:    500,
    }
}

// Vérification du type d'erreur
err := fonctionRisquée()
if err != nil {
    if monErr, ok := err.(*MonErreur); ok {
        fmt.Println("Code:", monErr.Code)
    }
}
```

## Goroutines et Concurrence

### Goroutines (threads légers)
Les goroutines sont des fonctions qui s'exécutent concurremment :

```go
func fonction() {
    // code qui s'exécute en parallèle
    for i := 0; i < 5; i++ {
        fmt.Println("Goroutine:", i)
        time.Sleep(100 * time.Millisecond)
    }
}

// Démarrage d'une goroutine
go fonction()     // Continue immédiatement sans attendre

// Pour éviter que le programme se termine avant la goroutine
fmt.Println("Principal: Attente...")
time.Sleep(1 * time.Second)
```

### Channels (canaux de communication)
Les canaux permettent aux goroutines de communiquer et de se synchroniser :

```go
// Canal non bufferisé (bloque à l'envoi jusqu'à réception)
ch := make(chan int)

// Canal bufferisé (capacité 3)
ch2 := make(chan string, 3)

// Envoi de données (bloquant si canal plein)
ch <- 42

// Réception de données (bloquante si canal vide)
valeur := <-ch

// Fermeture d'un canal (plus d'envois possibles)
close(ch)

// Vérification si un canal est fermé
valeur, ouvert := <-ch
if !ouvert {
    fmt.Println("Canal fermé")
}

// Itération sur un canal jusqu'à fermeture
for valeur := range ch {
    fmt.Println(valeur)
}
```

### Exemple complet avec channels
```go
func générateur(n int) <-chan int {
    ch := make(chan int)
    go func() {
        for i := 0; i < n; i++ {
            ch <- i
        }
        close(ch)
    }()
    return ch
}

func doubleur(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for valeur := range in {
            out <- valeur * 2
        }
        close(out)
    }()
    return out
}

// Utilisation
nombres := générateur(5)      // Génère 0, 1, 2, 3, 4
doubles := doubleur(nombres)  // Transforme en 0, 2, 4, 6, 8

for n := range doubles {
    fmt.Println(n)
}
```

### Select (multiplexage)
`select` permet d'attendre sur plusieurs opérations de canal :

```go
select {
case valeur := <-canal1:
    // Exécuté si canal1 est prêt pour lecture
    fmt.Println("Reçu du canal1:", valeur)

case canal2 <- valeur:
    // Exécuté si canal2 est prêt pour écriture
    fmt.Println("Envoyé sur canal2")

case <-time.After(time.Second):
    // Timeout après 1 seconde
    fmt.Println("Timeout!")

default:
    // Exécuté immédiatement si aucun cas n'est prêt
    // Sans default, select bloque
    fmt.Println("Rien n'est prêt")
}
```

### WaitGroup (synchronisation)
`sync.WaitGroup` permet d'attendre que des goroutines se terminent :

```go
var wg sync.WaitGroup

wg.Add(2)  // Attendre 2 goroutines

go func() {
    // Travail de la première goroutine
    defer wg.Done()  // Signal de fin (équivalent à wg.Add(-1))
    time.Sleep(100 * time.Millisecond)
    fmt.Println("Goroutine 1 terminée")
}()

go func() {
    // Travail de la deuxième goroutine
    defer wg.Done()  // Garantit que Done() est appelé même en cas d'erreur
    time.Sleep(200 * time.Millisecond)
    fmt.Println("Goroutine 2 terminée")
}()

wg.Wait()  // Bloque jusqu'à ce que le compteur atteigne 0
fmt.Println("Toutes les goroutines sont terminées")
```

## Interfaces

### Définition
Une interface définit un ensemble de méthodes :

```go
type Forme interface {
    Aire() float64
    Périmètre() float64
}
```

### Implémentation implicite
Un type implémente une interface simplement en définissant toutes ses méthodes :

```go
type Rectangle struct {
    Longueur, Largeur float64
}

// Rectangle implémente Forme
func (r Rectangle) Aire() float64 {
    return r.Longueur * r.Largeur
}

func (r Rectangle) Périmètre() float64 {
    return 2 * (r.Longueur + r.Largeur)
}

type Cercle struct {
    Rayon float64
}

func (c Cercle) Aire() float64 {
    return math.Pi * c.Rayon * c.Rayon
}

func (c Cercle) Périmètre() float64 {
    return 2 * math.Pi * c.Rayon
}

// Utilisation polymorphique
func afficherInfos(f Forme) {
    fmt.Printf("Aire: %.2f, Périmètre: %.2f\n", f.Aire(), f.Périmètre())
}

// Utilisation
r := Rectangle{5, 3}
c := Cercle{2.5}
afficherInfos(r)  // Aire et périmètre du rectangle
afficherInfos(c)  // Aire et périmètre du cercle
```

### Interface vide
`interface{}` (ou `any` depuis Go 1.18) peut contenir n'importe quel type :

```go
var x interface{}  // Peut contenir n'importe quel type
x = 42
x = "chaîne"
x = struct{ nom string }{"Alice"}

// Type assertion (vérification du type)
if valeur, ok := x.(string); ok {
    fmt.Println("x est une chaîne:", valeur)
} else {
    fmt.Println("x n'est pas une chaîne")
}

// Type switch (cascade de vérifications)
switch v := x.(type) {
case int:
    fmt.Println("x est un entier:", v)
case string:
    fmt.Println("x est une chaîne:", v)
case bool:
    fmt.Println("x est un booléen:", v)
default:
    fmt.Printf("Type inconnu: %T\n", v)
}
```

### Interfaces communes
- `fmt.Stringer`: pour représentation textuelle (`String() string`)
- `io.Reader`: pour lecture de données (`Read(p []byte) (n int, err error)`)
- `io.Writer`: pour écriture de données (`Write(p []byte) (n int, err error)`)
- `error`: pour erreurs (`Error() string`)

## Packages et Imports

### Organisation
```go
// Dans fichier main.go
package main

import (
    "fmt"           // Package standard
    "math"          // Package standard
    m "monprojet/monpackage"  // Alias pour éviter les conflits
    _ "package/inutilisé"     // Import pour effets secondaires (init())
)

func main() {
    fmt.Println(math.Pi)
    fmt.Println(m.MaFonction())
}
```

### Visibilité
- Majuscule au début (exporté): `MaFonction`, `MaVariable`
- Minuscule au début (privé): `maFonction`, `maVariable`

### Structure typique d'un projet Go
```
monprojet/
├── go.mod          // Fichier de configuration du module
├── go.sum          // Verrouillage des dépendances
├── main.go         // Point d'entrée
├── utils/          // Package utilitaire
│   ├── helper.go
│   └── helper_test.go
└── internal/       // Packages privés pour ce module
    └── config/
        └── config.go
```

## Gestion des fichiers

### Lecture
```go
// Lecture complète d'un fichier
contenu, err := os.ReadFile("fichier.txt")
if err != nil {
    log.Fatal(err)
}
fmt.Println(string(contenu))  // contenu est un []byte
```

### Écriture
```go
// Écriture complète d'un fichier
err := os.WriteFile("fichier.txt", []byte("Contenu"), 0644)
if err != nil {
    log.Fatal(err)
}
```

### Entrées / Sorties avancées
```go
// Ouverture en lecture
file, err := os.Open("fichier.txt")
if err != nil {
    log.Fatal(err)
}
defer file.Close()  // Fermeture garantie à la fin de la fonction

// Lecture par tampon
scanner := bufio.NewScanner(file)
for scanner.Scan() {
    ligne := scanner.Text()
    // Traiter la ligne
    fmt.Println(ligne)
}

if err := scanner.Err(); err != nil {
    log.Fatal(err)
}

// Ouverture en écriture
file, err = os.Create("nouveau.txt")
if err != nil {
    log.Fatal(err)
}
defer file.Close()

// Écriture avec bufio
writer := bufio.NewWriter(file)
_, err = writer.WriteString("Première ligne\n")
if err != nil {
    log.Fatal(err)
}
writer.Flush()  // Vide le tampon
```

## Fonctionnalités spéciales

### Defer (exécution différée)
`defer` programme l'exécution d'une fonction pour la fin de la fonction courante :

```go
func exemple() {
    fmt.Println("Début")
    
    // Les appels defer sont empilés (LIFO - dernier entré, premier sorti)
    defer fmt.Println("Defer 1")  // Exécuté en 3ème
    defer fmt.Println("Defer 2")  // Exécuté en 2ème
    defer fmt.Println("Defer 3")  // Exécuté en 1er
    
    fmt.Println("Milieu")
    // Même si un return est exécuté ici, les defer sont appelés
    fmt.Println("Fin")
}
// Sortie:
// Début
// Milieu
// Fin
// Defer 3
// Defer 2
// Defer 1
```

Utilisations communes de `defer` :
1. Fermeture de ressources (fichiers, connexions réseau)
2. Déverrouillage de mutex
3. Mesure de la durée d'exécution
4. Nettoyage en cas d'erreur

```go
// Exemple: mesure de durée
func chronométrer() func() {
    début := time.Now()
    return func() {
        fmt.Printf("Durée: %v\n", time.Since(début))
    }
}

func traitement() {
    defer chronométrer()()  // Noter les doubles parenthèses
    // Traitement long...
    time.Sleep(100 * time.Millisecond)
}
```

### Panic et Recover
`panic` cause l'arrêt immédiat de la fonction et remonte la pile d'appels :

```go
func fonction() {
    fmt.Println("Avant panic")
    panic("une erreur critique")
    fmt.Println("Ce code n'est jamais exécuté")
}
```

`recover` peut capturer une panic et permettre de continuer l'exécution :

```go
func fonction() {
    // Le defer est toujours exécuté, même après panic
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Récupéré de