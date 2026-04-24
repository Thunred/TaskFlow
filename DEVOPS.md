## Taille de l'image

La taille de l'image est de 199.48MB, elle ne peut pas être plus diminué malgré le multi staging. 

---

## Choix 1 - Image de base du backend 

|Version de Node|Poids|Nombre de CVE|
|-|-|-|
|Node 20-alpine|223.73MB|14 CVE sur l'ensemble du projet|
|Node 18-alpine|211.46MB|64 CVE détecté sur l'ensemble du projet|
|Node 18|1.59GB| 4567 CVE détecté sur l'ensemble du projet|

Nous sommes donc parti sur la version Node 20-alpine, car malgré le fait que elle soit plus lourde que la version 18, elle a moins de vulnérabilité. Ce qui en fait un choix plus pertinent.

---

## Choix 2 - Politique de redémarrage

Concernant la politique de redémarrage, nous avons choisi "unless-stopped". 

Car le on-failure redémarre uniquement dans le cas ou le code d'arrêt est un code autre que le code 0, alors que le unless stopped redémarre tout le temps à moins qu'il soit arrêté manuellement. Cela nous permets d'avoir plus de contrôle sur notre projet. 

Dans les cas unless stopped et always, le serveur redémarrera toujours si le serveur plante. 

Alors que das le cas on-failure, si le code de sortie est 0, le serveur s'arrêtera même si c'est un crash, ce qui fais que nous avons moins de contrôle sur ces arrêts de serveurs inattendu là. 

--- 

## Choix 3 - Strategie de deploiement Kubernetes

Nous avons choisi une strategie de rolling update avec `maxUnavailable: 0` et `maxSurge: 1` pour les environnements staging et production.

Ce choix garantit qu'aucun pod existant n'est termine tant qu'un nouveau pod n'est pas pret, ce qui permet de limiter au maximum les interruptions de service. Avec `maxSurge: 1`, Kubernetes peut lancer temporairement un pod supplementaire pendant la mise a jour, puis remplacer progressivement les anciens pods une fois les nouveaux declarés Ready.

En staging, ce choix est pertinent car il permet de tester les deploiements dans des conditions proches de la production, tout en conservant un comportement stable et previsible. En production, il est encore plus adapte car il privilegie la disponibilite du service au lieu de la vitesse de deploiement.

L'inconvenient principal est une consommation temporaire de ressources un peu plus elevee pendant le rolling update, mais ce compromis est acceptable pour assurer un deploiement sans coupure.

A l'oral, nous pouvons le prouver avec la commande `kubectl rollout status`, qui montre que Kubernetes remplace les pods progressivement sans interrompre le service.

--- 

## Trivy

Trivy a trouvé de nombreuses failles de sécurités avec les différentes versions de Node, ces nombreuses failles de sécurités nous ont dirigé sur les choix de la version de Node que nous avons pris. Et donc suite à l'analyse de Trivy et les erreurs de sécurités comme indiqué au dessus, nous avons choisi Node 20-alpine malgré son poids qui est une différence insignifiante considérant le nombre de faille trouvé sur le projet avec les versions précédentes. 

--- 

## Difficulté rencontrée

L'une des difficultés que nous avons rencontré est survenu lors de la mise en place de la pipeline CI/CD car elle lançait le serveur.js étant donné que nous pointions vers un serveur redis qui n'existait pas nous étions bloqué dans le job de test de la pipeline. 