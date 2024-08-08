# Howto Git

- Documentation: <https://git-scm.com/doc>
- À lire : <https://progit.org/>
- À voir : <https://youtu.be/4XpnKHJAok8>

[Git] est un logiciel de gestion de code source, créé en 2005 par Linus
Torvalds.

## Installation

    # apt install git gitg

Configuration minimale via `~/.gitconfig` :

    [user]
        name = John Doe
        email = jdoe+git@example.com
    [pull]
        rebase = true
    [push]
        default = simple
    [core]
        editor = vim

## Commandes de base

### Utilisation et pages de manuel

Les commandes Git s’utilisent sous la forme `git <commande> [options]`\
Pour chaque commande, une page de manuel *git-<commande>* est
disponible. Par exemple pour `git add` faire `man git-add`.

### Créer un dépôt

    $ mkdir foo
    $ cd foo
    $ git init

> Note : dans le cas où on crée un dépôt en mode « serveur », on
> utilisera l’argument `--bare`.

### Cloner un dépôt

Plusieurs méthodes d’accès pour récupérer un dépôt : ssh://, http(s)://,
git://, file:// etc.

    $ git clone ssh://git.example.com/path/projet.git
    $ git clone https://git.example.com/path/projet
    $ git clone git://git.example.com/path/projet.git
    $ git clone file:///path/projet

### Travailler sur un dépôt local

Ajouter un nouveau fichier :

    $ touch main.c
    $ git add main.c
    $ git commit -v

Modification d’un fichier existant :

    $ vi main.c
    $ git add main.c
    $ git commit -v

*Note* : si l’on souhaite faire un commit de l’ensemble des fichiers
modifiés (et suivis) par git, on peut utiliser la commande
`git  commit -a` sans avoir à utiliser `git add`. Néanmoins c’est une
habitude à éviter car on risque d’inclure dans le commit des
modifications non souhaitées.

Supprimer un fichier du dépôt (et non en local) :

    $ git rm --cached fichier

Voir l’état du dépôt local (fichiers non commités, etc.) :

    $ git status
    $ git status -s -b

Voir les derniers commits du dépôt local :

    $ git log

### pull/push avec un dépôt distant

Si l’on a cloné un dépôt existant, un lien est automatiquement créé
entre le dépôt local créé et le dépôt distant (référencé comme
*origin*).

Pour récupérer en local les derniers changements du dépôt distant :

    $ git pull --rebase origin

*Note* : l’option `--rebase` est désormais le défaut mais par pédagogie
on conseille de la mettre explicitement.

Pour envoyer ses modifications locales vers le dépôt référencé comme
distant :

    $ git push origin

### Gestion des branches

Par défaut, certains outils utilisent une branche nommée *master*. Cette
branche existe donc souvent au sein d’un dépôt, mais il faut garder en
tête que c’est une convention, rien n’oblige à en avoir une.

Lister les branches existantes et connaître sa branche courante :

    $ git branch -a

Passer d’une branche à une autre dans son dépôt local :

    $ git checkout myfeature
    $ git checkout master

Créer une branche *myfeature* à partir de la branche *master* :

    $ git checkout master
    $ git branch myfeature master

Renommer la branche courante :

    $ git branch -m <nouveau nom>

Renommer une branche autre que la branche courante :

    $ git branch -m <ancien nom> <nouveau nom>

Travailler sur la branche *myfeature* puis [merger] son travail dans la
branche *master* :

    $ git checkout master
    $ git merge --no-ff myfeature

Pousser une branche locale vers le dépôt référencé comme distant :

    $ git push origin myfeature

Supprimer une branche locale et distante :

    $ git branch -d myfeature
    $ git push origin :myfeature

Supprimer les branches locales qui ne sont pas présentes sur le dépôt
distant :

    $ git fetch -p

Commandes avancées
------------------

### Afficher l’historique

