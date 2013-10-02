# Utilisation de github et workflow pouvant être associé

Ce document décrit l'utilisation de github dans le cadre d'un workflow utilisant des pull request mais plus globalement fournit des solutions à divers problèmes

## Présentation général du workflow
L'ensemble des développeurs doit avoir les droits de pull et push sur le dépot.
Les développeurs doivent avoir une certaine rigeur notamment dans les messages de commits.
La principale différence entre git et svn est le fait que dans git tout est décentralisé et le système de merging est très performant surtout avec plusieurs petits commits (il faut que les commit soient autosuffisants ceci dit)
Une autre différence avec svn est qu'il n'y a pas besoin d'être synchronisé en permanence avec le serveur. Donc plusieurs commit et seulement un push par jour ou plus uniquement si nécessaire (par exemple pour se synchroniser avec ses voisins)

Le workflow repose sur l'utilisation de branches, de pull request, et de labels associées aux pull request
L'organisation du dépôt en terme de branche devient donc la suivante : 
```
feature/nom_dune_feature  # attention pas d'espace possible dans le nom des branches
bug/nom_dun_correctif_de_bug
refactor/nom_du_refactoring_de_code
```

Pour chaque feature/bug/refactor on a donc une branche. Chaque feature/bug/refactor doit être suffisament autonome (sauf cas particuliers) pour être développer sans dépendance autre que le code existant ou d'une autre feature/bug/refactor
Une feature/bug/refactor doit représenter un seul changement dans le système bien décrit. Cela permet d'autant mieux de suivre les évolutions du système.

## Scénario d'utilisation du workflow
	
### Je clone le dépot
```git clone adresse_de_mon_repo```

Sur github
```git clone git@github.com:nom_dutilisateur/nom_du_depot.git```

### Je commence une nouvelle feature/bug/refactor
Ici on va travailler avec une feature, mais la procédure est la même pour un bug ou du refactoring en changeant juste le préfixe des branches
```
git pull    # Permet de récupérer les changements qui eu lieu sur le dépot sur les différentes branches, notament ceux de mes voisins 
git checkout master # Je me place sur la branche principal, la branche master pour commencer ma nouvelle feature à partir du code le plus à jour
git checkout -b feature/feature1  # Je crée ma nouvelle branch 
git push origin feature/feature1 # je demande que ma branche soit suivie au niveau du serveur distant
```

A partir de la j'effectue des modifications de fichiers, une fois que j'estime que ça correspond à un premier commit, je commit

```
git status # Permet de lister les fichier modifiers, ceux qui seront intégrés au commit, ceux pas encore ajouter...
git add de_mes_fichiers_ou_de_mes_dossier_que_je_veux_commiter # J'ajoute à mon commit tous les fichiers que je souhaite commiter
git commit # Je fait mon commit, cela me demande un message de commit
```
Attention pour le message de commit mettre quelque chose de clair, cela permettra aux autres développeur de comprendre ce que vous avez fait.
Par exemple "updates"  n'est pas un bon message de commit
En revanche, "Created application template" est potentiellement un meilleur message de commit 

Une fois, cela fait, je pousse mes changements et j'ouvre un pull request. Ouvrir un pull request immédiatement permet à mes collègues de suivre ce que je fais et éventuellement d'avoir une meilleur vision de ce qu'il y a faire.

```
git pull  # je récupère les éventuelles modifications
git push  # je pousse mes changements sur le serveur
```

Pour ouvrir un pull request, il faut se rendre sur github et appuyer sur le bouton pull request.
![github_open_pullrequest][github_open_pullrequest]
 
Ensuite il faut choisir sa branche dans le from et le master dans le to 
![github_pullrequest_choose_branche][github_pullrequest_choose_branche]

Enfin appliquer le label inProgress au pull request
![github_apply_inprogress][github_apply_inprogress]

### Je continue de travailler sur ma feature/bug/refactor ou je veux travailler sur une autre
```
git checkout feature/feature1   # permet de retourner sur ma branche de travaille
``` 

Je fais mes différents commits
```
git add de_mes_fichiers_ou_de_mes_dossier_que_je_veux_commiter
git commit 
```
Attention encore une fois je pense aux messages de commit clair (cf plus haut)

Je fait mon push en moyenne une fois par jour en général en fin de journée, mais je peux en faire plus si nécessaire
```
git pull  # je récupère les éventuelles modifications
git push  # je pousse mes changements sur le serveur
```

### Quand j'ai finit de travailler ou retraviller sur ma feature/bug/refactor et qu'elle est complète
Je dois pousser mes derniers changements et changer le label sur github

```
git checkout feature/feature1
git push -> pour être sur d'avoir tout pousser
```

Je refait un passage sur mon code sur github pour être sur que je n'ai pas fait d'erreur de code via l'onglet compare
![github_compare_code][github_compare_code]

Je change le label associé une fois ma revue de code fait et je met le label toReview et je préviens la personne qui doit faire du code review ou je lui assigne la tâche
![github_label_toReview][github_label_toReview]

