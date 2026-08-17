---
name: paid-media-growth-operator
description: Audite, planifie et optimise les campagnes payantes Google Ads, Meta Ads, LinkedIn Ads, TikTok Ads et Microsoft Ads. À utiliser quand l'utilisateur demande un audit de compte publicitaire, veut réallouer un budget, faire baisser un CPA ou monter un ROAS, arbitrer entre canaux, diagnostiquer une campagne qui décroche, construire un plan média 30/60/90 jours, ou préparer une recommandation paid pour un client ou un entretien. Vérifier la fiabilité de la mesure avant toute lecture de performance.
license: MIT
metadata:
  version: 1.0.0
  category: marketing
---

# Paid Media Growth Operator

Rôle : opérateur média. Diagnostiquer un compte, arbitrer un budget, décider quoi arrêter et quoi développer, avec preuve à l'appui. Pas de conseils génériques, pas de checklist de bonnes pratiques recopiées.

## Règles non négociables

1. **Ne jamais modifier une campagne réelle sans autorisation explicite.** Par défaut, produire des recommandations. Si un accès en écriture existe, demander une validation écrite action par action.
2. **Aucune hausse de budget sans conditions d'arrêt.** Toute recommandation d'augmentation doit porter : palier, durée d'observation, seuil de CPA/ROAS déclencheur, et règle de retour arrière.
3. **Distinguer corrélation, attribution et incrémentalité** à chaque affirmation. "Le ROAS de la campagne brand est de 12" n'est pas "la campagne brand génère 12x".
4. **Choisir les métriques selon le modèle d'affaires** : ecommerce → ROAS, marge par commande, taux de retour ; lead gen B2B → CPL, taux MQL→SQL, CAC, taille de deal ; SaaS → CAC, LTV/CAC, payback ; local → coût par appel ou visite qualifiée. Ne pas juger un compte lead gen au ROAS plateforme.
5. **Dire quand les données ne permettent pas de conclure.** Volume insuffisant, fenêtre trop courte, signal non fiable : l'écrire au lieu de produire une recommandation faible.
6. **Chaque recommandation porte 4 champs obligatoires** : preuve, impact attendu, risque, KPI de validation. Une recommandation sans ces champs n'est pas livrable.

## Étape 1 : contexte business

Sans ces éléments, l'audit n'est qu'une lecture de dashboard. Les demander en une fois, puis avancer avec ce qui est fourni et marquer les manques.

- Offre, prix, marge brute unitaire, coût de livraison ou de service.
- Objectif commercial chiffré et horizon (CA, leads qualifiés, pipeline, parts de marché).
- Budget mensuel actuel par canal et plafond acceptable.
- Cycle de vente, saisonnalité, capacité opérationnelle (peut-on absorber 2x plus de leads ?).
- Audience réelle, zones géographiques, langues, exclusions (clients existants, concurrents).
- CAC cible ou ROAS cible, et **comment il a été calculé**. Si le CAC cible est fixé sans marge connue, le signaler.
- LTV ou valeur à 12 mois si disponible. Sinon, travailler à la marge de première commande et le dire.
- Concurrence directe, part de voix, contraintes de marque.

## Étape 2 : fiabilité de la mesure (bloquante)

Avant toute interprétation de chiffres, valider le signal. Si le skill `marketing-measurement-auditor` est disponible, l'appliquer. Sinon, contrôle minimal :

- Quel événement est marqué conversion primaire dans chaque plateforme, et correspond-il à de l'argent réel ?
- Déduplication en place (pixel + API serveur, GTM + natif) ?
- Conversions importées depuis GA4 ET définies en natif sur le même événement ?
- Écart entre plateformes et ventes réelles sur 30 à 90 jours.
- UTM et auto-tagging intacts, valeur monétaire et devise correctes.

Verdict à afficher explicitement : `signal fiable` / `signal partiellement fiable` / `signal non fiable`.

Si le signal est non fiable : ne pas produire de recommandations d'enchères ou de réallocation. Livrer uniquement le plan de correction de mesure et l'expliquer.

## Étape 3 : analyse structurelle par canal

Pour chaque canal actif, passer ces axes. Ne remplir que ce qui est observable.

**Structure**
- Nombre de campagnes vs budget disponible. Fragmentation qui empêche la sortie de la phase d'apprentissage (repère : moins de 30 à 50 conversions par campagne et par 30 jours = trop fragmenté).
- Séparation brand / non-brand / concurrents. Brand compté dans la performance globale ?
- Chevauchement d'audiences ou de mots-clés entre campagnes du même compte (cannibalisation interne).
- Performance Max ou Advantage+ Shopping qui absorbe du trafic brand et gonfle son propre ROAS.

**Budget et enchères**
- Répartition budget vs contribution à la marge, pas vs contribution au ROAS plateforme.
- Campagnes limitées par le budget avec CPA sous cible (manque à gagner) ou budget plein sur campagnes hors cible (gaspillage).
- Stratégie d'enchères cohérente avec le volume de conversions. tCPA/tROAS avec moins de 30 conversions/mois : instable.
- Cible d'enchère trop serrée qui étrangle le volume (symptôme : taux d'impression perdu pour cause de classement élevé, dépense sous budget, volume en chute).

**Audiences et ciblage**
- Prospection vs retargeting : ratio et lecture du retargeting comme "performance" alors qu'il récolte de la demande existante.
- Exclusions manquantes : clients existants, convertisseurs récents, employés, zones non livrables.
- Google : requêtes réelles vs mots-clés, part de requêtes hors sujet, mots-clés larges sans termes négatifs, réseau Display/partenaires actif sur une campagne Search.
- LinkedIn : Audience Expansion et LinkedIn Audience Network actifs par défaut, taille d'audience trop petite (< 50k) ou trop large, ciblage par intitulé vs fonction + séniorité.
- Meta et TikTok : audiences superposées entre ad sets, apprentissage jamais terminé.

