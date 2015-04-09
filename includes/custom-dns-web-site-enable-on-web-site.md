Une fois la propagation des enregistrements de votre nom de domaine terminée, vous devez associer ces enregistrements à votre application web. Procédez comme suit pour activer les noms de domaine à l'aide de votre navigateur Internet.

> [AZURE.NOTE] La propagation de vos enregistrements CNAME, créés à l'étape précédente, dans le système DNS peut prendre du temps. Vous ne pouvez pas ajouter le nom de domaine de votre application web avant la propagation de ces enregistrements. Si vous utilisez un enregistrement A, vous ne pouvez pas ajouter le nom de domaine de l'enregistrement A à votre application web avant la propagation de l'enregistrement CNAME **awverify** créé à l'étape précédente.
> 
> Vous pouvez utiliser un service tel que <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> pour vérifier que l'enregistrement CNAME est disponible.

1. Dans votre navigateur, ouvrez le [portail Azure](https://portal.azure.com).

2. Dans l'onglet **Web Apps**, cliquez sur le nom de votre application web, puis sélectionnez **Paramètres** et **Domaines personnalisés et SSL**.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

6. Utilisez les zones de texte **NOMS DE DOMAINE** pour entrer les noms de domaine à associer à cette application web. 

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

6. Cliquez sur la coche dans le coin inférieur droit pour enregistrer la configuration de nom de domaine.

	Une fois la configuration effectuée, le nom de domaine personnalisé est répertorié dans la section **Noms de domaine** du panneau **Domaines personnalisés et SSL** de votre application web.

À ce stade, vous devez pouvoir entrer le nom de domaine personnalisé dans votre navigateur et vérifier que vous êtes bien dirigé vers votre application web. 

<!--HONumber=49-->