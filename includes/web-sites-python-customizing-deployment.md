Azure détermine que votre application utilise Python **si les deux conditions suivantes sont remplies** :

- Fichier requirements.txt présent dans le dossier racine
- N’importe quel fichier .py présent dans le dossier racine ou un fichier runtime.txt spécifiant python

Lorsque ces deux conditions sont réunies, un script de déploiement spécifique Python est utilisé, effectuant la synchronisation des fichiers ainsi que des opérations supplémentaires Python tels que :

- Gestion automatique de l’environnement virtuel
- Installation des packages répertoriés dans requirements.txt à l’aide de pip
- Création du fichier web.config approprié selon la version de Python sélectionnée
- Collecte des fichiers statiques pour les applications Django

Vous pouvez contrôler certains aspects de la procédure de déploiement par défaut sans avoir à personnaliser le script.

Si vous souhaitez ignorer toutes les étapes de déploiement spécifiques à Python, vous pouvez créer ce fichier vide :

    \.skipPythonDeployment

Si vous souhaitez ignorer les fichiers statiques pour votre application Django :

    \.skipDjango 

Pour mieux contrôler le déploiement, vous pouvez remplacer le script de déploiement par défaut en créant les fichiers suivants :

    \.deployment
    \deploy.cmd

Vous pouvez utiliser l’[interface de ligne de commande Azure][] pour créer les fichiers. Utilisez cette commande à partir de votre dossier de projet :

    azure site deploymentscript --python

Lorsque ces fichiers n’existent pas, Azure crée un script de déploiement temporaire et l’exécute. Il est identique à celui que vous créez à l’aide de la commande ci-dessus.

[interface de ligne de commande Azure]: http://azure.microsoft.com/downloads/

<!---HONumber=August15_HO6-->