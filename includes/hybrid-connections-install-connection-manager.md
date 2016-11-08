
Le Gestionnaire de connexions hybrides permet à votre ordinateur local de se connecter à Azure et de relayer le trafic TCP. Vous devez installer le gestionnaire sur un ordinateur local qui peut se connecter à votre instance SQL Server.

1. La connexion que vous venez de créer doit avoir l’**état** **Le programme d’installation local n’a pas terminé**. Cliquez sur cette connexion, puis sur **Programme d’installation local**.
   
    ![On-Premises Setup](./media/hybrid-connections-install-connection-manager/5-1.png)
2. Cliquez sur **Installer et configurer**.
   
    Cette option installe une instance personnalisée du Gestionnaire de connexions, qui est déjà préconfiguré pour utiliser la connexion hybride que vous venez de créer.
3. Effectuez les étapes restantes de l'installation du Gestionnaire de connexions.
   
    Au terme de l’installation, le statut de la connexion hybride passe à **1 instance connectée**. Il vous faudra peut-être actualiser le navigateur et patienter quelques minutes.

Le programme d’installation de la connexion hybride est maintenant terminé.

<!---HONumber=Oct15_HO3-->