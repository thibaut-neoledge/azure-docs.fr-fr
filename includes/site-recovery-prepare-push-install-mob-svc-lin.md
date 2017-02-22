### <a name="prepare-for-push-install-on-linux-servers"></a>Préparation de l’installation de transmission (push) sur des serveurs Linux

1. Vérifiez qu’il existe une connectivité réseau entre la machine Linux et le serveur de processus.
2. Créez un compte pouvant être utilisé par le serveur de traitement pour accéder à la machine. Le compte doit être un utilisateur **racine** sur le serveur Linux source et être utilisé uniquement pour les installations/mises à jour de transmission (push).
3. Vérifiez que le fichier `/etc/hosts` qui se trouve sur le serveur Linux source contient des entrées qui mappent le nom d’hôte local sur les adresses IP associées à toutes les cartes réseau.
4. Installez les packages openssh, openssh-server et openssl les plus récents sur la machine à répliquer.
5. Vérifiez que SSH est activé et exécuté sur le port 22.
6. Activez le sous-système SFTP et l'authentification par mot de passe dans le fichier sshd_config comme suit :
  - Connectez-vous en tant qu’utilisateur **racine**.
  - Dans le fichier /etc/ssh/sshd_config, recherchez la ligne commençant par **PasswordAuthentication**.
  - Supprimez les commentaires de la ligne et remplacez la valeur **no** par **yes**.
   Recherchez la ligne commençant par **Subsystem**, puis supprimez la mise en commentaire qui lui est associée.

     ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

7. Ajoutez le compte que vous avez créé dans l’outil CSPSConfigtool.

    - Connectez-vous à votre serveur de configuration.
    - Ouvrez le fichier **cspsconfigtool.exe**. Il est disponible sous forme de raccourci sur le bureau et sous le dossier %ProgramData%\home\svsystems\bin.
    - Dans l’onglet **Gérer les comptes**, cliquez sur **Ajouter un compte**.
    - Ajoutez le compte que vous venez de créer. Après avoir ajouté le compte, vous devez fournir les informations d’identification lorsque vous activez la réplication d’une machine.


<!--HONumber=Jan17_HO3-->


