
1. Dans le [portail Azure](https://azure.portal.com/), cliquez sur **Parcourir** > **App Services**, puis cliquez sur votre serveur principal d’applications mobiles > **Tous les paramètres**, puis sous **Mobile** cliquez sur **Push**.

2. Dans Services de notification Push, cliquez sur **Windows (WNS)**, entrez la **clé de sécurité** (clé secrète client) et le **SID du package** que vous avez obtenus à partir du site des services Live, puis cliquez sur **Enregistrer**.

    ![Définition de la clé API GCM dans le portail](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Votre serveur principal d’applications mobiles est maintenant configuré pour utiliser WNS pour envoyer des notifications Push à votre application Windows à l'aide de son concentrateur de notification.

<!---HONumber=AcomDC_1203_2015-->