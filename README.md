## <font color=darkred> **DM de Deep Learning** : Classification du Risque Cancérologique

###  Introduction et Contexte
* Ce projet s'inscrit dans une démarche d'aide au diagnostic pour un cabinet médical.
* Actuellement, le dépistage du cancer du sein repose principalement sur l'âge des patientes.
* Cependant, le risque est une donnée multifactorielle qui ne peut se limiter à un seul critère.

**L'objectif de mon modèle** est d'élargir les recommandations de dépistage en intégrant 21 critères biologiques et environnementaux. J'ai utilisé le jeu de données `cancer-risk-factors` pour classifier le risque en trois catégories (**Low, Medium, High**).

---

### Démarche Méthodologique 

#### 1. Analyse exploratoire des données 
Pour préparer les données, j'ai mis en place les étapes suivantes :
* J'ai commencé par un nettoyage des données (présence de doublon, suppression des colonnes inutiles, sélection du cancer du sein uniquement ) 
* Exploration des 21 variables (Age, BMI, Smoking, Genetic factors, etc.).
* **Observation clé** : J'ai identifié un déséquilibre de classe majeur (19.3% classe "low", 77,8 % de risque "Medium"et 2.8% classe "high").
  
#### 2.  Réseau de neurones artificiels avec **TensorFlow/Keras**
* **Répartition  :** répartition des groupe pour l'entrainement et le test.
* **Normalisation :** Utilisation du `StandardScaler` pour mettre à l'échelle les variables numériques.
* **Encodage :** On transforme les réponses en vecteurs via le One-Hot encoding pour les 3 catégories.
* **Construction et entraînement du réseau de neurones** : Initialisation du modèle, démarrage de l'entraînement.
* **Évaluation des performances et analyse du modèle** : Matrice de confusion, Calcul des métriques (Accuracy, Précision, Recall, F1-score).
* **Conclusion** : Le modèle précis mais aveugle aux cas critiques
* Le modèle ne peut pas être déployé en l'état car il présente un risque de sécurité présence de faux négatifs sur les cas graves.

#### 3. Analyse de l'Influence des Facteurs par matrice de corrélation 
* **Réalisation d'une matrice de Corrélation** des Facteurs de Risque du Cancer du Sein 
* **Conclusion** : Bien que la matrice de corrélation souligne l'influence des facteurs environnementaux (Alcool, Pollution)
* Elle reste un outil limité, elle ne détecte pas les interactions complexes

#### 4. Modèle avec équilibrage des classes par calcul de poids
* **Calcule automatiquement** des poids, plus une catégorie est petite, plus son poids devient grand.
* **Entraînement** avec les poids calculés.
* **Prédiction** à nouveau sur le jeu de validation
* **Évaluation des performances et analyse du modèle** : Matrice de confusion, Calcul des métriques (Accuracy, Précision, Recall, F1-score).
* **Conclusion** Malgré les 91 % de bonnes prédictions, il échoue à identifier les urgences vitales.

#### 5. Test de l'équilibrage par augmentation de données synthétiques (SMOTE)
Contrairement à la pondération qui donne plus d'importance aux erreurs, SMOTE agit directement sur la structure du jeu de données
* **Initialisation de SMOTE** : J'ai configuré l'algorithme pour équilibrer automatiquement les classes de risque en créant des données synthétiques basées sur les deux plus proches voisins.
* **Application du rééquilibrage** : J'ai appliqué SMOTE exclusivement sur mes données d'entraînement pour corriger le fort déséquilibre sans biaiser mon jeu de validation.
* **Entraînement optimisé** : J'ai entraîné mon modèle de Deep Learning sur les données resamplées pour garantir une reconnaissance équitable de chaque niveau de risque.
* **Prédiction sur les données** de validation on utilise le modèle entraîné sur les données SMOTE.
* **Évaluation des performances et analyse du modèle** : Matrice de confusion, Calcul des métriques (Accuracy, Précision, Recall, F1-score).
* **Conclusion** Le modèle est toujours performant accuracy 91% , mais il échoue toujours sur la détection des 2 cas high.


#### 6. Test de l'ajustement du seuil de décision pour la gestion du risque critique
Contrairement à la pondération qui donne plus d'importance aux erreurs, SMOTE agit directement sur la structure du jeu de données
* **Initialisation de SMOTE** : J'ai configuré l'algorithme pour équilibrer automatiquement les classes de risque en créant des données synthétiques basées sur les deux plus proches voisins.
* **Application du rééquilibrage** : J'ai appliqué SMOTE exclusivement sur mes données d'entraînement pour corriger le fort déséquilibre sans biaiser mon jeu de validation.
* **Entraînement optimisé** : J'ai entraîné mon modèle de Deep Learning sur les données resamplées pour garantir une reconnaissance équitable de chaque niveau de risque.
* **Prédiction sur les données** de validation on utilise le modèle entraîné sur les données SMOTE.
* **Évaluation des performances et analyse du modèle** : Matrice de confusion, Calcul des métriques (Accuracy, Précision, Recall, F1-score).
* **Conclusion** Le modèle est toujours performant accuracy 91% , mais il échoue toujours sur la détection des 2 cas high




#### 3. Architecture du Modèle Deep Learning
**J'ai utilisé un réseau de neurones artificiels avec **TensorFlow/Keras** :
* **Structure :** Une architecture multicouche pour extraire les corrélations complexes entre les facteurs.
* **Régularisation : J'ai intégré des couches *Dropout* pour éviter le surapprentissage.

#### 4. Algorithmique d'Ensemble : Balanced Random Forest
J'ai testé un modèle de forêt aléatoire équilibrée :
* **Équilibrage :** Utilisation d'une stratégie de sous-échantillonnage pour corriger le biais du dataset.
* Cette méthode m'a permis d'obtenir une bien meilleure sensibilité sur les classes minoritaires (Low et High).


#### 5. Optimisation de la Sécurité Médicale
Dans la phase finale, **je me suis concentré** sur l'ajustement du seuil de décision :
* **Priorité au Rappel :** **J'ai calibré** le seuil de probabilité pour minimiser les faux négatifs.
* **Résultat :** Mon modèle atteint un **Rappel (Recall) de 100%** sur la classe "High", garantissant qu'aucun profil à haut risque ne soit ignoré.

---

### 🏁 Conclusion
Ce travail m'a permis de démontrer que la performance d'un projet médical ne repose pas uniquement sur la précision globale, mais sur la sécurité des prédictions. 

En associant la puissance du **Deep Learning** à la robustesse d'un **Balanced Random Forest**, **j'ai créé** une solution fiable. L'ajustement du seuil de décision que **j'ai effectué** transforme l'algorithme en un véritable outil de protection, assurant une détection exhaustive des cas critiques pour le cabinet médical.

---

### 📂 Structure du dépôt
* `deep_learning.ipynb` : Mon programme complet avec analyses et graphiques.
* `cancer-risk-factors.csv` : Le jeu de données source.
* `requirements.txt` : Bibliothèques nécessaires (TensorFlow, Scikit-Learn, Imbalanced-Learn).