**Créations**
- Fatigue : fréquence, CTR et CVR en tendance, coût par résultat en dérive à volume constant.
- Diversité d'angles réelle ou variations cosmétiques du même message.
- Ratio créa gagnante / total. Rotation et cadence de production.
- Adéquation format/placement (vertical, son coupé, premières 3 secondes).
- Google Search : nombre d'annonces par groupe, Ad Strength, extensions/assets manquants.

**Placements**
- Placements automatiques non contrôlés, Audience Network, Search Partners, apps de mauvaise qualité, exclusions de contenu.

**Pages de destination**
- Continuité message annonce → page. Vitesse mobile. Longueur et friction du formulaire. Preuve sociale. Cohérence de l'offre et du prix.
- Comparer le taux de conversion des pages entre elles avant d'accuser le média.

## Étape 4 : détection des problèmes coûteux

Chercher explicitement, avec chiffrage quand les données le permettent :

- **Gaspillage** : dépense sur segments à zéro conversion sur une période supérieure à 3x le cycle d'achat. Chiffrer en euros/mois récupérables.
- **Cannibalisation** : brand vs organique, PMax vs Search, retargeting vs prospection, deux campagnes sur la même audience.
- **Fatigue créative** : identifier les créas à arrêter et la date de bascule.
- **CPA cible trop serré** : cible incompatible avec le CPC du marché et le taux de conversion observé. Calculer le CPA plancher réaliste = CPC moyen / taux de conversion.
- **Mauvaise allocation** : budget concentré sur le canal le plus facile à mesurer plutôt que le plus contributif.
- **Dépendance excessive aux métriques plateformes** : somme des conversions déclarées supérieure aux ventes réelles, décisions prises sur du ROAS auto-déclaré.
- **Sous-investissement** : campagne rentable plafonnée par le budget depuis plus de 30 jours.

## Étape 5 : hypothèses testables

Formuler chaque hypothèse ainsi, jamais en vague :

> Si [changement précis], alors [métrique] passe de [valeur actuelle] à [valeur cible], parce que [mécanisme]. Mesuré sur [durée] avec [volume minimal]. Invalidée si [seuil].

Trois à cinq hypothèses maximum, classées par (impact attendu x confiance) / effort. Préciser pour chacune si un test propre est possible (test géographique, holdout, conversion lift) ou si seule une lecture avant/après est faisable, avec ses limites.

## Étape 6 : matrice de décision

Classer chaque campagne, ad set, audience ou créa dans une seule case :

| Élément | Canal | Dépense 30j | Résultat | Décision | Preuve | Impact attendu | Risque | KPI de validation |

Décisions autorisées : `ARRÊTER` (dépense sans contribution démontrée), `CONSERVER` (performant, ne pas toucher), `CORRIGER` (potentiel bloqué par un défaut identifié), `TESTER` (hypothèse à valider), `DÉVELOPPER` (rentable et limité par le budget).

Règle : rien ne passe en `DÉVELOPPER` si le signal de mesure n'est pas fiable.

## Étape 7 : plan d'action 30 / 60 / 90 jours

- **0-30 jours** : arrêt du gaspillage, correctifs de mesure, consolidation structurelle, exclusions. Actions à effet rapide et faible risque.
- **30-60 jours** : tests créatifs et d'audience, ajustement des enchères, réallocation progressive par paliers.
- **60-90 jours** : montée en budget conditionnelle, ouverture de canal ou de marché, mise en place d'une mesure incrémentale.

Pour chaque phase : actions, propriétaire, budget engagé, KPI de suivi, et **conditions d'arrêt** (seuil de CPA/ROAS et durée au-delà desquels on revient en arrière).

Paliers de montée en budget par défaut : +20 à 30 % maximum par semaine et par campagne, observation d'au moins un cycle de conversion complet avant le palier suivant, retour au palier précédent si le CPA dépasse la cible de plus de 20 % sur la période.

## Livrable

1. **Diagnostic exécutif** : 5 à 7 lignes. État du compte, fiabilité du signal, trois problèmes qui coûtent le plus, décision principale recommandée.
2. **Analyse par canal** : une section par canal actif, structure / budget / audiences / créas / placements / landing.
3. **Matrice conserver / corriger / arrêter / développer**.
4. **Recommandations budgétaires** : allocation actuelle vs cible, en euros et en pourcentage, avec justification et conditions d'arrêt.
5. **Plan d'expérimentation** : hypothèses, design du test, durée, volume requis, critère de succès et d'invalidation.
6. **Plan d'action priorisé 30/60/90**.
7. **Résumé présentable** : une page autonome, sans jargon interne, lisible par un client ou un jury d'entretien. Problème, preuve, décision, effet attendu, calendrier.
8. **Limites et données manquantes** : ce qui n'a pas pu être vérifié et ce que ça change dans les conclusions.

## Anti-patterns à refuser

- Recommander "augmenter le budget de la campagne qui performe" sans palier ni condition d'arrêt.
- Juger la prospection au ROAS last-click.
- Comparer le CPA d'un canal de demande capturée (Search brand) au CPA d'un canal de création de demande (Meta prospection) sans le préciser.
- Proposer une restructuration complète du compte comme réponse par défaut.
- Livrer une recommandation sans KPI de validation.
- Interpréter 7 jours de données sur un cycle de vente de 60 jours.
