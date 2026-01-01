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
Normalisation  des données conception et entraînement d'un réseau de neurones séquentiel optimisé pour classifier précisément les patientes selon leur niveau de risque de cancer.
* **Répartition  :** répartition des groupe pour l'entrainement et le test.
* **Normalisation :** Utilisation du `StandardScaler` pour mettre à l'échelle les variables numériques.
* **Encodage :** On transforme les réponses en vecteurs via le One-Hot encoding pour les 3 catégories.
* **Construction et entraînement du réseau de neurones** : Initialisation du modèle, démarrage de l'entraînement.
* **Évaluation des performances et analyse du modèle** : Matrice de confusion, Calcul des métriques (Accuracy, Précision, Recall, F1-score).
* **Conclusion** : Le modèle précis mais aveugle aux cas critiques
* Le modèle ne peut pas être déployé en l'état car il présente un risque de sécurité présence de faux négatifs sur les cas graves.

#### 3. Analyse de l'Influence des Facteurs par matrice de corrélation 
Génération d'une matrice de corrélation et d'une heatmap pour identifier et visualiser les facteurs de risque ayant la plus forte influence statistique sur le niveau de risque de cancer
* **Réalisation d'une matrice de Corrélation** des Facteurs de Risque du Cancer du Sein 
* **Conclusion** : Bien que la matrice de corrélation souligne l'influence des facteurs environnementaux (Alcool, Pollution)
* Elle reste un outil limité, elle ne détecte pas les interactions complexes

#### 4. Modèle avec équilibrage des classes par calcul de poids
Application d'une pondération des classes (class_weight) lors du réentraînement pour corriger le déséquilibre des données et forcer le modèle à accorder plus d'importance aux catégories minoritaires du risque élevé.
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
Abaissement du seuil de décision de la classe "High" à 0.15 pour augmenter la sensibilité du modèle et garantir une détection des cas les plus critiques.
* **récupèration** des scores de confiance pour chaque classe
* **Changement du seuil** pour la classe 'high' qui passe à 0.15
* **Évaluation des performances et analyse du modèle** : Matrice de confusion, Calcul des métriques (Accuracy, Précision, Recall, F1-score).
* **Conclusion** Malgré un ajustement de seuil à 0.15, le recall pour les patientes high reste à 0.00.
* Le modèle ne remplit pas son rôle d'alerte. Cette étape démontre qu'un seuil de 0.15 est insuffisant.

#### 7. Test du Modèle avec un seuil de sécurité optimisé à 100% de rappel
Au lieu de laisser le modèle choisir la classe la plus probable, méthode par défaut on force la détection dès l'apparition d'un signal faible.
En calant le curseur sur la probabilité la plus basse attribuée à une vraie malade (final_threshold).
* **abaissement** du seuil de décision pour ne rater aucun cas de risque élevé (High), même si celui-ci n'est pas majoritaire
* **transformation** des vecteurs complexes en une liste de chiffres simples
* **classement** On regarde si la probabilité du risque High (indice 2) atteint ou dépasse le seuil
* Si le risque de danger n'est pas détecté, on choisit la classe qui a le score le plus élevé entre Low (0) et Medium (1)
* **Évaluation des performances et analyse du modèle** : Matrice de confusion, Calcul des métriques (Accuracy, Précision, Recall, F1-score).
* **Conclusion** Rappel de 100% sur le risque "High", ce réglage permet de détecter la totalité des patientes à haut risque (2 sur 2).


#### 8. Test rééquilibrage SMOTE et optimisation du rappel critique
Couplage du rééquilibrage des données par SMOTE et de l'ajustement dynamique du seuil de décision pour garantir une détection exhaustive (100% de rappel) des patientes à haut risque
* **Équilibrage :** Équilibrage des données avec SMOTE, on augmente la représentation de la classe minoritaire pour aider le neurone
* **Entraînement du modèle** Cette méthode m'a permis d'obtenir une bien meilleure sensibilité sur les classes minoritaires (Low et High).
* **Renvoye des probabilités brutes** pour chaque catégorie (Low, Medium, High)
* **Création des probabilités pour la classe 'High'**
* **convertion** au format "One-Hot" (ex: [0, 0, 1]) en un index numérique simple (ex: 2)
* **définition** du seuil de décision basé sur la probabilité la plus basse d'un cas réel "High" afin de garantir un rappel de 100% pour la détection des risques critiques.
* **Application du nouveau seuil**
* **Évaluation des performances et analyse du modèle** : Matrice de confusion, Calcul des métriques (Accuracy, Précision, Recall, F1-score).
* **Conclusion** Le modèle identifie avec succès les 2 patientes à risque élevé présentes dans les données
    
#### 9.Test équilibrage hybride SMOTE-Tomek et Optimisation du Rappel
Couplage de la technique SMOTE-Tomek au calibrage d'un seuil de décision critique pour garantir un rappel de 100% sur l'identification des patientes à haut risque.
* **Équilibrage et Nettoyage avec SMOTE-Tomek**
* **Entraînement du modèle**
* **sélection** de la probabilité la plus basse parmi les cas réels à haut risque pour fixer un seuil de sécurité garantissant un rappel de 100 %.
* **# Application du nouveau seuil**
* **Évaluation des performances et analyse du modèle** : Matrice de confusion, Calcul des métriques (Accuracy, Précision, Recall, F1-score).
* **Conclusion** Le modèle identifie avec succès les 2 patientes à risque élevé présentes dans les données
Cette approche permet d'améliorer la précision de la classe "High" en passant 0.33 à 0.40 pour la précision high






---

### 🏁 Conclusion
Ce travail m'a permis de démontrer que la performance d'un projet médical ne repose pas uniquement sur la précision globale, mais sur la sécurité des prédictions. 

En associant la puissance du **Deep Learning** à la robustesse d'un **Balanced Random Forest**, **j'ai créé** une solution fiable. L'ajustement du seuil de décision que **j'ai effectué** transforme l'algorithme en un véritable outil de protection, assurant une détection exhaustive des cas critiques pour le cabinet médical.

---

### 📂 Structure du dépôt
* `deep_learning.ipynb` : Mon programme complet avec analyses et graphiques.
* `cancer-risk-factors.csv` : Le jeu de données source.
* `requirements.txt` : Bibliothèques nécessaires (TensorFlow, Scikit-Learn, Imbalanced-Learn).
