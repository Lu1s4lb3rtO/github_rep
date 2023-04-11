# github_rep
curl -sfL https://get.k3s.io | sh -s - --disable traefik --write-kubeconfig-mode 644 --node-name k3s-master-01

Rancher's k3s - Primeros pasos
Distribución de Kubernetes liviana, fácil y rápida con una huella muy pequeña
https://k3s.io o https://k3d.io para la versión dockerizada de k3s.

Manifestación
Usaré Linode para esa demostración.

Instalar maestro
curl -sfL https://get.k3s.io | sh -s - --disable traefik --write-kubeconfig-mode 644 --node-name k3s-master-01

Instalar trabajador
Tome el token del nodo maestro para poder agregarle nodos trabajados:
cat /var/lib/rancher/k3s/server/node-token
Instale k3s en el nodo trabajador y agréguelo a nuestro clúster:
curl -sfL https://get.k3s.io | K3S_NODE_NAME=k3s-worker-01 K3S_URL=https://<IP>:6443 K3S_TOKEN=<TOKEN> sh - 

Instalar el controlador de entrada nginx
Sitio web: https://kubernetes.github.io/ingress-nginx/
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.1/deploy/static/provider/cloud/deploy.yaml

Nota importante para AWS, Azure, GCP
No puedo explicarlo en detalle, pero si ejecuta una máquina virtual en AWS, Azure o GCP, el "ingreso-nginx" solo seleccionará la IP privada como IP externa. Debe editar manualmente el servicio para que su dirección IP pública esté disponible para nginx, de modo que pueda comenzar a escuchar en su dirección IP pública.
kubectl -n ingress-nginx edit svc ingress-nginx-controller
Agregue su IP como lista para que spec.externalIPsle guste:

  spec:
    externalIPs:
    - <YOUR_IP>



Información adicional
Puede cambiar la configuración de k3s cambiando la configuración del servicio, por ejemplo, con nano /etc/systemd/system/k3s.service.
Asegúrese de reiniciar el servicio después:systemctl restart k3s
En muchos casos, puede volver a ejecutar el instalador con diferentes variables y configurará su clúster en consecuencia sin eliminarlo en primer lugar.

Implementar aplicación de demostración
kubectl apply -f manifest.yaml
