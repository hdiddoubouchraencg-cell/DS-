# Rapport scientifique — Prétraitement et Analyse Exploratoire  
Réalisé à partir du notebook: machine_learning.ipynb  
Lien du notebook source : https://github.com/hdiddoubouchraencg-cell/DS/blob/1951ae4b900537edad3c1a5b45eaac4c9e5be827/machine_learning.ipynb

Date : 2025-12-03

Résumé
------
Ce rapport présente le prétraitement et l'analyse exploratoire (EDA) réalisés sur le jeu de données "Student Stress Factors (2).csv". Les étapes principales effectuées sont : chargement des données, nettoyage (suppression des duplicatas et standardisation des noms de colonnes), vérification/imputation des valeurs manquantes, mise à l'échelle des variables numériques (Min‑Max scaling), analyse univariée (histogrammes et boxplots), analyse de corrélation et création de nouvelles variables par ingénierie de caractéristiques. Les résultats saillants indiquent une forte suppression de doublons (416/520), des corrélations notables entre la charge d'étude, les maux de tête et le niveau de stress, et deux nouvelles variables ("health_indicator" et "academic_stress_indicator") créées pour résumer certains compromis santé/études.

1. Contexte et objectifs
------------------------
Objectif principal : Préparer et explorer les données pour permettre des analyses statistiques et la construction ultérieure de modèles prédictifs du stress étudiant.

