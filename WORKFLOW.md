# C@fés Numériques — Workflow d'ajout et d'archivage

## Architecture des fichiers

```
micetf.fr/
└── cafes-numeriques/
    ├── index.html              ← portail (liste de tous les cafés)
    ├── 2026-S17/
    │   └── index.html          ← présentation S17 2026
    ├── 2026-S18/
    │   └── index.html          ← présentation S18 2026 (à venir)
    └── 2025-S.../
        └── index.html          ← archives 2025 (rétro-compatible)
```

**Convention de nommage des dossiers** : `YYYY_SNN`
- `YYYY` = année sur 4 chiffres
- `S` = préfixe fixe
- `NN` = numéro de semaine ISO sur 2 chiffres (01 à 53)

---

## Préparer une nouvelle présentation

### 1. Dupliquer le template

```bash
# Depuis la racine du dépôt
cp -r cafes-numeriques/2026-S17 cafes-numeriques/2026-SNN
```

### 2. Adapter les métadonnées dans `index.html`

Rechercher et remplacer les occurrences suivantes :

| Champ                | Valeur à remplacer          | Valeur cible                  |
|----------------------|-----------------------------|-------------------------------|
| `<title>`            | `S17 de 2026`               | `SNN de YYYY`                 |
| `eyebrow` (slide 1)  | `semaine 17 de 2026`        | `semaine NN de YYYY`          |
| `mailto` subject     | `S17%20de%202026`           | `SNN%20de%20YYYY`             |
| `header-title`       | `S17·2026`                  | `SNN·YYYY`                    |
| Contenu des slides   | *(spécifique à la session)* | *(nouveau contenu)*           |

> **Encodage URL du sujet mailto**
> Le `@` de `C@fé` s'encode `%40`, l'espace en `%20`.
> Exemple : `A%20propos%20du%20C%40f%C3%A9%20Num%C3%A9rique%20S18%20de%202026`
>
> Générateur rapide en JS :
> ```js
> encodeURIComponent('A propos du C@fé Numérique S18 de 2026')
> // → "A%20propos%20du%20C%40f%C3%A9%20Num%C3%A9rique%20S18%20de%202026"
> ```

### 3. Mettre à jour le portail (`cafes-numeriques/index.html`)

**A. Ajouter la carte du nouveau café** dans la section de l'année concernée,
en copiant le bloc `.cafe-card` existant et en adaptant :
- `href` → `./2026-SNN/`
- `aria-label` → titre de la session
- `<span class="cafe-card-badge">` → `SNN · YYYY`
- `<h3>` → titre de la présentation
- `<p>` → description courte (2 phrases max)
- `.cafe-card-tags` → outils présentés (emoji + nom)

```html
<!-- Exemple de carte à copier-coller -->
<a href="./2026-S18/" class="cafe-card" aria-label="C@fé Numérique S18 2026 — …">
  <div class="cafe-card-header">
    <span class="cafe-card-badge">S18 · 2026</span>
    <span class="cafe-card-date">Semaine 18</span>
  </div>
  <div class="cafe-card-body">
    <h3>Titre de la présentation</h3>
    <p>Description courte des outils présentés.</p>
  </div>
  <div class="cafe-card-tags">
    <span class="tag">🔧 Outil 1</span>
    <span class="tag">🔧 Outil 2</span>
  </div>
  <div class="cafe-card-footer">
    <span class="cafe-card-link">Voir la présentation →</span>
    <span class="cafe-card-duration">~30 min</span>
  </div>
</a>
```

**B. Supprimer ou mettre à jour la carte placeholder** `coming-soon` si elle existe.

**C. Créer une nouvelle carte placeholder** pour le café suivant (optionnel).

### 4. Nouvelle année

Quand une nouvelle année commence, ajouter une section dans `index.html` :

```html
<section class="year-group" aria-labelledby="year-YYYY">
  <div class="year-label">
    <h2 id="year-YYYY">YYYY</h2>
  </div>
  <div class="cards-grid">
    <!-- cartes de l'année YYYY -->
  </div>
</section>
```

Insérer la nouvelle section **au-dessus** des années précédentes
(ordre antéchronologique : plus récent en haut).

---

## Déploiement

```bash
# Vérifier les fichiers modifiés
git status

# Ajouter la nouvelle session + portail mis à jour
git add cafes-numeriques/2026-SNN/index.html
git add cafes-numeriques/index.html

# Commit
git commit -m "feat(cafes): ajoute C@fé Numérique 2026-SNN — <titre court>"

# Pousser
git push origin main
```

### Checklist avant push

- [ ] Le dossier est nommé `YYYY_SNN` (ex: `2026-S18`)
- [ ] Le fichier de présentation se nomme `index.html`
- [ ] Le lien `mailto:` contient le bon sujet encodé
- [ ] La carte est ajoutée dans `cafes-numeriques/index.html`
- [ ] L'ancienne carte `coming-soon` a été retirée ou mise à jour
- [ ] Testé en local avec un serveur HTTP (ex: `npx serve .`)

> ⚠️ Ne pas ouvrir les fichiers en `file://` pour tester les iframes :
> utiliser un serveur local (`npx serve .` ou Live Server dans VSCode).

---

## Référence : encodage du sujet mailto par session

| Session  | Sujet encodé                                                              |
|----------|---------------------------------------------------------------------------|
| S17 2026 | `A%20propos%20du%20C%40f%C3%A9%20Num%C3%A9rique%20S17%20de%202026`       |
| S18 2026 | `A%20propos%20du%20C%40f%C3%A9%20Num%C3%A9rique%20S18%20de%202026`       |
| S01 2027 | `A%20propos%20du%20C%40f%C3%A9%20Num%C3%A9rique%20S01%20de%202027`       |

Générer rapidement depuis la console navigateur :
```js
(s,y) => `A%20propos%20du%20C%40f%C3%A9%20Num%C3%A9rique%20${encodeURIComponent('S'+s+' de '+y)}`
// usage : ('18', '2026') → "A%20propos%20du%20C%40f%C3%A9%20Num%C3%A9rique%20S18%2520de%25202026"
```
