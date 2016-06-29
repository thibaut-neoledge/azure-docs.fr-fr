Votre modèle peut être un fichier local ou un fichier externe disponible par le biais d’un URI. Lorsque votre modèle se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle et fournir un jeton de signature d’accès partagé (SAP) au cours du déploiement.

## Déploiements incrémentiels et complets

Par défaut, Resource Manager gère les déploiements sous la forme de mises à jour incrémentielles du groupe de ressources. Dans le cadre d’un déploiement incrémentiel, Resource Manager :

- **conserve telles quelles** les ressources qui existent dans le groupe de ressources, mais qui ne sont pas spécifiées dans le modèle ;
- **ajoute** les ressources qui sont spécifiées dans le modèle, mais qui n’existent pas dans le groupe de ressources ; 
- **ne réapprovisionne pas** les ressources qui existent dans le groupe de ressources à l’état défini dans le modèle.

Dans le cadre d’un déploiement complet, Resource Manager :

- **supprime** les ressources qui existent dans le groupe de ressources, mais qui ne sont pas spécifiées dans le modèle ;
- **ajoute** les ressources qui sont spécifiées dans le modèle, mais qui n’existent pas dans le groupe de ressources ; 
- **ne réapprovisionne pas** les ressources qui existent dans le groupe de ressources à l’état défini dans le modèle.
 
Vous spécifiez le type de déploiement par le biais de la propriété **Mode**.

<!---HONumber=AcomDC_0615_2016-->