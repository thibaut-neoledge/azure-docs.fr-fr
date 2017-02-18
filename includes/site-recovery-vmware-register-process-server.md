1. Connectez-vous à la machine virtuelle du serveur de traitement à l’aide de la connexion Bureau à distance.
2. Une fois la connexion établie, l’outil de configuration du serveur de traitement est démarré automatiquement et exige que vous entriez les informations suivantes
  * Nom de domaine complet ou adresse IP du serveur de configuration
  * Port sur lequel le serveur de configuration écoute. La valeur doit être 443
  * Phrase secrète de connexion pour se connecter au serveur de configuration.
  * Port de transfert des données à configurer pour ce serveur de traitement. Conservez la valeur par défaut, sauf si vous avez opté pour un numéro de port différent dans votre environnement.

    ![Enregistrer le serveur de traitement](./media/site-recovery-vmware-register-process-server/register-ps.png)
3. Cliquez sur le bouton d’enregistrement pour enregistrer la configuration.
4. Une fois l’enregistrement terminé, le serveur de traitement commence à apparaître sous le serveur de configuration et peut être utilisé pour la restauration automatique.

> [!TIP]
> L’utilitaire de configuration du serveur de traitement peut être démarré en double-cliquant sur le raccourci **cspsconfigtool** disponible sur le bureau de la machine virtuelle.


<!--HONumber=Feb17_HO1-->


