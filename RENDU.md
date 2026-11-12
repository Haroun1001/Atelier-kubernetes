# Rendu — Atelier Kubernetes

**Nom / Prénom :** Boukedjar Haroune
**Groupe :** RS2

---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   > 3 nœuds : `k3d-lab-server-0` est le control-plane (le master, il décide où tourne chaque pod), `k3d-lab-agent-0` et `k3d-lab-agent-1` sont les workers qui font tourner les applis.
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   > `docker ps` montre 4 conteneurs : server-0, agent-0, agent-1 et un load balancer (`serverlb`). Dans k3d, un nœud est un conteneur Docker qui fait tourner k3s.

## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   > Un Deployment, qui crée un ReplicaSet, qui crée un Pod.
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   > Un nouveau pod avec un autre nom est apparu en 2 secondes. Le ReplicaSet voit qu'il manque un pod par rapport au nombre voulu (1) et en recrée un.

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   > Non, il a disparu. Le système de fichiers d'un conteneur est éphémère : le nouveau pod repart de l'image nginx d'origine.
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   > Le `port-forward` est attaché à un pod précis. Quand ce pod est supprimé, le tunnel est coupé, même si un nouveau pod existe.

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   > `create` est impératif : il crée l'objet et échoue s'il existe déjà. `apply` est déclaratif : il crée ou met à jour l'objet pour qu'il corresponde au fichier YAML.
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   > Le `selector` désigne les pods (par leurs labels) qui reçoivent le trafic. S'il ne correspond à aucun pod, le Service n'a aucun endpoint et rien ne répond.

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   ```
   NAME                    READY   STATUS    RESTARTS   AGE   IP          NODE               NOMINATED NODE   READINESS GATES
site-5cc664db9c-mdprm   1/1     Running   0          24m   10.42.1.6   k3d-lab-agent-1    <none>           <none>
site-5cc664db9c-pmnrq   1/1     Running   0          24m   10.42.2.6   k3d-lab-server-0   <none>           <none>
site-5cc664db9c-ztgkg   1/1     Running   0          25m   10.42.0.9   k3d-lab-agent-0    <none>           <none>
   ```
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    > Le pod était en `ErrImagePull` puis `ImagePullBackOff`. `kubectl get events` m'a donné la cause : `Failed to pull image "nginx:9.99": not found`.
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    > Oui. Pendant le rolling update, le nouveau pod n'est jamais devenu prêt, donc Kubernetes a gardé les 3 anciens pods en `Running` et le trafic continuait d'aller vers eux.

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    > https://animated-space-computing-wrq74vx79jqjhgxjj-8585.app.github.dev
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
    > `kubectl get deploy mario` le retrouve. Oui, les deux parlent au même composant : l'API server de Kubernetes, sur le control-plane (Portainer l'appelle via `kubernetes.default.svc`).

## Bonus (facultatif)

14. Bonus réalisés et remarques :
    > Aucun bonus réalisé.

---

**Score obtenu avec `./scripts/check.sh` :** 15 / 18 ( C'est les bonus qu'il manque)
