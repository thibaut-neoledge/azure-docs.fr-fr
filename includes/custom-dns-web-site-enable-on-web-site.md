Une fois la propagation des enregistrements de votre nom de domaine terminée, vous devez associer ces enregistrements à votre application web. Procédez comme suit pour activer les noms de domaine à l'aide de votre navigateur Internet.

> [AZURE.NOTE] La propagation dans le système DNS de vos enregistrements TXT créés aux étapes précédentes peut prendre du temps. Vous ne pouvez pas ajouter le nom de domaine de votre application web avant la propagation des enregistrements TXT. Si vous utilisez un enregistrement A, vous ne pouvez pas ajouter le nom de domaine de l’enregistrement A à votre application web avant la propagation de l’enregistrement TXT créé à l’étape précédente.
>
> Vous pouvez utiliser un service tel que <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> pour vérifier que l’enregistrement TXT est disponible.

1. Dans votre navigateur, ouvrez le [portail Azure](https://portal.azure.com).

2. Sous l’onglet **Web Apps**, cliquez sur le nom de votre application web, puis sélectionnez **Domaines personnalisés**.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Dans le panneau **Domaines personnalisés**, cliquez sur **Ajouter un nom d’hôte**.
	
4. Utilisez les zones de texte **Nom d’hôte** pour entrer les noms de domaine à associer à cette application web.

	![](./media/custom-dns-web-site/add-custom-domain.png)

6.  Cliquez sur **Valider**.

7.  Lorsque vous cliquez sur **Valider**, Azure lance le processus de vérification du domaine. Ce processus vérifie la propriété du domaine ainsi que la disponibilité du nom du domaine, signale la réussite ou le détail de l’échec de l’opération, et fournit des instructions de résolution des erreurs.

À ce stade, vous devez pouvoir entrer le nom de domaine personnalisé dans votre navigateur et vérifier que vous êtes bien dirigé vers votre application web.

<!---HONumber=AcomDC_0824_2016-->