Affichage de l’historique de différentes façons :

    $ git log
    $ git log -p
    $ git log --stat
    $ git log --summary
    $ git log --color
    $ git log --graph
    $ git log --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset'

Afficher seulement les fichiers modifié :

    $ git log --name-only
    $ git show --name-only COMMIT

On peut notamment combiner toutes ces options dans un alias à mettre
dans `.gitconfig` :

    [alias]
        lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset'

On peut aussi utiliser *whatchanged* qui va lister tout ce qui a changé
:

    $ git whatchanged
    $ git whatchanged -p

Voir l’avant-dernier commit :

    $ git show HEAD~1

Voir le fichier *foo/bar* tel qu’il était dans l’avant-dernier commit :

    $ git show HEAD~1:foo/bar

Pour voir ce qu’il s’est passé entre deux commits :

    $ git show HEAD~8..HEAD
    $ git show -p HEAD~8..HEAD

On peut voir graphiquement l’historique avec différents outils, par
exemple *gitg*, *gitk* ou *tig* :

    # apt install gitk gitg tig
    $ gitg
    $ gitk --all
    $ tig

### Modifier l’historique

/!\\ Les modifications de l’historique ne doivent avoir lieu que si cela
n’a pas été pushé vers un dépôt partagé !

Modifier son dernier message de commit :

    $ git commit --amend

Modifier son dernier commit :

    $ vi main.c
    $ git add main.c
    $ git commit --amend

Modifier son avant-dernier commit :

    $ git rebase -i HEAD^^
    … remplacer « pick » par « edit » pour le commit à modifier
    $ vi main.c
    $ git add main.c
    $ git commit --amend
    $ git rebase --continue

*Note* : On peut remonter au Nième commit en utilisant *HEAD\~N*

### Picorer un commit d’une autre branche (cherry-pick)

<https://git-scm.com/docs/git-cherry-pick>

Récupérer un commit d’une autre branche dans master :

    $ git checkout master
    $ git cherry-pick <SHA1 du commit>

Si tout se passe bien, il ne demandera rien de plus sauf s’il y a des
conflits. Dans ce cas, il faut éditer les fichiers où la commande
`git status` indique “modifié des deux côtés : conflit.txt”.

Après avoir effectué ces modifications, il faudra lancer la commande
suivante pour confirmer l’action :

    $ git cherry-pick --continue

Sinon pour tout annuler :

    $ git cherry-pick --abort

### gitignore

<https://git-scm.com/docs/gitignore>

Créer un fichier `.gitignore` à la racine pour ignorer certains
fichiers/chemins :

    $ cat .gitignore

    test.php
    htdocs/foo/bar
    foo/bar*

> **Note** : Le fichier `.gitignore` se commit dans le dépôt.\
> **Note** : Si on veut ignorer un fichier déjà commité, il faudra le
> supprimer de l’indexe avec `git rm --cached <file>`.

On peut également avoir un fichier `~/.config/git/ignore` qui sera
valable pour tous les dépôts Git.

### Ranger temporairement son travail (git stash)

<https://git-scm.com/docs/git-stash>

Cela permet d’avoir un buffer pour mettre « en pause » des modifications
non commitées :

    …hack…hack…
    $ git stash save

On peut lister ce buffer :

    $ git stash list
    stash@{0}: WIP on dev: fb1fa70… m
    stash@{1}: WIP on dev: fb1fa70… m

Pour appliquer la dernière modification stockée :

    $ git stash pop

Pour purger le buffer sans l’appliquer :

    $ git stash clear

Pour voir une des modifications :

    $ git stash show -p stash@{0}

Pour retrouver un stash pop grâce à gitk :

    gitk --all $( git fsck --no-reflog | awk '/dangling commit/ {print $3}' )

### git diff

<https://git-scm.com/docs/git-diff>

Voir les modifications locales non commitées et non indexés :

    $ git diff

