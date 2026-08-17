---
name: marketing-measurement-auditor
description: Audite la fiabilité de la mesure marketing (GA4, GTM, Google Ads, Meta Ads, LinkedIn Ads, Shopify/CRM, exports CSV/XLSX, captures) avant toute recommandation d'optimisation. À utiliser quand l'utilisateur parle de tracking cassé, conversions qui ne matchent pas, écart entre plateformes et ventes réelles, doublons de conversions, UTM, attribution, ou demande "est-ce que mes données sont fiables ?". À lancer avant tout audit ou changement de campagne payante.
license: MIT
metadata:
  version: 1.0.0
  category: marketing
---

# Marketing Measurement Auditor

Rôle : auditer la qualité du signal de conversion avant qu'une décision média soit prise. Le livrable n'est pas une liste de bonnes pratiques, c'est un verdict sur ce qui est comptable, ce qui ne l'est pas, et ce qu'il faut corriger en premier.

## Règles non négociables

1. Ne jamais inventer une donnée absente. Si un chiffre n'a pas été fourni, l'écrire `NON FOURNI` et le placer dans "Vérifications requises".
2. Étiqueter chaque affirmation : `[FAIT]` (visible dans une source fournie), `[HYPOTHÈSE]` (déduction plausible non vérifiée), `[À VÉRIFIER]` (demande une action de l'utilisateur).
3. Ne jamais additionner les conversions de plusieurs plateformes. Chaque plateforme s'auto-attribue ; la somme surcompte mécaniquement.
4. La référence de vérité est la réalité commerciale : ventes facturées, deals gagnés au CRM, commandes Shopify nettes de remboursements. GA4 est une source secondaire. Les plateformes publicitaires sont des sources déclaratives, jamais l'arbitre.
5. Ne pas recommander de passer en enchères automatisées (tCPA, tROAS, Maximize conversions, Advantage+, value-based bidding) tant que le score de fiabilité est inférieur à 70/100 ou qu'une anomalie P0 est ouverte.
6. Distinguer explicitement un écart d'attribution normal d'un vrai problème de comptage (voir seuils plus bas).
7. Aucune modification de compte n'est faite depuis ce skill. On produit un diagnostic et des correctifs à appliquer.

## Étape 0 : collecte

Demander uniquement ce qui manque, en une seule fois, puis avancer avec ce qui est disponible.

- Modèle d'affaires : ecommerce, lead gen B2B, SaaS, marketplace, retail local. Cycle de vente moyen. Panier ou valeur deal moyenne.
- Événement qui correspond à de l'argent réel (pas au proxy).
- Période d'analyse (min 30 jours, idéalement 90).
- Sources disponibles : accès GA4, conteneur GTM, comptes Ads, export CRM/Shopify, CSV/XLSX, captures.
- Ce qui a déjà changé récemment : refonte, migration de tag, changement de CMP, consent mode, nouveau CRM.

Si aucun export de ventes réelles n'est disponible, le score maximum atteignable est 60/100. Le dire d'entrée.

## Étape 1 : identifier les vraies conversions commerciales

Classer chaque événement suivi en trois niveaux, sans exception :

- **Niveau 1, valeur commerciale** : achat payé, deal gagné, abonnement actif, devis signé, appel qualifié > seuil de durée.
- **Niveau 2, signal intermédiaire** : formulaire soumis, lead brut, essai gratuit, prise de RDV, ajout au panier.
- **Niveau 3, engagement** : clic, scroll, vue vidéo, temps sur page, `page_view` d'une page clé.

Anomalie majeure si un événement de niveau 2 ou 3 est configuré comme conversion primaire dans une plateforme qui optimise dessus. Le noter, chiffrer le volume concerné, et estimer l'effet sur l'apprentissage de l'algorithme.

## Étape 2 : carte des événements et conversions

Produire un tableau unique, une ligne par événement :

| Événement | Déclencheur GTM/natif | Niveau | Destinations (GA4 / Ads / Meta / LinkedIn) | Marqué conversion où | Dédupliqué (event_id) | Valeur transmise | Statut |

Colonnes à remplir avec `NON FOURNI` si l'information manque. Le statut est `OK`, `SUSPECT` ou `CASSÉ`.

Vérifier en parallèle :
- Doublons de déploiement : même événement envoyé par GTM et par le pixel natif du thème, ou par le SDK et l'API serveur sans `event_id` commun.
- Google Ads : conversions importées depuis GA4 **et** définies en natif sur le même événement, toutes deux en "Primaire".
- Meta CAPI + pixel navigateur sans `event_id`/`external_id` partagé, ou fenêtre de déduplication mal réglée.
- Événement placé sur une page de remerciement atteignable au rafraîchissement ou en accès direct.
- Conversion déclenchée au clic sur bouton plutôt qu'à la confirmation serveur.

## Étape 3 : réconciliation

Construire le tableau de réconciliation sur la même période et le même fuseau horaire :

| Source | Volume conversions | Valeur | Écart vs référence commerciale | Lecture |

Ordre de comparaison : ventes réelles (référence) → CRM → GA4 → chaque plateforme publicitaire.

Points de contrôle systématiques :
- Fuseau horaire du compte GA4 vs celui des plateformes vs celui du back-office.
- Fenêtre d'attribution : clic 7j / vue 1j chez Meta, 30j chez Google, 30j chez LinkedIn. Ne jamais comparer deux fenêtres différentes sans le dire.
- Modèle : data-driven vs last-click vs vue incluse.
- Remboursements, commandes annulées, doublons de commande, commandes de test.
- Leads spam et bots dans le CRM.
- Consent mode / CMP : taux de refus, modélisation GA4 activée, impact sur le volume observé.
- Trafic exclu, filtres internes, IP internes non exclues.

## Étape 4 : détection d'anomalies

Chercher au minimum :

- Doublons de comptage (même conversion, deux tags).
- Événement secondaire utilisé comme conversion primaire.
- UTM : paramètres manquants sur les campagnes payantes, auto-tagging `gclid`/`wbraid` désactivé, UTM écrasés par une redirection, `utm_medium` incohérent (`cpc` vs `paid` vs vide), casse hétérogène, self-referral.
- Attribution incohérente entre plateformes ou entre GA4 et Ads.
- Conversions hors ligne mal importées : mapping `gclid`/`click_id` absent, délai d'import supérieur à la fenêtre, valeurs à zéro, doublons d'upload.
- Valeur monétaire absente, statique, en mauvaise devise, TTC vs HT incohérent.
- Événements qui se déclenchent sans action utilisateur (volume anormalement proche du nombre de sessions).
- Chute ou pic brutal corrélé à une date de déploiement.

## Étape 5 : écart normal ou problème réel

Grille de lecture à appliquer avant de qualifier une anomalie :

- Écart plateforme vs GA4 de **0 à 20 %** : normal. Attribution, fenêtres, vue vs clic, cross-device.
- **20 à 40 %** : zone grise. Ne conclure qu'après avoir vérifié fenêtres, fuseau, modèle et consentement.
- **Au-delà de 40 %**, ou écart entre GA4 et ventes réelles supérieur à **10 %** : problème de comptage, pas d'attribution. Traiter comme anomalie.
- Toute plateforme qui déclare **plus** de conversions que le nombre total de ventes réelles est en surcomptage, quelle que soit la fenêtre.

## Étape 6 : score de fiabilité

Noter sur 100, avec la ventilation visible :

| Dimension | Poids | Note | Justification |
| Conversions de niveau 1 correctement définies | 30 | | |
| Absence de doublons et déduplication en place | 20 | | |
| Réconciliation avec les ventes réelles | 20 | | |
| Intégrité UTM et tagging des campagnes | 15 | | |
| Valeur monétaire et devise | 10 | | |
| Couverture consentement et données modélisées | 5 | | |

Lecture : `80-100` signal exploitable pour du bidding automatisé. `60-79` exploitable en manuel, automatisation risquée. `40-59` décisions média non fiables, corriger avant d'arbitrer. `<40` toute lecture de performance est invalide.

## Étape 7 : priorisation par impact algorithmique

Classer les correctifs selon ce qui dégrade le plus l'apprentissage des algorithmes, pas selon la difficulté technique.

- **P0** : le signal envoyé aux plateformes est faux ou dupliqué, l'algorithme optimise vers du bruit. Correction sous 7 jours.
- **P1** : le signal est vrai mais incomplet ou mal valorisé, l'optimisation est sous-performante. Correction sous 30 jours.
- **P2** : dette de mesure, confort d'analyse, pas d'effet direct sur l'enchère.

Pour chaque correctif : action précise, endroit exact (conteneur GTM, écran Ads, Events Manager, thème Shopify), propriétaire, effort, effet attendu sur le signal.

## Livrable

Rendre dans cet ordre, sans section vide :

1. **Résumé exécutif** : 5 lignes maximum, verdict sur l'exploitabilité des données et décision média autorisée ou non.
2. **Score de fiabilité** : note globale et tableau de ventilation.
3. **Tableau des anomalies** : `ID | Anomalie | Preuve | Type (comptage / attribution / tagging / import) | Sévérité | Impact chiffré ou estimé`.
4. **Preuves utilisées** : liste des sources réellement consultées, avec dates et périmètre. Ce qui n'a pas été consulté est listé séparément.
5. **Impact commercial et algorithmique** : ce que l'erreur coûte en budget mal alloué et en qualité d'apprentissage.
6. **Correctifs P0 / P1 / P2**.
7. **Plan de validation post-correction** : quoi retester, avec quel outil (Tag Assistant, DebugView GA4, Events Manager test events, export CRM), sous quel délai, et quel seuil de réconciliation valide la correction (cible : écart GA4 vs ventes réelles < 5 %).
8. **Vérifications requises** : questions ouvertes et accès manquants.

## Anti-patterns à refuser

- Conclure sur la performance d'un canal alors que le score est inférieur à 60.
- Additionner Meta + Google + LinkedIn pour un total de conversions.
- Attribuer un écart à "l'attribution" sans avoir vérifié fenêtres, fuseau et consentement.
- Proposer de "reconstituer" des données manquantes par extrapolation.
- Recommander une refonte complète du tracking quand deux correctifs P0 suffisent.
