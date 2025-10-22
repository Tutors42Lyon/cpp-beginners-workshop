# Quiz sur le Subtype Polymorphism, classes abstraites et interfaces (C++98)

---

1. Pourquoi doit-on déclarer un destructeur `virtual` dans une classe de base destinée à être utilisée polymorphiquement ?
A) Pour améliorer les performances
B) Pour que la destruction via un pointeur de base appelle aussi le destructeur dérivé
C) Pour empêcher la copie d'objets
D) Pour empêcher l'instanciation de la classe

---

2. Que se passe-t-il si on appelle une méthode virtuelle depuis le constructeur de la classe de base ?
A) L'appel dispatchera vers l'implémentation dérivée
B) L'appel dispatchera vers l'implémentation la plus dérivée disponible au moment de l'appel
C) L'appel utilisera l'implémentation de la classe en cours de construction (base)
D) Comportement indéfini

---

3. Qu'est-ce que l'object slicing ?
A) Copier un pointeur vers un objet dérivé dans un pointeur base
B) Perdre les champs et le comportement dérivés quand on stocke un objet dérivé par valeur dans un objet base
C) Utiliser des références pour éviter la copie
D) Une optimisation du compilateur

---

4. Quelle est la meilleure façon d'accepter plusieurs types dérivés pour une opération commune sans casser le polymorphisme ?
A) Passer par valeur la base (Base b)
B) Utiliser des pointeurs ou références vers la base
C) Dupliquer le code pour chaque type concret
D) Utiliser des templates uniquement

---

5. Lequel des éléments suivants définit une classe abstraite ?
A) Une classe avec au moins un constructeur protégé
B) Une classe qui contient au moins une fonction virtuelle pure ( = 0 )
C) Une classe qui a un destructeur non virtuel
D) Une classe avec des membres privés

---

6. Quel est un pattern correct pour implémenter une "interface" en C++98 ?
A) Classe avec uniquement des fonctions membres non-virtuelles
B) Classe avec des fonctions membres concrètes
C) Classe composée uniquement de fonctions virtuelles pures et d'un destructeur virtuel
D) Utiliser `interface`

---

7. Concernant la performance, quelle affirmation est vraie à propos des appels virtuels ?
A) Ils sont toujours plus lents d'un facteur significatif et doivent être évités partout
B) Ils ont un léger coût d'indirection mais sont acceptables
C) Ils sont volatilisés par le compilateur et ne coûtent rien
D) Ils nécessitent un garbage collector

---

9. Quelle bonne pratique faut-il suivre pour une classe qui va être utilisée comme interface (pure abstract) ?
A) Lui ajouter des membres d'état pour partager des données entre implémentations
B) Lui fournir un destructeur non virtuel pour économiser de la mémoire
C) Ne pas ajouter d'état, garder uniquement des fonctions virtuelles pures et un destructeur virtuel
D) Toujours définir des opérateurs d'affectation

---

Réponses :

1 - B
    - Sans destructeur `virtual`, `delete base_ptr;` n'appellera pas le destructeur dérivé → fuite .

2 - C
    - Pendant la construction la table virtuelle correspond à la classe en cours : les appels virtuels appellent l'implémentation de la classe construite (la base), pas la dérivée.

3 - B
    - Le slicing survient quand on copie un objet dérivé dans un objet base par valeur : les parties dérivées sont perdues.

4 - B
    - Utiliser des pointeurs ou références à la base permet la résolution dynamique et évite le slicing.

5 - B
    - Une fonction virtuelle pure ( = 0 ) rend la classe abstraite.

6 - C
    - En C++98 on implémente une interface par une classe ne contenant que des fonctions virtuelles pures et un destructeur virtuel.

7 - B
    - Les appels virtuels impliquent une indirection (vtable) : petit coût mais souvent acceptable.

9 - C
    - Une interface doit déclarer le contrat (fonctions) sans état ; ajouter de l'état rompt l'abstraction. Toujours fournir un destructeur virtuel.

---

Notes et suggestions :
- Le format est similaire à `quizz_polymorphisme.md`. Si vous voulez, je peux :
  - Ajouter 2–3 questions pratiques (petits extraits de code à analyser)
  - Traduire en anglais
  - Intégrer explications plus détaillées pour chaque réponse
