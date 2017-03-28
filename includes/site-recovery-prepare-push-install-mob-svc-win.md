### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Préparer une installation push sur un ordinateur Windows

1. Vérifiez qu’il existe une connectivité réseau entre l’ordinateur Windows et le serveur de processus.
2. Créez un compte pouvant être utilisé par le serveur de traitement pour accéder à l’ordinateur. Le compte doit disposer des droits d’administrateur (local ou de domaine). (Utilisez ce compte uniquement pour l’installation push et les mises à jour de l’agent.)

   > [!NOTE]
   > Si vous n’utilisez pas un compte de domaine, désactivez le contrôle d’accès utilisateur distant sur l’ordinateur local. Pour ce faire, sous la clé de registre HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, ajoutez un nouveau DWORD : **LocalAccountTokenFilterPolicy**. Définissez la valeur sur **1**. Pour exécuter cette action à l’invite de commandes, exécutez la commande suivante :  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
2. Dans le pare-feu Windows de l’ordinateur à protéger, sélectionnez **Autoriser une application ou une fonctionnalité à franchir le pare-feu**. Activez **Partage de fichiers et d’imprimantes** et **Windows Management Instrumentation (WMI)**. Pour les ordinateurs qui appartiennent à un domaine, vous pouvez configurer les paramètres de pare-feu avec un objet de stratégie de groupe.

   ![Paramètres du pare-feu](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. Ajoutez le compte créé dans l’outil CSPSConfigtool.
    1.  Connectez-vous à votre serveur de configuration.
    2.  Ouvrez le fichier **cspsconfigtool.exe**. (Il est disponible sous forme de raccourci sur le bureau et dans le dossier %ProgramData%\home\svsystems\bin.)
    3.  Dans l’onglet **Gérer les comptes**, sélectionnez **Ajouter un compte**.
    4.  Ajoutez le compte que vous venez de créer.
    5.  Entrez les informations d’identification utilisées lors de l’activation de la réplication d’un ordinateur.