Objectifs secondaires :
- Nettoyage et validation de la qualité des données.
- Mise en forme et transformation pour compatibilité avec des modèles ML.
- Révéler relations potentielles entre stress et facteurs (sommeil, charge d'étude, maux de tête, activités extrascolaires, performance académique).
- Proposer pistes d'analyse et de modélisation futures.

2. Données et chargement
------------------------
- Fichier lu : '/content/Student Stress Factors (2).csv.crdownload' (voir notebook pour le chemin exact).
- Aperçu initial : 520 lignes, 6 colonnes.
- Variables initiales (abrégées) :
  - Kindly Rate your Sleep Quality 😴  -> sleep_quality
  - How many times a week do you suffer headaches 🤕? -> headaches_per_week
  - How would you rate you academic performance 👩‍🎓? -> academic_performance
  - how would you rate your study load? -> study_load
  - How many times a week you practice extracurricular activities 🎾? -> extracurricular_activities_per_week
  - How would you rate your stress levels? -> stress_levels
- Types lus initialement : int64 pour toutes les colonnes.

3. Prétraitement réalisé
------------------------
3.1. Détection et traitement des duplicatas
- Duplicatas détectés : 416 sur 520 (≈80 %).  
- Action : suppression complète des lignes dupliquées (drop_duplicates).  
- Taille après suppression : 104 entrées uniques × 6 colonnes.
- Remarque méthodologique : un taux élevé de duplicatas peut indiquer soit une remontée répétée de sondage, soit des enregistrements distincts avec mêmes réponses. Avant suppression définitive, il est conseillé de vérifier l'origine/des horodatages (si disponibles) ou d'enquêter sur le processus de collecte.

3.2. Standardisation des noms de colonnes
- Renommage pour faciliter l'utilisation en code et la lisibilité :
  - sleep_quality, headaches_per_week, academic_performance, study_load, extracurricular_activities_per_week, stress_levels

3.3. Valeurs manquantes
- Vérification : aucune valeur manquante (0 NaN dans toutes les colonnes après chargement et nettoyage).

3.4. Encodage
- Toutes les variables étaient numériques (ordinales / comptages), aucun encodage supplémentaire requis.

3.5. Mise à l'échelle
- Méthode : MinMaxScaler (scaling dans [0,1]) appliqué à toutes les colonnes.
- Type résultant : float64 pour toutes les variables.
- Raison : homogénéisation des échelles pour méthodes sensibles à l'échelle (p. ex. distance-based, réguliarisations).

4. Analyse exploratoire et résultats
-----------------------------------
4.1. Analyse univariée (distributions)
- sleep_quality : distribution centrée autour de 3–4 (qualité moyenne à bonne). Peu d'outliers.
- headaches_per_week : distribution asymétrique (skew à droite) ; nombreuses réponses à 1 par semaine, quelques cas à 5.
- academic_performance : légère concentration vers 3–4 (bonne performance pour beaucoup).
- study_load : distribution étalée, indication possible de bimodalité (réponses réparties entre faible et élevé).
- extracurricular_activities_per_week : skew à droite, plusieurs répondants avec faible nombre d'activités.
- stress_levels : distribution relativement symétrique autour de 2–4 (majorité en niveau moyen).

4.2. Corrélations (matrice)
- Calcul : corr() sur les variables (après scaling). Valeurs clés rapportées dans le notebook :
  - stress_levels vs study_load : +0.58 (corrélation positive modérée à forte)
  - stress_levels vs headaches_per_week : +0.50 (positive)
  - academic_performance vs headaches_per_week : −0.56 (négative)
  - academic_performance vs stress_levels : −0.46 (négative)
  - sleep_quality vs stress_levels : +0.23 (faible)
  - extracurricular_activities_per_week vs stress_levels : +0.01 (négligeable)
- Interprétation : la charge d'étude et la fréquence des maux de tête sont les facteurs les plus associés au stress déclaré. Une meilleure performance académique semble associée à moins de stress et moins de maux de tête.

4.3. Ingénierie de caractéristiques
- Création de deux variables :
  - health_indicator = sleep_quality − headaches_per_week  
    (concept : évaluer l'équilibre sommeil / maux de tête ; valeurs plus grandes → meilleur état perçu)
  - academic_stress_indicator = study_load − academic_performance  
    (concept : pression académique perçue ; valeurs positives → charge > performance)
- Visualisation des distributions de ces nouvelles variables réalisée dans le notebook (histogrammes / boxplots).

5. Discussion scientifique
--------------------------
5.1. Principaux enseignements
- La charge perçue du travail académique est le facteur le plus lié au stress autotranscrit par les étudiants (corrélation ≈ 0.58).  
- Les maux de tête (headaches_per_week) sont aussi fortement corrélés au stress ; ils sont inversément corrélés à la performance académique.  
- Les activités extrascolaires semblent n’avoir pas d'effet linéaire notable sur le stress dans ce jeu de données (corr ≈ 0.01).

5.2. Validité et limitations
- Taille finale réduite : après suppression des duplicatas, n = 104 — limite la puissance statistique.
- Duplicatas massifs : la suppression a réduit les biais de répétition mais peut aussi avoir éliminé des réponses valides si les réponses identiques étaient indépendantes.
- Données auto‑rapportées : subjectivité et biais (souvenir, désirabilité sociale).
- Variables ordinales traitées comme continues après scaling ; certaines méthodes peuvent exiger une gestion différente (tests non paramétriques).
- Corrélation ≠ causalité : relations linéaires identifiées ne prouvent pas d'effet causal.

6. Recommandations et travaux futurs
-----------------------------------
6.1. Analyses statistiques complémentaires
- Tests de corrélation robustes : tester Pearson et Spearman (pour ordinalité) et rapporter p‑values et intervalles de confiance.
- Réaliser des tests d'association (ANOVA / Kruskal‑Wallis) si l’on segmente par groupes (p. ex. tranches de performance).
- Étudier la distribution des réponses avant et après suppression des duplicatas (comparer statistiques descriptives).

6.2. Modélisation prédictive
- Objectif possible : prédire "stress_levels" (régression continue) ou classifier (binariser le stress : faible vs élevé).
- Modèles candidats : régression linéaire, régression ridge/lasso, arbres (Random Forest, Gradient Boosting), modèles robustes (XGBoost), modèles interprétables (SHAP pour importance).
- Validation : k‑fold cross‑validation (k ≥ 5), courbes d'apprentissage, séparation train/test (stratifiée si classe binaire).
- Métriques : RMSE/MAE pour régression ; précision, rappel, F1, AUC pour classification.

6.3. Ingénierie et enrichissement de données
- Conserver le jeu complet avant suppression définitive des duplicatas et comparer.
- Si possible, récupérer métadonnées (date de réponse, identifiants anonymes) pour détecter réponses répétées non-indépendantes.
- Enrichir par variables démographiques (âge, sexe, année d'étude) pour contrôler les effets de confusion.
- Considérer transformations non linéaires ou interactions (p. ex. study_load × sleep_quality).

6.4. Interprétabilité et éthique
- Utiliser méthodes interprétables (coefficients standardisés, SHAP) pour expliquer prédictions.
- Respecter la vie privée : anonymisation et conformité RGPD si applicable.
- Communiquer résultats avec prudence ; éviter d'attribuer la cause unique du stress à un seul facteur.

7. Résumé des étapes reproduites (extrait de code)
--------------------------------------------------
Exemples d'opérations clés (présent dans le notebook) :
- Chargement :
  - df = pd.read_csv('/content/Student Stress Factors (2).csv.crdownload')
- Suppression des duplicatas :
  - df.drop_duplicates(inplace=True)
- Renommage des colonnes :
  - df.rename(columns={ ... }, inplace=True)
- Vérification des NaN :
  - df.isnull().sum()
- Mise à l'échelle :
  - from sklearn.preprocessing import MinMaxScaler
  - scaler = MinMaxScaler()
  - df = pd.DataFrame(scaler.fit_transform(df), columns=df.columns)
- Création de variables :
  - df['health_indicator'] = df['sleep_quality'] - df['headaches_per_week']
  - df['academic_stress_indicator'] = df['study_load'] - df['academic_performance']

8. Conclusion
-------------
Le jeu de données a été nettoyé, normalisé et exploré. Les résultats montrent que la charge d'étude et la fréquence des maux de tête sont fortement associées au stress auto‑rapporté. Le jeu est maintenant prêt pour des étapes de modélisation supervisée et d'analyse plus fines,
