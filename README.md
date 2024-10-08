
set tabstop=2

set expandtab

set shiftwidth=2

alias k=kubectl

export do="-o yaml --dry-run=client"

## Upgrade
apt update

apt show kubectl -a | grep 1.30

apt install kubectl=1.30.1-1.1 kubelet=1.30.1-1.1

service kubelet restart

kubeadm token create --print-join-command

kubeadm join....

service kubelet status

---------------

k api-resources --namespaced -o name

k -n secret create secret generic secret2 --from-literal=user=user1 --from-literal=pass=1234

kubectl create deployment nginx --image=nginx --replicas=3 --dry-run=client -o yaml > nginx-deployment.yaml

kubectl run nginx --image=nginx --dry-run=client -o yaml


Taint: kubectl taint nodes <nodename> key=value:effect

Untaint: kubectl taint nodes <nodename> key=value:effect-

k get pods --selector=app=nginx

k label nodes node01 tier=large

k -n project-c13 get pod \
  -o jsonpath="{range .items[*]} {.metadata.name}{.spec.containers[*].resources}{'\n'}"

k get pods -n project-c13 \
  -o jsonpath="{range .items[*]}{.metadata.name} {.status.qosClass}{'\n'}"

k get nodes -o=jsonpath='{.items[*].metadata.name}{"\n"}{.items[*].status.capacity.cpu}'

k get nodes -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.capacity.cpu}{"\n"}{end}'

OR

k get nodes -o=custom-columns=NODE:.metadata.name,CPU:.status.capacity.cpu

k get nodes --sort-by=.status.capacity.cpu

k get pv --sort-by=.spec.capacity.storage -o=custom-columns=NAME:metadata.name,CAPACITY:.spec.capacity.storage

kubectl config view --kubeconfig=/root/my-kube-config -o=jsonpath='{.users[*].name}'

kubectl config view --kubeconfig=my-kube-config -o jsonpath="{.contexts[?(@.context.user=='aws-user')].name}"


journalctl -u containerd

journalctl -u kubelet -xn -f

(/var/lib/kubelet/config.yaml, /etc/kubernetes/kubelet.conf)

crictl ps -a

crictl logs <containerid>

echo "" | base64 -w 0


-- IP commands
ip link

ip addr

ip addr add 192.168.1.10/24 dev eth0

ip route

ip route add 192.168.1.0/24 via 192.168.2.1

cat /proc/sys/net/ipv4/ip_forward

ip address show type bridge

iptables-save 


-- certificate validation
openssl x509  -noout -text -in /etc/kubernetes/pki/apiserver.crt

openssl x509  -noout -text -in /etc/kubernetes/pki/etcd/server.crt | grep Validity -A2


kubeadm certs check-expiration

kubeadm certs renew apiserver

export ETCDCTL_API=3

etcdctl snapshot save --endpoints https://[127.0.0.1]:2379 --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key  /opt/etcd-backup.db


ETCDCTL_API=3 etcdctl snapshot save /tmp/etcd-backup.db \
--cacert /etc/kubernetes/pki/etcd/ca.crt \
--cert /etc/kubernetes/pki/etcd/server.crt \
--key /etc/kubernetes/pki/etcd/server.key


ETCDCTL_API=3 etcdctl snapshot restore /tmp/etcd-backup.db \
--data-dir /var/lib/etcd-backup \
--cacert /etc/kubernetes/pki/etcd/ca.crt \
--cert /etc/kubernetes/pki/etcd/server.crt \
--key /etc/kubernetes/pki/etcd/server.key


-- K8s api commands

TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)

curl -k https://kubernetes.default/api/v1/secrets -H "Authorization: Bearer ${TOKEN}"

CACERT=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt

curl --cacert ${CACERT} https://kubernetes.default/api/v1/secrets -H "Authorization: Bearer ${TOKEN}"

auth can-i get secret --as system:serviceaccount:<ns>:<saname>


-- Service CIDR
check in kube-apiserver.yaml and kube-controller-manager.yaml

