#### Installation de MPIO sur l’hôte

1. Ouvrez le Gestionnaire de serveur sur votre hôte Windows Server. Par défaut, le Gestionnaire de serveur démarre lorsqu’un membre du groupe Administrateurs ouvre une session sur un ordinateur qui exécute Windows Server 2012 R2 ou Windows Server 2012. Si le Gestionnaire de serveur n’est pas déjà ouvert, cliquez sur **Démarrer > Gestionnaire de serveur**.

	![Gestionnaire de serveur](./media/storsimple-install-mpio-windows-server/IC740997.png)

2. Cliquez sur **Gestionnaire de serveur > Tableau de bord > Ajouter des rôles et fonctionnalités**. Cette action démarre l’Assistant **Ajouter des rôles et fonctionnalités**.

	![Assistant Ajouter des rôles et fonctionnalités 1](./media/storsimple-install-mpio-windows-server/IC740998.png)

3. Dans l’Assistant **Ajouter des rôles et fonctionnalités**, procédez comme suit :

	- Sur la page **Avant de commencer**, cliquez sur **Suivant**.
	- Sur la page **Sélectionner le type d’installation**, acceptez le paramètre par défaut de l’installation **Installation basée sur un rôle ou sur une fonctionnalité**. Cliquez sur **Next**.
	
		![Assistant Ajouter des rôles et fonctionnalités 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
	- Sur la page **Sélectionner le serveur de destination**, choisissez **Sélectionner un serveur du pool de serveurs**. Votre serveur hôte doit être détecté automatiquement. Cliquez sur **Next**.
	- Sur la page **Sélectionner des rôles de serveurs**, cliquez sur **Suivant**.
	- Sur la page **Sélectionner des fonctionnalités**, sélectionnez **Multipath I/O** et cliquez sur **Suivant**.
	
		![Assistant Ajouter des rôles et fonctionnalités 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
	- Sur la page **Confirmer les sélections pour l’installation**, confirmez la sélection, puis sélectionnez **Redémarrer automatiquement le serveur de destination, si nécessaire** tel qu’indiqué ci-dessous. Cliquez sur **Installer**.
	
		![Assistant Ajouter des rôles et fonctionnalités 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
	- Vous recevrez une notification une fois l’installation terminée. Cliquez sur **Fermer** pour fermer l’assistant.
	
		![Assistant Ajouter des rôles et fonctionnalités 9](./media/storsimple-install-mpio-windows-server/IC741002.png)

<!---HONumber=AcomDC_0323_2016-->