### <a name="prepare-for-push-install-on-windows-machines"></a>Préparation de l’installation de transmission (push) sur des ordinateurs Windows

1. Vérifiez qu’il existe une connectivité réseau entre l’ordinateur Windows et le serveur de processus.
2. Créez un compte pouvant être utilisé par le serveur de traitement pour accéder à la machine. Le compte doit disposer de privilèges d’administrateur (local ou domaine) ; il est utilisé uniquement pour l’installation de transmission (push) et les mises à jour des agents.

   > [!NOTE]
   > Si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur distant sur la machine locale. Pour ce faire, accédez à un ordinateur, ajoutez un nouveau DWORD **LocalAccountTokenFilterPolicy** dans la clé de Registre HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, puis définissez cette valeur sur 1. Voici la commande permettant d’effectuer la même opération depuis une invite de commande : **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. Sur le pare-feu Windows de la machine à protéger, sélectionnez **Autoriser une application ou une fonctionnalité à franchir le pare-feu**. Activez **Partage de fichiers et d’imprimantes** et **Windows Management Instrumentation**. Pour les machines qui appartiennent à un domaine, vous pouvez configurer les paramètres de pare-feu avec un objet de stratégie de groupe.

   ![Paramètres du pare-feu](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. Ajoutez le compte que vous avez créé dans l’outil CSPSConfigtool.
    - Connectez-vous à votre serveur de configuration.
    - Ouvrez le fichier **cspsconfigtool.exe**. Il est disponible sous forme de raccourci sur le bureau et sous le dossier %ProgramData%\home\svsystems\bin.
    - Dans l’onglet **Gérer les comptes**, cliquez sur **Ajouter un compte**.
    - Ajoutez le compte que vous venez de créer. Après avoir ajouté le compte, vous devez fournir les informations d’identification lorsque vous activez la réplication d’une machine.


<!--HONumber=Jan17_HO3-->


