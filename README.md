# examrnffyfyfyyfyy
# 🧠 Git & GitHub — Cheat Sheet
> Révision rapide · TEK-UP ING-3 · Prof. Nouran Zouabi

---

## ⚙️ Configuration

```bash
git config --global user.name "Ton Nom"
git config --global user.email "email@example.com"
git config --global --list          # vérifier
```

---

## 🔑 SSH (Authentification)

```bash
ssh-keygen -t ed25519               # générer les clés
cat ~/.ssh/id_ed25519.pub           # afficher la clé publique → copier sur GitHub
ssh -T git@github.com               # tester la connexion
```
> GitHub → Settings → SSH and GPG keys → New SSH key → coller la clé publique

---

## 📁 Initialisation

```bash
git init                            # créer un nouveau dépôt local
git clone <url> [nom-dossier]       # cloner un dépôt distant
git remote add origin <url>         # lier un dépôt local à GitHub
git remote -v                       # vérifier les remotes
```

---

## 📊 Les 3 Zones de Git

```
Working Directory  →(git add)→  Staging Area  →(git commit)→  Repository (.git)
```

```bash
git status                          # voir l'état des fichiers
git add fichier.txt                 # ajouter un fichier au staging
git add .                           # ajouter TOUT au staging
git commit -m "message"             # commiter
git commit --amend -m "nouveau msg" # modifier le dernier message de commit
```

---

## 📜 Historique

```bash
git log                             # historique complet
git log --oneline                   # une ligne par commit
git log --oneline --graph --decorate # graphe des branches
git reflog                          # TOUT l'historique (même resets)
```

---

## ⏪ Rollback — Annuler des modifications

| Situation | Commande | Réversible ? |
|-----------|----------|:---:|
| Modif non stagée | `git restore fichier` ou `git checkout -- fichier` | ❌ |
| Fichier stagé (avant commit) | `git restore --staged fichier` | ✅ |
| Annuler commit (garder modifs en staging) | `git reset --soft HEAD^` | ✅ |
| Annuler commit (garder modifs locales) | `git reset HEAD^` | ✅ |
| Annuler commit + effacer modifs | `git reset --hard HEAD^` | ❌ |
| Annuler commit déjà pushé (safe) | `git revert HEAD` | ✅ |

---

## 🌿 Branches

```bash
git branch                          # lister les branches
git branch feature-x                # créer une branche
git checkout feature-x              # basculer sur une branche
git checkout -b feature-x           # créer + basculer (raccourci)
git branch -m ancien nouveau        # renommer une branche
git branch -d feature-x             # supprimer une branche (après merge)
```

---

## 🔀 Merge

```bash
# Sur la branche de destination (ex: main) :
git merge feature-x                 # merge standard (commit de merge)
git merge feature-x --squash        # squash : condense tous les commits en 1
git commit -m "feat: squash merge"  # ← obligatoire après --squash
```

### Résoudre un conflit de merge (5 étapes)

```bash
# 1. Voir les fichiers en conflit
git status

# 2. Ouvrir le fichier → chercher les marqueurs :
<<<<<<< HEAD
contenu de main
=======
contenu de feature-x
>>>>>>> feature-x

# 3. Éditer le fichier → choisir le contenu final → supprimer TOUS les marqueurs

# 4. Marquer comme résolu
git add fichier.txt

# 5. Finaliser
git commit -m "fix: resolve merge conflict"
```

---

## 🔁 Rebase

### Rebase simple — mettre à jour une branche
```bash
# Sur feature-x, se mettre à jour avec main :
git rebase main

# En cas de conflit pendant le rebase :
git add fichier.txt                 # après résolution
git rebase --continue               # ← PAS git commit !
git rebase --abort                  # annuler tout le rebase
git rebase --skip                   # ignorer le commit problématique
```

### Rebase interactif — réécrire l'historique
```bash
git rebase -i HEAD~3                # retravaille les 3 derniers commits
git rebase -i main                  # retravaille depuis le point de séparation avec main
```

**Commandes dans l'éditeur :**

| Commande | Raccourci | Effet |
|----------|:---------:|-------|
| `pick`   | `p` | Garder le commit tel quel |
| `reword` | `r` | Garder mais modifier le message |
| `squash` | `s` | Fusionner avec le précédent + combiner messages |
| `fixup`  | `f` | Fusionner avec le précédent, ignorer CE message |
| `drop`   | `d` | Supprimer le commit |

**Exemple — fusionner 2 commits en 1 :**
```
pick a1b2c3d feat: add min calculation
squash d4e5f6g fix: fix indentation
```

> ⚠️ Les commits sont listés du **plus ancien** (haut) au **plus récent** (bas) — inverse de `git log`

### Rebase vs Merge

| | `git merge` | `git rebase` |
|---|---|---|
| Historique | Bifurqué (branches visibles) | **Linéaire et propre** |
| Commit de fusion | Oui (commit M créé) | Non |
| Anciens commits | Conservés | Recréés (nouveaux SHA) |
| Sécurité partagé | ✅ Toujours sûr | ⚠️ Dangereux si déjà pushé |

> 🚨 **Règle d'or :** Ne jamais rebase une branche déjà pushée que d'autres utilisent.

---

## 🗄️ Stash — Mettre de côté

```bash
git stash                           # mettre de côté les modifs non commitées
git stash list                      # voir les stashs
git stash pop                       # récupérer + supprimer le stash
git stash apply                     # récupérer SANS supprimer
```

---

## 🍒 Cherry-pick

```bash
git cherry-pick a1b2c3d             # copier un commit spécifique sur la branche courante
```

---

## 🌐 GitHub — Push / Pull

```bash
git push -u origin main             # premier push (lie la branche locale à origin)
git push                            # push suivants
git push origin --all               # pusher TOUTES les branches
git push --force-with-lease         # push forcé après rebase (plus sûr que --force)

git pull                            # récupérer + merger depuis GitHub
git fetch                           # récupérer sans merger
```

---

## 🙈 .gitignore

```gitignore
*.log           # ignorer tous les fichiers .log
node_modules/   # ignorer le dossier node_modules
.env            # ignorer les fichiers d'environnement
target/         # ignorer le dossier target (Java/Maven)
dist/           # ignorer les builds
```

---

## 💥 Erreurs fréquentes

| Erreur | Cause | Solution |
|--------|-------|----------|
| `fatal: not a git repository` | `git init` non fait | `git init` |
| `fatal: pathspec 'fichier' did not match` | Fichier inexistant | Créer le fichier AVANT `git add` |
| `git push username` | Mauvaise syntaxe | `git push origin main` |
| `Permission denied (publickey)` | Clé SSH non ajoutée à GitHub | Ajouter `id_ed25519.pub` sur GitHub |
| `rejected — non-fast-forward` | Historique diverge après rebase | `git push --force-with-lease` |
| Marqueurs `<<<` restent dans le fichier | Conflit non résolu correctement | Supprimer TOUS les marqueurs avant `git add` |

---

## 🔖 Flux de travail complet (TP type)

```bash
# 1. Créer et basculer sur une branche
git checkout -b feature-x

# 2. Travailler + commiter
git add . && git commit -m "feat: ..."

# 3. Nettoyer l'historique avant PR
git rebase -i HEAD~N

# 4. Se mettre à jour avec main
git rebase main

# 5. Merger dans main
git checkout main
git merge feature-x          # ou : git rebase feature-x

# 6. Publier
git push origin --all
```

---

*Made with ❤️ · Malak Abdelkhalek · TEK-UP 2025/2026*
