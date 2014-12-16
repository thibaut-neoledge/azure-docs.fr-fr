
1. Si le service mobile est en cours d'exécution dans IIS Express, arrêtez-le. Cliquez avec le bouton droit sur l'icône de barre d'état système d'IIS Express, puis cliquez sur **stop** pour arrêter le service mobile.

    ![](./media/mobile-services-how-to-configure-iis-express/iis-express-tray-stop-site.png)


2. Dans la fenêtre d'invite de commandes, exécutez la commande **ipconfig** pour rechercher une adresse IP locale valide pour votre station de travail.

    ![](./media/mobile-services-how-to-configure-iis-express/ipconfig.png)


3. Dans Visual Studio, ouvrez le fichier applicationhost.config d'IIS Express. Ce fichier se trouve dans le sous-répertoire suivant du répertoire de votre profil utilisateur.

        C:\Users\<your profile name>\Documents\IISExpress\config\applicationhost.config

4. Configurez IIS Express pour autoriser les demandes de connexion à distance au service. Pour ce faire, dans le fichier applicationhost.config, recherchez l'élément de site de votre service mobile et ajoutez un nouvel élément binding pour le port qui utilise l'adresse IP que vous avez précédemment notée. Enregistrez ensuite le fichier applicationhost.config. 

    Votre élément de site doit être semblable à ce qui suit :

        <site name="todolist_Service(1)" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="C:\Archive\GetStartedDataWP8\C#\todolist_Service" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:58203:localhost" />
                <binding protocol="http" bindingInformation="*:58203:192.168.137.72" />
            </bindings>
        </site>

5. Ouvrez la console du Pare-feu Windows et créez une règle de port pour autoriser les connexions au port. Pour plus d'informations sur la création d'une règle de port via le Pare-feu Windows, consultez la rubrique [Ajout d'une nouvelle règle de port via le Pare-feu Windows].

    >[WACOM.NOTE] Si votre ordinateur de test est associé à un domaine, les exceptions de pare-feu peuvent être contrôlées par une stratégie de domaine. Dans ce cas, vous devez contacter votre administrateur de domaine pour bénéficier d'une exemption de port sur votre ordinateur.

    Votre configuration doit maintenant être prête pour procéder à un test avec l'instance d'IIS Express qui héberge votre service mobile. 

    >[WACOM.NOTE] Une fois le service testé localement, vous devez supprimer la règle de Pare-feu Windows que vous avez créée. 


<!-- URLs. -->
[Ajout d'une nouvelle règle de port via le Pare-feu Windows]:  http://go.microsoft.com/fwlink/?LinkId=392240
