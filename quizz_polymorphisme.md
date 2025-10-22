# Quiz sur le Polymorphisme, l'Overloading et la Canonical Class Form


---

**1. Qu'est-ce que le polymorphisme ad-hoc en C++ ?**
A) La capacité d'un objet à changer de type à l'exécution
B) La capacité de fonctions ou opérateurs à se comporter différemment selon le type de leurs opérandes
C) L'utilisation de classes abstraites
D) La gestion automatique de la mémoire

---

**2. Pourquoi ne peut-on pas surcharger les opérateurs pour les types primitifs uniquement ?**
A) Parce que le compilateur ne le permet pas
B) Parce que cela causerait des fuites de mémoire
C) Parce que les opérateurs doivent impliquer au moins un type défini par l'utilisateur
D) Parce que les opérateurs sont réservés au système

---

**3. Quel est le rôle du destructeur dans la Canonical Class Form (OCCF) ?**
A) Allouer de la mémoire
B) Copier les objets
C) Libérer les ressources détenues par l'objet
D) Surcharger les opérateurs

---

**4. Que se passe-t-il lors d'une copie superficielle (shallow copy) ?**
A) Les données sont dupliquées
B) Seul le pointeur est copié, pas les données pointées
C) Les ressources sont libérées
D) L'objet est détruit

---

**5. Quelle pratique est recommandée lors de la surcharge de l'opérateur + ?**
A) Modifier l'objet courant
B) Retourner un nouvel objet sans modifier les opérandes
C) Utiliser des variables globales
D) Ignorer la sémantique conventionnelle de l'opérateur

---

**6. Pourquoi faut-il implémenter le destructeur, le constructeur de copie et l'opérateur d'affectation dans une classe qui gère des ressources ?**
A) Pour améliorer la performance
B) Pour garantir la sécurité mémoire et éviter les doubles frees
C) Pour rendre le code plus lisible
D) Pour permettre l'héritage

---

**7. Que permet l'overloading des opérateurs dans une classe ?**
A) D'utiliser des opérateurs avec des objets comme avec les types natifs
B) D'empêcher la copie d'objets
C) De supprimer des méthodes
D) D'automatiser la gestion mémoire

---

**8. Lors d'une auto-affectation (self-assignment) dans l'opérateur =, pourquoi faut-il vérifier si l'objet est le même ?**
A) Pour éviter de modifier des variables globales
B) Pour éviter de supprimer des ressources déjà libérées
C) Pour améliorer la lisibilité
D) Pour permettre l'héritage

---

Voici un exemple de surcharge de l'opérateur de pre incrémentation:

```hpp
class Counter {
private:
    int value;
public:
    Counter& operator++();
```

```cpp
Counter& operator++() {
    ++value;
    return *this;
}
```
**9. Quelle est la syntaxe correcte pour surcharger l'opérateur de post-incrémentation ?**
A)
```cpp
Counter& operator++(int) {
    value++;
    return *this;
}
```
B)
```cpp
Counter& operator+++() {
    value++;
    return *this;
}
```
C)
```cpp
int operator++(int) {
    int temp = value;
    value++;
    return temp;
}
```
D)
```cpp
int ++operator() {
    value++;
}
```

> **Tips :** L'opérateur de post-incrémentation prend un paramètre `int` fictif pour différencier la signature, et il est d'usage de retourner l'ancienne valeur

**Réponses :**
1-B, 2-C, 3-C, 4-B, 5-B, 6-B, 7-A, 8-B, 9-C
