Le script de déploiement ignorera la création de l'environnement virtuel sur Azure s'il détecte qu'il existe déjà un environnement virtuel compatible.  Ceci peut accélérer considérablement le déploiement.  Les packages déjà installés sont ignorés par pip.

Dans certaines situations, vous pouvez souhaiter forcer la suppression de cet environnement virtuel.  Cela peut être intéressant notamment si vous décidez d'inclure un environnement virtuel dans le cadre de votre référentiel.  Vous pouvez également effectuer cette opération si vous devez supprimer certains packages ou tester les modifications apportées au fichier requirements.txt.

Différentes options permettent de gérer l'environnement virtuel existant sur Azure :

### Option 1 : Utilisation de FTP

Avec un client FTP, connectez-vous au serveur. Vous serez alors en mesure de supprimer le dossier env.  Notez que certains clients FTP (tels que les navigateurs web) peuvent être en lecture seule et ne permettent pas de supprimer des dossiers. Veillez donc à utiliser un client FTP offrant cette fonctionnalité.  Le nom d'hôte et le nom d'utilisateur FTP sont affichés dans la page Tableau de bord pour votre site Web, sur le portail Azure.

### Option 2 : Exécution de bascule

Cette alternative tire parti du fait que le script de déploiement supprime le dossier env lorsque celui-ci ne correspond pas à la version souhaitée de Python.  Cette opération supprime efficacement l'environnement existant et en crée un nouveau.

1. Passez à une autre version de Python (via runtime.txt ou la page de configuration de site web).
1. git force certaines modifications (ignorez les erreurs d'installation pip, le cas échéant).
1. Revenez à la version initiale de Python.
1. git force à nouveau des modifications.

### Option 3 : Script de déploiement personnalisé

Si vous avez personnalisé le script de déploiement, vous pouvez modifier le code dans le fichier deploy.cmd afin de forcer la suppression du dossier env.

<!--HONumber=49-->