Si vous utilisez un abonnement Azure alors que vous n’êtes pas administrateur/propriétaire, par exemple un abonnement détenu par l’entreprise, vous devez vérifier ce qui suit avant de procéder comme indiqué dans ce document :

* Votre connexion Azure doit avoir au moins un accès **Collaborateur** au groupe de ressources Azure que vous utilisez lors de la création de HDInsight (et d’autres ressources Azure.)
* Une personne disposant d’au moins un accès **Collaborateur** à l’abonnement Azure doit avoir auparavant enregistré le fournisseur pour la ressource que vous utilisez. L’enregistrement du fournisseur se produit lorsqu’un utilisateur disposant d’un accès Contributeur à l’abonnement crée une ressource pour la première fois sur l’abonnement. Vous pouvez également faire cela sans créer une ressource en [enregistrant un fournisseur à l’aide de REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Pour plus d’informations sur le fonctionnement de la gestion des accès, consultez les documents suivants :

* [Prise en main de la gestion des accès dans le portail Azure](../articles/active-directory/role-based-access-control-what-is.md)
* [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../articles/active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_1005_2016-->