Voir les modifications locales *suivies* (ajoutées avec *git add*) :

    $ git diff --cached

Voir les modifications entre deux commits, ou entre deux branches :

    $ git diff HEAD^ HEAD
    $ git diff HEAD~8 HEAD^^
    $ git diff myfeature master

*Note* : si le 2ème commit est *HEAD* on peut ne pas le mentionner,
`git diff HEAD^` montrera les modifications du dernier commit

Voir les modifications entre deux commits, mais uniquement pour un
fichier *foo* :

    $ git diff HEAD~8..HEAD^^ -- foo

Avoir un diff inversé (pratique pour faire préparer un revert partiel
par exemple) :

    $ git diff -R

Avoir des stats entre 2 commits ou branches :

    $ git diff --stat HEAD~8 HEAD^^
    $ git diff --stat myfeature master

Voir les modifications en mode “mots” plutôt qu’en mode “ligne” (très
pratiques pour voir les changements au sein d’une ligne) :

    $ git diff --word-diff

Voir les modifications depuis un certain temps sur un dépôt complet ou
sur un fichier précis :

    $ git diff HEAD 'HEAD@{3 hours ago}'
    $ git diff HEAD 'HEAD@{3 weeks ago}' -- foo/bar.txt

### git pull/fetch

<https://git-scm.com/docs/git-pull>

`git pull` est une commande qui effectue un rapatriement des données
(`git fetch`) puis l’intégration de ces données via `git rebase` (par
défaut) ou `git merge`.

<https://git-scm.com/docs/git-fetch>

Dans certains cas il est pratique de faire un simple `git fetch`,
notamment si l’on veut faire des manipulations complexes ou si l’on veut
avoir toutes les données pour travailler hors ligne sans rien changer à
l’état actuel de sa *working copy*.

### git push

<https://git-scm.com/docs/git-push>

Pousser toutes ses branches d’un coup (ce qui est déconseillé en
général) :

    $ git push --all origin

Si l’on a une branche locale nommée *foo* et que l’on veut la pousser
dans la branche nommée *bar* du dépôt distant :

    $ git push origin foo:bar

/!\\ Attention, il ne faut jamais modifier un historique qui a été pushé
vers un dépôt partagé. Néanmoins dans certaines situations
exceptionnelles (un mot de passe ou un email qui aurait été mis par
erreur) on peut être amené à faire un `push --force` :

    $ git push -f origin

Il faudra ensuite que chaque utilisateur du dépôt soit prévenu et
accepte d’écraser leur dépôt local (en faisant ou vérifiant les
modifications d’historiques) :

    $ git fetch
    $ git reset origin

### git checkout

<https://git-scm.com/docs/git-checkout>

Pour avoir le dépôt tel qu’il était à un commit :

    $ git checkout <SHA1 du commit>

Ou, un fichier spécifique tel qu’il était à un commit :

    $ git checkout <SHA1 du commit> -- <fichier>

On est alors dans l’état « HEAD détachée ». Pour revenir dans l’état
précédent :

    $ git checkout -

Pour se remettre sur la version courante :

    $ git checkout master

Pour créer une branche et switcher directement dans cette nouvelle
branche, on utilise l’option `-b` :

    $ git checkout -b myfeature

Pour annuler toutes les modifications d’un fichier foo/ non indexé :

    $ git checkout foo/

### git branch

<https://git-scm.com/docs/git-branch>

Une branche est en fait la création une déviation, un commit qui a deux
*enfants*. On peut ainsi créer une branche à partir de n’importe quel
commit :

    $ git branch newbranch e150b8517a694a2d4816cff95ef612086d644f67

Pour supprimer la branche *foo* sur un dépôt distant non cloné :

    $ git push ssh://git.example.com/projet.git :foo

Pour récupérer une nouvelle branche depuis le dépôt référencé comme
distant :

    $ git remote update

### git reset/clean

