## <font color=darkred> **DM de Deep Learning** : Classification du Risque Cancérologique

###  Introduction et Contexte
* Ce projet s'inscrit dans une démarche d'aide au diagnostic pour un cabinet médical.
* Actuellement, le dépistage du cancer du sein repose principalement sur l'âge des patientes.
* Cependant, le risque est une donnée multifactorielle qui ne peut se limiter à un seul critère.

**L'objectif de mon modèle** est d'élargir les recommandations de dépistage en intégrant 21 critères biologiques et environnementaux. J'ai utilisé le jeu de données `cancer-risk-factors` pour classifier le risque en trois catégories (**Low, Medium, High**).

---

### Démarche Méthodologique 

#### 1. Analyse Exploratoire des données 
* J'ai commencé par un nettoyage des données (présence de doublon, suppression des colonnes inutiles, sélection du cancer du sein uniquement ) 
* Exploration des 21 variables (Age, BMI, Smoking, Genetic factors, etc.).
* ** Observation clé** : J'ai identifié un déséquilibre de classe majeur (19.3% classe "low", 77,8 % de risque "Medium"et 2.8% classe "high").
  
#### 2. Pipeline de Prétraitement
Pour préparer les données, j'ai mis en place les étapes suivantes :
* **Normalisation :** Utilisation du `StandardScaler` pour mettre à l'échelle les variables numériques.
* **Encodage :** Transformation des données textuelles en formats numériques.
* **Stratification : J'ai découpé les données en ensembles d'entraînement et de validation en veillant à conserver la proportion réelle de chaque classe.

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
