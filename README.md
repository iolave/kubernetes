### First master node
```
mkdir -p /root/.kube
wget -O /root/.kube/kubeadm-config.yaml https://raw.githubusercontent.com/iolave/k8s-cluster-onprem/main/config/k8s-master-1.yaml 
kubeadm init --config=/root/.kube/kubeadm-config.yaml
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://docs.projectcalico.org/v3.23/manifests/calico.yaml

kubeadm token create --print-join-command
```

### Second master node
```
mkdir -p /root/.kube
wget -O /root/.kube/kubeadm-config.yaml https://raw.githubusercontent.com/iolave/k8s-cluster-onprem/main/config/k8s-master-2.yaml 


sed -i 's/{{TOKEN}}/[PLACE_YOUR_TOKEN_HERE]/g' /root/.kube/kubeadm-config.yaml
sed -i 's/{{HASH}}/[PLACE_YOUR_HASH_HERE]/g' /root/.kube/kubeadm-config.yaml
sed -i 's/{{MASTER_API_SERVER}}/[PLACE_YOUR_INITITAL_APISERVER_HERE]/g' /root/.kube/kubeadm-config.yaml


scp -r root@192.168.0.102:/etc/kubernetes/pki /etc/kubernetes/

# REPLACE TOKEN AND HASH
kubeadm join 192.168.0.102:6443 --config=/root/.kube/kubeadm-config.yaml

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```