<https://git-scm.com/docs/git-reset>\
<https://git-scm.com/docs/git-clean>

/!\\ Certaines commandes peuvent provoquer une perte de données !

Pour supprimer toutes les modifications non commitées et les
fichiers/répertoires non indexés :

    $ git reset --hard
    $ git clean -f -d

On peut aussi `git reset` sur des commits précédents
(*HEAD\^*,*HEAD\~N*,*<SHA1 du commit>*). Ou même sur une branche
distante comme *origin/master* par exemple (ce qui est pratique si
l’historique de la branche distante a été modifié).

Pour désindexer des modifications d’un fichier :

    $ git reset HEAD <fichier>

### git add

<https://git-scm.com/docs/git-add>

Pour ajouter tout un répertoire et son contenu :

    $ git add foo/

Pour choisir ce que l’on veut ajouter pour le prochain commit :

    $ git add -p

### git merge

<https://git-scm.com/docs/git-merge>

Un merge consiste à créer un commit qui aura deux parents, et permet
ainsi de fusionner deux branches existantes.

Quand un merge est très simple, c’est-à-dire que cela rajoute simplement
des commits sur une branche qui n’a pas bougé entre temps, on n’a pas
besoin de créer un commit pour fusionner, on appelle cela un merge
**fast-forward**. Cela se fait automatiquement avec la commande
`git merge`. Notons que l’on peut vouloir tout de même avoir un commit
pour marquer le coup et forcer un commit de merge avec l’option
`git merge --no-ff`.

Quand un merge n’est pas simple, Git peut adopter plusieurs stratégies
(resolve, recursive, ours, theirs, patience, etc.). Il est probable que
cela génère des conflits qui devront être résolus manuellement.

### git apply

<https://git-scm.com/docs/git-apply>

`git apply` permet d’appliquer un diff.

On peut utiliser une option pour exclure l’application de certain
fichier du diff :

    $ git apply --exclude=debian/changelog /tmp/foo.diff

### git blame

<https://git-scm.com/docs/git-blame>

Pour voir qui a modifié les lignes d’un fichier *foo/bar* :

    $ git blame foo/bar

### git revert

<https://git-scm.com/docs/git-revert>