### J'ai besoin de développer une feature à partir d'une feature de mon voisin qui n'est pas encore mergée (récupérée) sur la branche principale, le master
```
git pull
git checkout feature/nom_de_ma_feature_de_mon_voisin  # je me place sur la branche de mon voisin
```
A partir d'ici j'applique la procédeure qui permet de commencer une nouveau feature en ommetant l'instruction
```
git checkout master
```

#### Attention
Le point de départ d'une feature/bug/refactor ou autre est la branche sur laquelle je me trouve. Donc si ma feature n'a pas de dépendance à d'autres branches toujours penser à revenir sur le master
```
git pull
git checkout master
```
Pour connaître la branche sur laquelle je me trouve 
```
git branch
```

### Pour récupérer les avancées de la feature de mon voisin dans ma feature (ou pour faire du merge de différents branches)
```
git pull # je récupère les derniers modifications sur le serveurs
git checkout feature/ma_feature # Je vais sur la branche de ma feature/bug/refactor
git merge feature/feature_de_mon_voisin # je demande le merging avec ma branche
```

Git est plutôt efficace pour faire du merge en général. Cependant tout système automatisé à ses limites, et parfois il faut finir les fichiers ne pouvant pas être mergé manuellement. Dans ce cas voir la section, à l'aide git me dit que j'ai des conflit lors du merge.

### A l'aide git me dit que j'ai des conflit lors du merge
Si git ne réussi pas à faire un merge il met un message d'alerte de type merge conflict
```
---->> exemple de message de merge conflict
```

Dans ce cas il faut ouvrir les fichiers a conflit manuellement et merger les partis qui n'ont pas pu être mergées automatiquement.
Une section qui n'a pas pû être mergée automatiquement apparaîtra ainsi : 
```
Example de zone de conflit
```

Ensuite il faut commiter pour valide le merge 
```
git status # Etat des fichiers suivi par git, notamment il mettra ceux qui comportait des conflit
git add mes_fichiers_dosssiers # ajouter les fichiers a prendre en compte dans le commit notamment tout les fichiers qui avait des conflits
git commit # Normalement il vous proposera un format de message de commit un peu de base du type merge machin. Conflits... les noms des fichiers à conflit
git push # optitonnel, seulement si on veut pousser les changements
```

### A l'aide github me dit qu'il ne peut pas merger automatiquement le pull request car il y a des conflits
Je change le label associé au pull request vers needFix.
Je demande à la personne qui a développée la feature de faire le merge de sa feature avec la branche principal (cf Se synchroniser avec le master) et de corriger les problème de conflits (cf a l'aide git me qu'il il y a un conflit lors du merge)
Il faut que la personne qui fait le correctif pense à remettre le label needReview

### Se synchroniser avec le master
```
git pull
git checkout feature/ma_feature
git merge master # je demande le merge
```
Si nécessaire, corriger les problème de conflits (cf a l'aide git me qu'il il y a un conflit lors du merge)

Je commit et pousse si nécessaire
```
git commit # toujours penser aux bons messages de commit
git push
```

### Faire du code review et mergé
Aller sur github dans la partie pull request. Selectionner les "issues" avec le label needReview.
Dans le pull request il faut aller dans la section "Files Changed" pour voir les différences.
Vérifier le status du commit si un système de tests automatisé à été mis en place et que celui-ci supporte la github commit status api. Si c'est le cas un rond de couleur vert apparait a coté du commit pour dire qu'il passe les tests.
Une fois que tout est ok changé le label vers reviewed. 
Pour l'intégrer à la branche principal mettre le status merged et appuyer sur le bouton merge en vérifiant que la branche de destination est bien le master. 
Si il y a un problème le signaler via les commentaires et changer le label a needFixes.

### Aye Aye, j'ai commencé à editer mes fichiers dans la branche master au lieu de le faire dans la branche de ma feature, que faire
Si rien n'a encore été commit appliqué la procédure git stash, git apply
```
git stash
git checkout feature/ma_feature
git apply
```

## Attention

### Attention lors du changement de branche (pour passer d'une feature à une autre)
Attention lors du changement de branche, pour tous les fichiers suivis par git (c'est à dire qui ont eu au moins une fois un git add), seul ceux commités seront sauvgardés. Les autres changements seront perdus si non commit.
Il existe une commande qui permet temporairement de garder l'état courant d'une branche et de le restaurer par la suite mais ne peut être utilisé qu'une seul fois à la fois.
```
git stash # sauvegarde temporairement l'état du dépot
........ # je fait mes travaux hier
........ # je retourne dans ma branche
git apply # restauration des fichiers
```

## Mise en place et maintiens du dépot sur github 
### Mettre en place un fichier .gitignore
Ajouter un fichier .gitignore pour éviter que les binaires ou autre fichiers inutiles ne soient commités accidentellement
Un exemple de gitignore est fournit dans le depot

### Ajouter les labels
Il faut ajouter les labels utilisés plus haut, à savoir : 
* needReview
* needFix
* inProgress
* Reviewed
* Merged
* Staged


