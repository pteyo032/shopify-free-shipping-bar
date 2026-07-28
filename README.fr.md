<p align="right"><a href="README.md">Read in English</a></p>

# Shopify Free Shipping Bar — barre de progression pour le drawer panier

Une barre de progression multi-paliers, native au thème, pour le drawer
panier : le client voit à quel point il est proche de la livraison gratuite
(ou de toute autre récompense configurée), avec jusqu'à 3 paliers sur une
seule barre segmentée — chaque segment se remplit indépendamment et s'arrête
exactement à son propre repère une fois atteint.

Conçu pour le thème **Shopify Horizon**. Aucune application tierce, aucun
abonnement mensuel, et aucun JavaScript — l'animation de remplissage repose
sur le mécanisme de morphing DOM déjà présent dans le thème, pas sur un
script custom.

*(Captures d'écran : dépose les tiennes dans `docs/screenshots/` — ce repo
n'en contient pas encore.)*

## Fonctionnalités

- Jusqu'à 3 paliers configurables, chacun avec son propre montant, son
  libellé de récompense, et une icône (camion, cadeau, étiquette de
  réduction, ou aucune)
- Remplissage segmenté — chaque palier remplit sa propre portion de la barre
  et s'arrête exactement à son repère une fois atteint, au lieu d'une seule
  barre qui dépasse les repères déjà atteints
- Animation de remplissage fluide **sans une seule ligne de JavaScript**,
  grâce au morphing DOM déjà utilisé par Horizon lors des mises à jour du
  panier (voir `docs/gotchas.md` pour comprendre pourquoi ça marche, et dans
  quel cas ça ne marcherait pas)
- Couleur de la barre, épaisseur, taille du repère et taille de l'icône,
  tous configurables depuis l'éditeur de thème — aucun code à toucher
- Bilingue dès le départ (anglais + français) ; ajoute d'autres langues en
  complétant les fichiers de locale
- Respecte `prefers-reduced-motion`
- Accessible : `role="progressbar"` avec `aria-valuenow`/`min`/`max` et un
  `aria-label` traduit

## Contenu du repo

Ce repo contient **seulement le code de cette fonctionnalité** — pas le
thème Horizon complet, qui appartient à Shopify. Tu déposes ces fichiers
dans un thème Horizon (ou dérivé de Horizon) existant.

| Chemin | Ce que c'est |
|---|---|
| `snippets/free-shipping-bar.liquid` | Toute la fonctionnalité — markup, calculs des paliers/segments, et CSS scopé dans un seul fichier |
| `assets/icon-truck.svg`, `assets/icon-gift.svg` | Les deux icônes custom (une icône de réduction est supposée déjà exister dans la plupart des thèmes Horizon sous le nom `icon-discount.svg`) |
| `locales/*.json`, `locales/*.schema.json` | Traductions anglais + français (textes storefront et libellés de l'éditeur) |
| `docs/settings-schema-snippet.json` | Le JSON exact à coller dans le `config/settings_schema.json` de ton thème |
| `docs/integration-guide.md` | Instructions d'installation étape par étape |
| `docs/gotchas.md` | Pièges techniques rencontrés en construisant cette feature, pour ne pas les répéter |

## Démarrage rapide

1. Copie `snippets/free-shipping-bar.liquid` et les deux icônes dans ton
   thème.
2. Colle `docs/settings-schema-snippet.json` dans le `config/settings_schema.json`
   de ton thème, et ajoute les clés de traduction de `locales/` à tes
   propres fichiers de locale.
3. Ajoute `{% render 'free-shipping-bar' %}` dans ton `cart-drawer.liquid`,
   juste après le titre/en-tête du drawer (les deux états, panier vide et
   panier rempli — voir `docs/integration-guide.md` pour les emplacements
   exacts).
4. Configure les paliers, couleurs et tailles depuis l'éditeur de thème.

## Limitation connue : récompense affichée vs. réellement appliquée

Cette barre est **purement informative**. Atteindre un palier n'applique
pas automatiquement de réduction, n'ajoute pas de cadeau gratuit au panier,
et ne change pas le tarif d'expédition — elle indique seulement au client
qu'un seuil est atteint. Pour que le comportement réel du panier corresponde
à ce qui est affiché, il faut :

1. **Une réduction ou un tarif d'expédition Shopify natif correspondant**,
   configuré manuellement dans Admin → Réductions / Réglages → Expédition
   (le plus simple, sans code — mais il faut garder les réglages du thème
   et la config Shopify synchronisés à la main)
2. **Une Shopify Function** qui lit le sous-total du panier et applique la
   récompense automatiquement (propre et fiable, mais un vrai build
   d'extension d'app — pas du code de thème)

Tranche cette question avec le propriétaire de la boutique *avant* de
configurer les montants des paliers — si les chiffres divergent, la barre
peut promettre quelque chose que le checkout ne tient pas.

## Licence

MIT
