Vous pouvez utiliser un abonnement Azure dont vous n’êtes pas l’administrateur ou le propriétaire, par exemple un abonnement appartenant à l’entreprise. Si c’est le cas, vous devez vérifier que vous disposez bien des éléments suivants pour suivre les étapes décrites dans cet article :

* Accès Collaborateur. Pour vous connecter à Azure, vous devez disposer d’au moins un accès Collaborateur au groupe de ressources Azure. Ce groupe de ressources est utilisé pour créer un cluster Azure HDInsight et d’autres ressources Azure.
* Inscription du fournisseur. Une personne disposant d’au moins un accès Collaborateur à l’abonnement Azure doit avoir auparavant enregistré le fournisseur pour la ressource que vous utilisez. L’enregistrement du fournisseur se produit lorsqu’un utilisateur disposant d’un accès Contributeur à l’abonnement crée une ressource pour la première fois sur l’abonnement. Vous pouvez également réaliser cette opération sans créer de ressource, en [inscrivant un fournisseur à l’aide de REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Pour plus d’informations sur le fonctionnement de la gestion des accès, consultez les articles suivants :

* [Prise en main de la gestion des accès dans le portail Azure](../articles/active-directory/role-based-access-control-what-is.md)
* [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../articles/active-directory/role-based-access-control-configure.md)
