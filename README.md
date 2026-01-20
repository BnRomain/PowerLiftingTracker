# 🏋️ PowerLift Tracker

Un système intelligent de suivi d'entraînement de force développé sur Google Sheets, intégrant calculs automatisés d'e1RM, gestion de charge progressive et analyse de fatigue.

## 📸 Aperçu

[Insérer ici 2-3 screenshots de ton sheet : vue d'ensemble, graphiques, tableau de suivi]

## 🎯 Problématique

En tant que pratiquant de powerlifting, j'avais besoin d'un outil pour :
- Planifier mes séances avec précision (charges, répétitions, RPE)
- Suivre ma progression sur les mouvements principaux (Squat, Bench Press, Deadlift)
- Adapter automatiquement mes charges en fonction de mes performances réelles
- Détecter la fatigue accumulée pour optimiser ma récupération

Les applications existantes manquaient de personnalisation et ne prenaient pas en compte certains aspects techniques comme le poids de corps pour les exercices lestés (tractions, dips) et/ou étaient payantes.

## ✨ Fonctionnalités

### 📊 Calcul automatique d'e1RM (estimated 1 Rep Max)
- Basé sur la **RPE Chart** (Rate of Perceived Exertion)
- Calcul en temps réel selon : charge utilisée, nombre de répétitions, RPE déclaré
- Gestion spécifique pour exercices au poids de corps (Pull-ups, Dips) intégrant le bodyweight

### 🎯 Planification intelligente des charges
- Calcul automatique de l'**Eload** (charge prévue) basé sur un pourcentage de l'e1RM
- Ajustement hebdomadaire des références selon les performances réelles
- Arrondi automatique au multiple de 2,5 kg (standard des disques de salle de sport)

### 📈 Suivi de progression
- **e1RM moyens hebdomadaires** pour chaque mouvement principal
- Calcul de progression semaine par semaine (en kg et pourcentage)
- Tracking du volume total (charge × reps) par mouvement
- Suivi du nombre de répétitions total par exercice

### 🔥 Indice de fatigue
- Calcul automatique : `RPE réel - RPE prévu`
- Moyenne hebdomadaire pour détecter la surcharge
- Indicateurs visuels :
  - **Positif** : Fatigue accumulée
  - **Négatif** : Bonne forme
  - **~0** : Conforme aux prévisions

### ⚖️ Gestion du poids de corps
- Tracking quotidien du bodyweight par séance
- Calcul automatique du poids de corps moyen hebdomadaire
- Intégration dans les calculs d'e1RM pour exercices lestés
- Affichage intelligent : seul le lest est affiché, mais le calcul inclut le poids total

### 📉 Visualisation des données
- Graphiques d'évolution des e1RM sur plusieurs semaines
- Histogramme de l'indice de fatigue par exercice
- Suivi de l'évolution du poids de corps

## 🛠️ Technologies & Compétences

### Formules avancées Google Sheets
- **ArrayFormula** : Calculs matriciels pour moyennes conditionnelles
- **MOYENNE.SI / MOYENNE.SI.ENS** : Moyennes avec critères multiples
- **SI imbriqués** : Logique conditionnelle complexe (jusqu'à 6 niveaux)
- **SIERREUR** : Gestion robuste des erreurs (#N/A, divisions par zéro)
- **ESTNUM** : Validation de données numériques
- **ARRONDI.AU.MULTIPLE** : Adaptation aux standards matériels

### Data Management
- Architecture multi-feuilles : 1 semaine = 1 feuille
- Référencement inter-feuilles pour suivi longitudinal
- Gestion dynamique des données manquantes (séances ratées)

### Data Visualization
- Graphiques en colonnes et lignes pour tendances
- Mise en forme conditionnelle pour indicateurs visuels
- Design épuré et lisible

## 🔧 Formules clés

### Calcul e1RM avec gestion poids de corps
```excel
=SI(OU(B12="pu";B12="dp");((J12+A12)*100)/L12;(J12*100)/L12)
```
- Si Pull-up ou Dips : ajoute le bodyweight avant calcul
- Sinon : calcul standard avec charge seule

### e1RM moyen hebdomadaire (ignore cellules vides)
```excel
=ARRONDI(ArrayFormula(SIERREUR(MOYENNE(SI($B$11:$B$57="sq";SI(ESTNUM($M$11:$M$57);$M$11:$M$57)));""));1)
```
- Filtre par label d'exercice
- Ignore les #N/A et cellules vides
- Arrondi à 1 décimale

### Calcul Eload avec soustraction poids de corps
```excel
=SI(B12="ac";"";SI(OU(B12="pu";B12="dp");ARRONDI.AU.MULTIPLE((I12/100)*SI(B12="pu";$C$6;$C$7)-A12;2,5);ARRONDI.AU.MULTIPLE(SI(B12="sq";(I12/100)*$C$2;SI(B12="bn";(I12/100)*$C$3;SI(B12="dl";(I12/100)*$C$4;"")));2,5)))
```
- Calcule le % de l'e1RM de référence
- Soustrait le poids de corps pour pu/dp
- Arrondit au multiple de 2,5 kg

### Indice de fatigue
```excel
=SI(ET(K11<>"";ESTNUM(K11));K11-E11;"")
```
- Différence entre RPE réel et RPE prévu
- Gestion des cellules vides

## 📊 Exemple d'utilisation

**Lundi - Séance Deadlift**
1. Mon e1RM Deadlift de référence : **190 kg**
2. Séance prévue : Sumo 2×8 @ RPE 7 (73,9% = **140 kg**)
3. Je charge **140 kg** (Eload calculé automatiquement)
4. Je réalise **2×8 @ 135 kg** (j'ai mis un peu moins lourd)
5. RPE réel ressenti : **7** (conforme)
6. e1RM calculé : **182,6 kg** (basé sur 135kg × 8 reps @ RPE 7)
7. Indice de fatigue : **0** (7 - 7 = conforme aux prévisions)

**Fin de semaine**
- e1RM moyen Deadlift calculé : **187,3 kg** (moyenne des 2 séances DL)
- Cette valeur devient la référence pour la semaine suivante

## 🚀 Évolutions futures

- [ ] Migration vers React + Firebase pour une vraie web app
- [ ] Comparaison aux standards de force (Beginner/Intermediate/Advanced/Elite)
- [ ] Export PDF des programmes hebdomadaires
- [ ] Application mobile

## 📝 Utilisation

1. **Dupliquer le template** : [Lien vers Google Sheet en mode template]
2. **Remplir tes MAX actuels** dans la section "MAXs"
3. **Planifier ta semaine** en remplissant exercices, sets, reps et RPE prévus
4. **Enregistrer tes performances** : charge utilisée, RPE réel
5. **Analyser** : e1RM, progression, indice de fatigue sont calculés automatiquement

## 👤 Auteur

**Romain Ben**
- À la recherche d'une alternance en [domaine]
- Passionné de développement et de powerlifting

## 📧 Contact

- LinkedIn : [ton profil]
- Email : [ton email]
- Portfolio : [ton site si tu en as un]

---

⭐ Si ce projet vous intéresse, n'hésitez pas à le star !
