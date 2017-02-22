Les étapes d’annulation de l’inscription d’un serveur de traitement diffèrent en fonction de son état de connexion avec le serveur de configuration.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Annuler l’inscription d’un serveur de traitement qui se trouve dans un état connecté

1. Accédez à distance au serveur de traitement en tant qu’administrateur.
2. Démarrez le **Panneau de configuration** et ouvrez **Programmes > Désinstaller un programme**
3. Désinstallez le programme nommé **Serveur de configuration/traitement Microsoft Azure Site Recovery**
4. Une fois l’étape 3 terminée, vous pouvez désinstaller **Dépendances du serveur de configuration/traitement Microsoft Azure Site Recovery**

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Annuler l’inscription d’un serveur de traitement qui se trouve dans un état déconnecté

> [!WARNING]
> Utilisez les étapes ci-dessous s’il n’existe aucun moyen de réactiver la machine virtuelle sur laquelle le serveur de traitement a été installé.

1. Connectez-vous à votre serveur de configuration en tant qu’administrateur.
2. Ouvrez une invite de commande d’administration et accédez au répertoire `%ProgramData%\ASR\home\svsystems\bin`.
3. Ensuite, exécutez la commande.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Cette opération vide les informations du serveur de traitement du système.


<!--HONumber=Feb17_HO1-->


