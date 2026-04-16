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

Spec complète dans `docs/cooklang-spec.md` et `docs/cooklang-ecosystem.md`.

## Conventions du projet

- **Langue** : recettes en français, metadata `locale: fr`
- **Unités** : système métrique (g, kg, ml, l, cl, cs, cc)
  - `cs` = cuillère à soupe, `cc` = cuillère à café
- **Fichiers** : nommer les fichiers `.cook` avec le nom de la recette en casse naturelle (ex: `Poulet rôti.cook`)
- **Images** : placer à côté du `.cook` avec le même nom (ex: `Poulet rôti.jpg`)
