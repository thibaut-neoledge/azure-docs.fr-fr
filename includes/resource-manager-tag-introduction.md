Vous appliquez des balises à vos ressources Azure pour les organiser de façon logique par catégories. Chaque balise se compose d’une clé et d’une valeur. Par exemple, vous pouvez appliquer la clé « Environnement » et la valeur « Production » à toutes les ressources en production. En l’absence de cette balise, il peut être difficile de savoir si une ressource est destinée au développement, aux tests ou à la production. « Environnement » et « Production » sont juste des exemples. Vous devez définir les clés et les valeurs les plus pertinentes pour organiser votre abonnement.

Après avoir appliqué une balise, vous pouvez utiliser sa clé et sa valeur pour récupérer toutes les ressources dans votre abonnement. Les balises vous permettent de récupérer les ressources associées qui se trouvent dans des groupes de ressources différents. Cette approche est utile lorsque vous devez organiser les ressources à des fins de facturation ou de gestion.

Les limites suivantes s’appliquent aux balises :

* Chaque ressource ou groupe de ressources peut inclure un maximum de 15 paires clé/valeur de balise. Cette limitation s’applique uniquement aux balises directement appliquées au groupe de ressources ou à la ressource. Un groupe de ressources peut contenir de nombreuses ressources qui ont chacune 15 paires clé/valeur de balise. 
* Le nom de la balise est limité à 512 caractères.
* La valeur de la balise est limitée à 256 caractères. 
* Les ressources d’un groupe de ressources n’héritent pas des balises appliquées à ce groupe de ressources. 

Si vous devez associer plus de 15 valeurs à une ressource, utilisez une chaîne JSON pour la valeur de balise. La chaîne JSON peut contenir plusieurs valeurs appliquées à une seule clé de balise. Un exemple d’affectation d’une chaîne JSON à la clé de balise est présenté dans cet article.