Pour effectuer les modifications inverses de celles effectuées (et donc
revenir à l’état précédent :

    $ git revert <SHA1 du commit>

### git tag

Ajouter et pousser un tag à l’endroit où l’on est :

    $ git tag -a foo -m "version foo"
    $ git push origin foo

Lister les tags :

    $ git tag
    foo

Changer un tag foo en bar sur un dépôt distant :

    $ git co foo && git tag bar && git tag -d foo && git push origin :foo && git push --tags

### .git/config

<https://git-scm.com/docs/git-config>

La configuration d’un dépôt se trouve dans le fichier `.git/config` On
peut éditer ce fichier ou utiliser des commandes pour le faire.

Pour ajouter un dépôt distant :

    $ git remote add origin2 ssh://git.example.com/path/projet.git

Pour lister les dépôts distants configurés :

    $ git remote

### Échanger des commits sous forme de patchs Git

Pour transmettre ses 3 derniers commits, on peut générer 3 patches qui
contiendront les diffs ainsi que les messages de commit :

    $ git format-patch -3

Si l’on a ces 3 patches, on peut les appliquer sur son dépôt local ainsi
:

    $ git am 000*.patch

### hooks

On peut gérer des hooks à différents moments (pre-commit, post-commit,
pre-push, post-push, etc.), par exemple pour générer des emails de
notification.

Voir dans le répertoire `.git/hooks/`.

On peut par exemple s’en servir pour [activer des mails de commit]

### dépôt « bare »

Un dépôt classique possède un répertoire `.git/` qui contient toutes les
données et méta-données, ainsi qu’une *working copy* de la branche
courante.

Quand on crée un dépôt ayant vocation à servir de dépôt central, il est
préférable de ne pas avoir de *working copy* : il s’agira alors d’un
**bare repository** que l’on peut initier avec la commande :

    $ mkdir foo
    $ cd foo
    $ git init --bare

Workflow de travail
-------------------

Il existe plusieurs workflows possibles en travaillant avec Git.

### Le modèle Git branching

Voir <http://nvie.com/posts/a-successful-git-branching-model/>

L’idée est de ne jamais toucher à *master*, sauf hotfixes. Pour le reste
on le fait dans *dev*. Et pour les grosses features on le fait dans une
branche, nommée avec le nom de la feature.

Plomberie
---------

<https://git-scm.com/book/fr/Git-Commands-Plumbing-Commands>

La plomberie de Git consiste à réaliser des commandes bas niveau
manipulant tout ce qui se trouve dans le répertoire `.git/` d’un dépôt.

Les données brutes sont dans le répertoire `.git/objects/` qui contient
de nombreux fichiers : à chaque commit correspond un fichier nommé en
fonction de l’empreinte SHA1 du commit et contenant les données
compressées. Les données comprennent notamment la référence au(x)
commit(s) *parent* ce qui permet d’avoir des liens entre les commits.

Observons `.git/refs/` qui contient des pseudos-pointeurs (fichier texte
d’une seule ligne contenant un SHA1 ou une autre référence) :

-   un tag est simplement un pointeur vers un commit ;
-   une branche est également un pointeur vers un commit, la différence
    est qu’il se déplace à chaque nouveau commit dans la branche.

Directement dans `.git/` on a également *HEAD* (et *FETCH\_HEAD*,
*ORIG\_HEAD*) qui sont aussi des pseudo-pointeurs, qui changent
notamment quand on change de branche ou pendant un *rebase*.

Si l’on veut mettre à jour ces pseudo-pointeurs, il faut utiliser
`git update-ref`.

Observons `.git/logs/` qui contient l’historique de ce qui a été fait
dans le dépôt. Cet historique est notamment accessible avec la commande
`git reflog`.

FAQ
---

### Partager un dépôt en HTTP - simplement

<https://www.kernel.org/pub/software/scm/git/docs/git-http-backend.html>

Le dépôt git est dans /home/git/projets/projet.git, et le serveur web
est Apache2.4.

``` {.bash}
SetEnv GIT_PROJECT_ROOT /home/git/
SetEnv GIT_HTTP_EXPORT_ALL
ScriptAlias /git/ /usr/lib/git-core/git-http-backend/

Alias /git /home/git/projets
<Location /git>
  AuthType Basic
  AuthName "Git HTTP Access"
  AuthUserFile /etc/apache2/htpasswd_git
  Require user git
</Location>
```

Pour cloner le dépot :

``` {.bash}
$ git clone http://git@domain:port/git/projet
```

### Partager un dépôt avec plusieurs utilisateurs

Avec un dépôt *foo* existant, on autorisera les utilisateurs que s’ils
appartiennent au groupe *git* :

    # cd foo
    # git config core.sharedRepository group
    # addgroup git
    # chgrp -R git .
    # chmod g=rwXs,o= -R .
    # chmod g=rwX -R . # astuce pour garder le +s sur les répertoires

### Importer un dépôt SVN dans GIT

Ce script permet de récupérer la liste des auteurs SVN, modifiez-la
comme voulu.

``` {.bash}
#!/usr/bin/env bash
authors=$(svn log -q | grep -e '^r' | awk 'BEGIN { FS = "|" } ; { print $2 }' | sort | uniq)
for author in ${authors}; do
echo "${author} = Prenom Nom <jdoe@example.com>";
done
```

On lance ensuite la commande suivante :

    $ git svn --authors-file=path/to/authors_file clone svn+ssh://svn.example.com/path/to/repo

### Importer un dépôt CVS dans GIT

Lancer la commande suivante :

    $ git cvsimport -C repo_git -r cvs -k -vA path/to/authors_file -d user@hostname:/path/to/cvsroot repo

### Importer un dépôt Arch dans GIT

Lancer la commande suivante :

    $ git-archimport -v foo@arch.example.com--branch

### Convertir un dépôt en utf8

Créer un fichier exécutable dans `/tmp/recode-all-files` :

``` {.bash}
#!/bin/sh
find . -type f -print | while read f; do
        mv -i "$f" "$f.recode.$$"
        iconv -f iso-8859-1 -t utf-8 < "$f.recode.$$" > "$f"
        rm -f "$f.recode.$$"
done
```

Puis exécuter la commande suivante dans votre dépôt :

    $ git filter-branch --tree-filter /tmp/recode-all-files HEAD

Exécuter ensuite la commande suivante pour convertir les messages de
commit :

    $ git filter-branch --msg-filter 'iconv -f iso-8859-1 -t utf-8' -- --all

### Push vers un non-bare repository

Ceci est évidemment déconseillé, car cela mettra aussi à jour les
fichiers, ce qui nécessite de faire un `git reset --hard`. Mais si l’on
veut tout de même le forcer :

    $ git push
    Counting objects: 7, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (4/4), done.
    Writing objects: 100% (4/4), 618 bytes, done.
    Total 4 (delta 3), reused 0 (delta 0)
    Unpacking objects: 100% (4/4), done.
    remote: error: refusing to update checked out branch: refs/heads/master
    remote: error: By default, updating the current branch in a non-bare repository
    remote: error: is denied, because it will make the index and work tree inconsistent
    remote: error: with what you pushed, and will require 'git reset --hard' to match
    remote: error: the work tree to HEAD.
    remote: error:
    remote: error: You can set 'receive.denyCurrentBranch' configuration variable to
    remote: error: 'ignore' or 'warn' in the remote repository to allow pushing into
    remote: error: its current branch; however, this is not recommended unless you
    remote: error: arranged to update its work tree to match what you pushed in some
    remote: error: other way.
    remote: error:
    remote: error: To squelch this message and still keep the default behaviour, set
    remote: error: 'receive.denyCurrentBranch' configuration variable to 'refuse'.
     ! [remote rejected] master -> master (branch is currently checked out)

Il faut ajouter dans la config du repository « distant » :

    [receive]
        denyCurrentBranch = warn

### Transformer un non-bare repository en bare repository

Il suffit de supprimer tous les fichiers sauf le `.git`, par exemple :

    $ rm -rf *

Puis d’indiquer dans la config du repository :

    bare = true

### Mettre en place des notifications de push

Sous Debian, pour envoyer des notifications par email à git@example.com
:

    # chmod a+x /usr/share/doc/git-core/contrib/hooks/post-receive-email
    $ cd /path/path/to/your/repository.git
    $ ln -sf /usr/share/doc/git-core/contrib/hooks/post-receive-email hooks/post-receive
    $ git hooks.mailinglist git@example.com

Pour recevoir les patches :

    $ git config hooks.showrev "git show -C %s; echo

### Ignorer les vérifications SSL

(à éviter autant que possible bien sûr)

    $ git config --global http.sslverify "false"

### Nettoyage d’un dépôt Git

Quand on supprime des commits, ils ne sont pas vraiment supprimés du
dépôt. Cela offre l’avantage de pouvoir les retrouver en cas de besoin.
Si l’on veut gagner de la place, on peut aussi faire un nettoyage :

    $ git repack
    $ git gc

### Migrer un dépot git

Il se peut qu’on change de forge (passage de gitolite à gitlab par
exemple) et il faut alors migrer les dépots. Un moyen de faire :

``` {.bash}
$ for branch in `git branch -a | grep remotes | grep -v HEAD | grep -v master`; do rbranch=$(echo $branch | tr '/' ' ' | awk '{print $3}') ; git checkout -b $rbranch $branch ; done
$ git remote add gitlab gitlab@gitlab.example.com:group/repo.git && git push gitlab --all && git remote remove origin && git remote rename gitlab origin && git push --set-upstream origin master
```

### Avoir des stats basiques

Il existe la commande shortlog pour avoir quelques stats sur un dépôt
git

    $ git shortlog -sne

### Supprimer un fichier du dépôt et non en local

    $ git rm --cached file

### Supprimer le dernier commit

À faire avec précaution bien sûr :

    $ git reset --hard HEAD^

### Troubleshooting bitbucket

Si soucis avec Bitbucket, notamment performance réseau, on peut tester :

    $ git clone https://bitbucket.org/mirror/git.git

[Notamment Bitbucket a des performances mauvaises (ou nulles) en IPv6.]

Voir
<https://confluence.atlassian.com/bbkb/troubleshooting-network-issues-389778693.html>

### Algorithme de diff

Un diff peut être présenté de différentes façons.

Exemple très simple, si le fichier original est :

    [foo]
    A

et le fichier modifié est :

    [bar]
    A

Le diff peut être :

    -[foo]
    +[bar]

ou :

    -[foo]
    -A
    +[bar]
    +A

…évidemment cet exemple très simple est juste pour démontrer qu’il peut
y avoir des diffs différents pour une même modification.

Ainsi, on peut utiliser différents algorithmes de diff avec `git diff`
ou `git show` : minimal, patience, histogram ou myers.

    $ git diff --minimal
    $ git diff --patience
    $ git diff --histogram
    $ git diff --diff-algorithm=myers

### alias

Voici des alias qui peuvent être intéressants :

    d = diff --ignore-space-change --patience --no-prefix
    # affiche le diff en mode "patience", sans les changements d'espaces et sans les prefix

    dw = diff --word-diff
    # affiche le diff en mode "word" qui met en évidence les changements au sein de chaque ligne

    lg = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative
    # log des commits affichés avec une présentation riche mais compacte

    cane = commit --amend --no-edit
    # modifie le dernier commit sans éditer le message de commit

    rf = !sh -c 'rm $1 && git checkout $1'
    # restaure un fichier/emplacement depuis le dépôt

    s = status --short --branch
    # affiche un statut compacte

    conflicts = !sh -c 'git status -sb | grep ^UU | sed "s/UU\\ //"'
    # liste les fichiers ayant un conflit non résolu

    resolve = !sh -c 'git conflicts && $EDITOR $(git conflicts) +\"/<<<<\"'
    # ouvre les fichiers en conflit dans un éditeur (compatible Vim), positionné au bon endroit

### Quitter l’éditeur de texte sans que Git n’exécute l’action liée

Lorsque Git délègue à l’éditeur de texte la rédaction du message de
commit (ou autre édition), si l’éditeur quitte avec un code de sortie 0,
alors Git considère que tout va bien et exécute son action initiale
(faire un commit par exemple). Si on veut que Git ne fasse pas cette
action, il suffit de faire quitter l’éditeur avec un code de sortie &gt;
0. Avec Vim, ça se fait avec la commande `:cq`.

Exemple concret de cette situation : on veut amender un commit auquel on
a ajouté (ou retiré) certains éléments, mais au moment de rédiger le
message on se rend compte qu’on en a trop ajouté (ou retiré). Un simple
`:q!` dans Vim ne validera pas le message de commit mais validera le
commit lui-même. C’est là qu’on peut sortir de Vim et annuler l’action
de Git avec `:cq`.

  [Git]: https://git-scm.com/
  [merger]: #git-merge
  [activer des mails de commit]: #mettre-en-place-des-notifications-de-push
  [Notamment Bitbucket a des performances mauvaises (ou nulles) en
  IPv6.]: https://grosse.io/blog/posts/Fixing-slow-Bitbucket-git-connections-via-SSH
