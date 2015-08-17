

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/) et cliquez sur **+NOUVEAU** en bas de l’écran.

2. Cliquez sur **Services d'application**, puis sur **Service Bus**, **Concentrateur de notification**, **Création rapide**.

3. Tapez un nom pour votre concentrateur de notification, sélectionnez la région souhaitée, puis cliquez sur **Créer un concentrateur de notification**.

   	![Définition des propriétés du concentrateur de notification](./media/notification-hubs-android-configure-push/notification-hub-create-from-portal2.png)

4. Sous **Service Bus**, cliquez sur l’espace de noms que vous venez de créer (en général ***nom du concentrateur de notification*-ns**).

5. Dans votre espace de noms, cliquez sur l’onglet **Concentrateurs de notification** en haut de la page, puis cliquez sur le concentrateur que vous venez de créer.

6. Cliquez sur l’onglet **Configurer** dans la partie supérieure, saisissez la valeur de **clé d’API** obtenue à la section précédente, puis cliquez sur **Enregistrer**.

   	![Définition de la clé API GCM sous l’onglet Configurer](./media/notification-hubs-android-configure-push/notification-hub-configure-android.png)

7. Sélectionnez l’onglet **Tableau de bord** en haut, puis cliquez sur **Afficher la chaîne de connexion**. Notez les deux chaînes de connexion.

Votre concentrateur de notification est à présent configuré pour GCM, et vous disposez des chaînes de connexion vous permettant d’inscrire votre application pour la réception de notifications et l’envoi de notifications Push.

<!---HONumber=August15_HO6-->