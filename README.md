# Claude Marketing Skills

Deux Agent Skills pour Claude Code, orientés mesure et média payant. Ils sont pensés pour être enchaînés : on valide le signal de conversion avant de prendre la moindre décision budgétaire.

| Skill | Rôle |
|---|---|
| [`marketing-measurement-auditor`](skills/marketing-measurement-auditor/SKILL.md) | Auditer la fiabilité de la mesure (GA4, GTM, Google Ads, Meta, LinkedIn, Shopify/CRM, exports, captures) et produire un score de fiabilité avec correctifs P0/P1/P2. |
| [`paid-media-growth-operator`](skills/paid-media-growth-operator/SKILL.md) | Auditer, planifier et optimiser les campagnes Google Ads, Meta Ads, LinkedIn Ads, TikTok Ads et Microsoft Ads, avec matrice de décision et plan 30/60/90. |

## Principe

`paid-media-growth-operator` s'arrête à l'étape 2 si le signal de conversion n'est pas fiable et renvoie vers `marketing-measurement-auditor`. Aucune recommandation d'enchères automatisées n'est produite sur un signal jugé non fiable.

Règles communes aux deux skills :

- Aucune donnée inventée. Ce qui manque est marqué `NON FOURNI` et listé en vérifications requises.
- Séparation explicite entre faits, hypothèses et éléments à vérifier.
- La réalité commerciale (ventes facturées, deals gagnés) prime sur GA4, qui prime sur les chiffres auto-déclarés des plateformes.
- Jamais d'addition des conversions de plusieurs plateformes.
- Aucune modification de compte publicitaire sans autorisation explicite.

## Installation

### Skills personnels, disponibles dans tous les projets

```bash
git clone git@github.com:USER/claude-marketing-skills.git /tmp/claude-marketing-skills && cp -R /tmp/claude-marketing-skills/skills/* ~/.claude/skills/
```

### Skills de projet, versionnés avec un dépôt

```bash
mkdir -p .claude/skills && cp -R /tmp/claude-marketing-skills/skills/* .claude/skills/
```

Redémarrer Claude Code après la copie pour que les skills soient détectés.

## Utilisation

Invocation explicite :

```
/marketing-measurement-auditor
```

```
/paid-media-growth-operator
```

Invocation automatique : Claude déclenche le skill quand la demande correspond à sa description (tracking cassé, conversions qui ne réconcilient pas, audit de compte publicitaire, réallocation de budget, plan média).

Exemple avec contexte :

```
/paid-media-growth-operator audit du compte Google Ads, budget 8k/mois, lead gen B2B, cycle de vente 70 jours
```

## Structure

```
skills/
├── marketing-measurement-auditor/
│   └── SKILL.md
└── paid-media-growth-operator/
    └── SKILL.md
```

Un seul fichier par skill : tout le workflow tient dans les instructions, sans script ni dépendance externe.

## Validation

Les deux skills passent le validateur officiel du skill `skill-creator` :

```bash
python3 ~/.claude/skills/skill-creator/scripts/quick_validate.py skills/marketing-measurement-auditor
```

## Licence

MIT.
