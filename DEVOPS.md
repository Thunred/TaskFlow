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

Nous avons choisi cette politique de redémarrage car c'est le choix le plus pertinent parmis les 3 étant donné que les autres n'ont pas vraiment de sens, car si nous choisissons always le serveur redémarrera toujours lorsque nous essayerons de l'arrêter. Et nous n'avons pas choisi le on failure car le code qui peut être envoyé lors d'un arrêt pourrait être interprété comme un crash. 

Dans chacun des cas, si le serveur plante à 3h du matin le serveur redémarrera. 