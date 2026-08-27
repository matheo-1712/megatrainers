# megatrainers - un dresseur par Méga-Évolution

Un dresseur pour chaque Méga-Évolution fournie par
[Cobblemon: Mega Showdown](https://modrinth.com/mod/mega-showdown), qui remet **la
méga-gemme correspondante** au joueur qui le bat. Battre le spécialiste de Dracaufeu X donne
la Dracaufite X, et ainsi de suite.

**Les équipes sont vides.** Chaque fichier a un `"team": []` à remplir : un dresseur sans
équipe ne peut pas combattre. Tout le reste - nom, répliques, niveau, difficulté, gimmick,
récompense - est déjà écrit.

## Ce qu'il contient

```
megatrainers/
├── pack.mcmeta
├── assets/megatrainers/lang/       ← les textes, en français et en anglais
│   ├── fr_fr.json
│   └── en_us.json
└── data/megatrainers/cobblemontrainers/
    ├── megas/                      ← 47 dresseurs + category.json
    └── megas_bonus/                ← 34 dresseurs + category.json
```

| Catégorie | Dossier | Dresseurs | Ce que c'est |
| --- | --- | --- | --- |
| Méga-Évolutions | `megas/` | 47 | Les méga-gemmes officielles (Dracaufeu et Mewtwo comptent double, X et Y) |
| Méga-Évolutions bonus | `megas_bonus/` | 34 | Les méga-évolutions ajoutées par Mega Showdown (Raichu compte double) |

Le dossier est la catégorie : supprimer `megas_bonus/` retire les 34 dresseurs bonus, sans
rien casser ailleurs.

Chaque dresseur est réglé pareil, à changer selon ta progression :

```json
"battle": { "level": 75, "difficulty": 4, "gimmicks": ["mega"] }
```

et sa récompense est la gemme, **une seule fois** :

```json
"rewards": [{ "item": "mega_showdown:charizardite_x", "count": 1, "firstWinOnly": true }]
```

`firstWinOnly` fait que la revanche reste possible sans distribuer une gemme à chaque victoire.

## Les textes

Rien n'est écrit en dur dans les dresseurs : le `name` et les cinq `messages` sont des **clés
de traduction**, résolues côté client depuis `assets/megatrainers/lang/`.

```json
// data/megatrainers/cobblemontrainers/megas/charizard_x.json
"name": "trainer.megatrainers.megas.charizard_x.name"
```

```json
// assets/megatrainers/lang/fr_fr.json
"trainer.megatrainers.megas.charizard_x.name": "Spécialiste de Dracaufeu X"
```

| Clé | Ce que c'est |
| --- | --- |
| `trainer.megatrainers.<catégorie>.<dresseur>.name` | Le nom affiché au-dessus du dresseur |
| `trainer.megatrainers.<catégorie>.<dresseur>.greeting` / `.start` / `.decline` / `.win` / `.lose` | Ses répliques |
| `category.megatrainers.<catégorie>` | Le titre de la catégorie |

Réécrire un dialogue, c'est donc éditer le lang, pas le dresseur. Ajouter une langue, c'est
copier `en_us.json` en `es_es.json` et traduire. Une clé absente s'affiche telle quelle en
jeu : c'est ce qui rend une faute de frappe visible tout de suite.

Les répliques tournent sur quatre voix différentes, pour que 81 dresseurs ne se saluent pas
tous avec la même phrase.

## Remplir une équipe

Un Pokémon par entrée, au format Showdown. La gemme se donne comme un objet tenu, et
`Fallback Item:` couvre le cas où Mega Showdown n'est pas installé :

```json
"team": [
  "Charizard @ Charizardite X\nFallback Item: Life Orb\nAbility: Blaze\nEVs: 252 Atk / 4 SpD / 252 Spe\nJolly Nature\n- Flare Blitz\n- Dragon Claw\n- Earthquake\n- Roost"
]
```

Il faut **les deux** : la gemme sur le Pokémon et `["mega"]` dans `battle.gimmicks` - ce
second est déjà là. Le détail est dans [GIMMICKS.md](../../docs/GIMMICKS.md), le format
d'équipe dans [DATAPACK.md](../../docs/DATAPACK.md#le-format-déquipe).

## L'installer

Le pack a deux moitiés : les dresseurs sous `data/`, les textes sous `assets/`. **`mods/` est
la seule voie qui charge les deux en un fichier.** Zippe le contenu de ce dossier - les
fichiers à la racine de l'archive, pas le dossier lui-même -, renomme en `.jar`, et pose-le
dans `mods/` à côté de `cobblemon-trainers` :

```bash
cd examples/megatrainers && zip -r ../../megatrainers.jar .
```

Sinon, la même archive va dans `saves/<monde>/datapacks/` **et** dans `resourcepacks/` : le
dossier `datapacks/` d'un monde ne lit que `data/`, et les noms des dresseurs s'afficheraient
en clés brutes sans la seconde moitié.

En jeu : `/reload`, puis `/cobblemontrainers spawn megatrainers:megas/charizard_x`.

## Ce qu'il reste à décider

- **Les équipes**, donc.
- **Le niveau et la difficulté** : 75 et 4 partout, ce qui ne distingue pas un Méga-Nostenfer
  d'un Méga-Mewtwo. Monter les légendaires en 100 / 5 est le réglage le plus évident.
- **L'ordre**, si tu veux une ligue plutôt que 81 dresseurs indépendants : `requires.defeated`
  enchaîne un dresseur au précédent, `requires.items` peut exiger la Key Stone, et
  `progress.rematch` ferme la revanche. Voir
  [DATAPACK.md](../../docs/DATAPACK.md#conditions-pour-combattre).
- **Les skins** : tous les dresseurs sont en Steve. `"skin": { "type": "player_username",
  "value": "..." }` les habille.
- **Le lieu** : sans bloc `location`, un dresseur ne peut pas être appelé depuis le Battle
  Phone. Voir [SPAWNING.md](../../docs/SPAWNING.md).

## La Key Stone du joueur

Un dresseur méga-évolue même face à un joueur qui n'a pas de Key Stone - c'est une règle de
Cobblemon, pas du mod. Un joueur sans Key Stone se bat donc à armes inégales : mettre la Key
Stone avant cette série, ou l'exiger via `requires.items`, est ce qui rend la ligne jouable.
