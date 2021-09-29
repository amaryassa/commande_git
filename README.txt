
###########################################################################
##                             configuration                             ##
###########################################################################
  git config --list --show-origin
  git config --global user.email xxxxx@yahoo.fr
  git config --local user.email xxxxxxxx@gmail.com
  git config --global user.email
  git config --local user.email 
  git config --global --unset user.name
  git config --global --remove-section user
  git config --local --remove-section user

  git cat-file [sha1 commit] -p //connaitre l'origine d'un commit

###########################################################################
##                              GIT LOG                                  ##
###########################################################################

  git log --pretty=format:"%h %s" --graph
  git log --oneline --all --graph

###########################################################################
##                              GIT DIFF                                 ##
###########################################################################

  git diff
    différence entre  working area(en rouge) et staging area (en vert) [avant le commit]
      git diff <file> 
    différence entre le staging (après le add) et le repo (celui qui a déjà été commité)
      git diff --staged <file>
    différence entre le working area et le repo (commit)
      git diff Head <file>

###########################################################################
##                            Rename File                                ##
###########################################################################
  git mv oldFileName.txt newFileName.txt
  équivalent à  mv oldFileName.txt newFileName.txt; git add oldFileName.txt newFileName.txt


###########################################################################
##               DELETE/REMOVE Chages (git status)                       ##
###########################################################################

  git restore --staged test.txt (on l'enlève du ADD)
  git restore test.txt (on remets comme le commit)

  
##########################################################################
##                               Branches                               ##
##########################################################################

    git branch // cat .git/HEAD 
    git branch nouvelle-branche //create
    git checkout master //switch
    git switch new_branch //switch
    git checkout -b "new_branch" //create + switch
    git switch -c nouvelle-branche //create + switch
    git branch -m my_branch // MOVE/Rename branch (faut se mettre sur la branche)
    git branch -d my_branch // delete branch
    git switch - //revenir sur votre branche précédemment extraite :
    git branch -v //Pour visualiser la liste des derniers commits sur chaque branche, vous pouvez utiliser le commande 
    git branch --merged
    git branch --no-merged
##########################################################################
##                               MERGE                               ##
##########################################################################
    git branch --merged
    git branch --no-merged
    git merge login_feature
      si le master n'a pas été changé depuis la branch alors c'est un merge fast-forward (pas de nouveau commit qui serait créé comme si le master rejoint l'autre branche mergé)
      sinon "Merge made by the 'recursive' strategy" si le master a changé depuis (sans conflit), un nouveau commit sera créé
      sinon conflit
        git merge --abort //pour annuler le merge
        sinon
        on va résoudre le conflit puis
          git add . 
          git commit 
         
##########################################################################
##                                REBASE                                ##
##########################################################################

  Rebaser (Rebasing)
    git rebase --onto master serveur client //Cela signifie en substance "Extraire la branche client, déterminer les patchs depuis l’ancêtre
      commun des branches client et serveur puis les rejouer sur master ". C’est assez complexe, mais le
      résultat est assez impressionnant. (PAGE 103)

    git rebase master brancheA // rajoute toutes la brancheA après master, après ne pas oublier de switch sur master et faire un merge brancheA
    sur la branche master (checkout master):
      git rebase brancheA / ramener la branche master et la placer après la brancheA
    

  Rebase interactive:
    git log --oneline --graph
      * 445948d (HEAD -> login) modification TERMINEE OK
      * d2f5f3b modification 3
      * bcc9b78 modification 2
      * b11b247 modification 1
      * 893776b creation fichier login
      * 127b9f3 (master) premiere modification depuis master
      * 261018f (feature) modification depuis feature
      * 98d3975 init file master from master

    git rebase -i master // récupérer les commit jusqu'à la branche master // il va récupérer à partir de 893776b
      OU : git rebase -i HEAD~5 // récupère moi les 5 derniers commentaires
      puis mettre remplacer les pick par s(squash commit = utiliser le commit, mais fusionner avec le précédent commit) sauf le premier commentaire
        pick 445948d modification TERMINEE OK
        s d2f5f3b modification 3
        s bcc9b78 modification 2
        s b11b247 modification 1
        s 893776b creation fichier login

      puis changer la desc du commit et sauvegarder
    git switch master
    git merge login
    résultat 
    git log --oneline --graph
    * d86f2f2 (HEAD -> master, login) Login Feature complete !
    * 127b9f3 premiere modification depuis master
    * 261018f (feature) modification depuis feature
    * 98d3975 init file master from master
      

  Les dangers du rebasage
    Ne rebasez jamais des commits qui ont déjà été poussés sur un dépôt public.


##########################################################################
##                   Erreur de commit (précipitation)                   ##
##########################################################################

  Changer le dernier commit (ex :on s'est trompé dans un fichier, ou on a pas ajouté un fichier et on a commité rapidement)
    on modifie notre fichier
    git add .
    git diff --cached // git diff --staged [voir la différence ]
    git commit --amend //on peut meme changer la desc du dernier commit
    git log --oneline // on voit que y a pas eu un autre commit et les modifications sont prises en compte


###########################################################################
##                          GIT cherry-pick                              ##
###########################################################################
  git cherry-pick  d2f5f3b 98d3975 d2f5f3b


##########################################################################
##                                RESET                                 ##
##########################################################################

  git reset sha1Commit // revenir à un commentaire précédent sans perdre le travail des autres commits(donc il faut git add . pour les rajouter si on veut)
  git reset sha1Commit --hard //sans possibilité de récupération des commits supprimés

  git reset head --mixed // reset d'un truc déjà dans le stageArea vers workingArea <==>  git restore --staged <file> (on l'enlève du ADD)
  git reset head --hard //on perd les données modifiées

##########################################################################
##                                Distant                                 ##
##########################################################################
    //Pousser la branche renommée sur le server
  git push --set-upstream origin nom-de-branche-corrigé
  git push origin --delete mauvais-nom-de-branche
  git push (serveur distant) (branche)
  git push origin correctionserveur:branchegeniale //si on veut changer le nom de la branche local et lui donner un autre nom sur le serveur

  récupérer les modifications des autres avec fetch: quand on récupère une branche elle n'est pas fusionné directement avec notre travail il faut un merge si on veut  
    git fetch servreDistant_origin_par_exemple
    git merge origin/correctionserveur // pour le merge
    git checkout -b correctionserveur origin/correctionserveur // si on veut créer notre propre branche,Cette commande vous fournit une branche locale modifiable basée sur l’état actuel de
  origin/correctionserveur
  *****************************************
  Suivre les branches [***** à relire le cours******] :
    L’extraction d’une branche locale à partir d’une branche distante crée automatiquement ce qu’on
    appelle une "branche de suivi" (tracking branch) [sont des branches locales qui sont en relation
    directe avec une branche distante.]
  *****************************************
  Suppression de branches distantes
    git push origin --delete correctionserveur
