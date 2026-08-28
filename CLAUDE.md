# Recipes Project

Collection de recettes de cuisine au format **Cooklang** (`.cook`).

## Structure du projet

```
recipes/              -- recettes au format .cook (organisées en sous-dossiers)
docs/                 -- spécifications du langage Cooklang
  cooklang-spec.md            -- syntaxe (ingredients, cookware, timers, metadata, etc.)
  cooklang-ecosystem.md       -- conventions fichiers (.menu, .shopping-list, aisle.conf, etc.)
docker/
  cook-server/Dockerfile      -- image Docker pour le serveur web CookCLI
docker-compose.yml            -- déploiement du serveur (bind mount sur le repo local)
```

## Déploiement

Le serveur web CookCLI tourne dans un container Docker sur le réseau local.
- `docker compose up -d` pour démarrer
- Le répertoire `recipes/` est monté directement dans le container
- Port : 9080
- L'UI web permet de consulter et modifier les recettes
- Synchronisation git manuelle (`git pull` / `git add` + `commit` + `push`)

## Cooklang Quick Reference

- **Ingrédients** : `@nom{quantité%unité}` -- ex: `@farine{250%g}`, `@oeuf{3}`
- **Multi-mots** : terminer par `{}` -- ex: `@huile d'olive{2%cs}`
- **Ustensiles** : `#nom{}` -- ex: `#saladier{}`, `#four{}`
- **Minuteurs** : `~nom{durée%unité}` -- ex: `~cuisson{30%minutes}`
- **Préparation** : parenthèses après quantité -- ex: `@oignon{1}(émincé)`
- **Métadonnées** : bloc YAML `---` en début de fichier (title, tags, servings, prep time, cook time, source...)
- **Commentaires** : `-- ligne` ou `[- bloc -]`
- **Sections** : `= Nom de section`
- **Notes** : `> texte`
- **Référence de recette** : `@./chemin/Recette{facteur}` -- ex: `@./Sauce barbecue{1}`

Spec complète dans `docs/cooklang-spec.md` et `docs/cooklang-ecosystem.md`.

## Balisage des ingrédients

Ne marquer en `@` que les **composants réels de la recette**. Deux exclusions :

- **L'aliment support**, la pièce sur laquelle la recette s'applique. Dans un rub,
  une marinade ou une sauce, écrire « la viande » en texte brut, jamais
  `@viande{1%kg}` : dès que la recette est référencée depuis une autre, l'aliment
  ressort en doublon dans la liste de courses du parent (`travers de porc 2 kg`
  **et** `viande 2 kg`). Le poids de référence se porte dans `description`, une
  note `>` et `servings`.
- **Les ingrédients conditionnels** : « 1 cs d'eau si trop épais », un jus de
  cuisson « si disponible ». En texte brut, pour ne pas polluer la liste de courses.

Il n'existe aucun moyen de masquer un ingrédient : les modificateurs
`@-caché{}` et `@?optionnel{}` sont des extensions cooklang-rs **désactivées**
dans ce build -- les caractères `-` et `?` ressortent littéralement dans la liste
de courses. Ne pas les utiliser.

**Alternatives et substitutions** : les écrire dans la note de préparation, après
les accolades, jamais dans le nom.

```cooklang
@vin rosé{20%cl}(ou blanc sec)      -- oui
@vin rosé (ou blanc sec){20%cl}     -- non
```

Un nom qui embarque l'alternative devient un ingrédient distinct : il ne fusionne
plus avec le même ingrédient venu d'une autre recette (même piège que les unités
au pluriel). À savoir : la note ne ressort pas dans `cook shopping-list`, qui
n'affiche que le nom et la quantité -- l'alternative reste visible dans la recette
et dans l'UI web.

## Références entre recettes

Une recette se référence avec `@./Nom de la recette{facteur}`, un ingrédient dont
le nom est un chemin relatif sans l'extension `.cook` :

```cooklang
Préparer le @./Rub barbecue{1} et l'appliquer sur les @travers de porc{1%kg}.
```

- `cook shopping-list` **déplie récursivement** les ingrédients de la recette
  référencée et les agrège à ceux de la recette parente.
- Les accolades portent un **facteur d'échelle**, pas une quantité : `{2}` double
  toutes les quantités scalables de la recette référencée. Variantes documentées
  pour les `.menu` : `{4%servings}`, `{%units}`.
- Sur une recette-composant, caler `servings` sur l'unité de référence
  (`servings: 1` = pour 1 kg de viande) pour que le facteur du parent ait un sens.

## Conventions du projet

- **Langue** : recettes en français, metadata `locale: fr`
- **Unités** : système métrique (g, kg, ml, l, cl, cs, cc)
  - `cs` = cuillère à soupe, `cc` = cuillère à café
  - **Unités de comptage** : écrire la forme `gousse(s)`, `feuille(s)`, `branche(s)`,
    `tranche(s)`, `pincée(s)`. L'agrégation de la liste de courses compare les unités
    comme des chaînes brutes : `3 gousses` et `1 gousse` sortent en deux lignes, alors
    que la forme parenthésée unique fusionne en `4 gousse(s)`. Les unités métriques,
    elles, se convertissent d'elles-mêmes (`500 ml` + `500 ml` = `1 l`).
- **Fichiers** : nommer les fichiers `.cook` avec le nom de la recette en casse naturelle (ex: `Poulet rôti.cook`)
- **Images** : placer à côté du `.cook` avec le même nom (ex: `Poulet rôti.jpg`)
