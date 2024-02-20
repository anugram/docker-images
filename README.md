# Running Docker Image

## Quickstart
### 1) Deploy Ciphertrust Manager (CM) community edition (Always Free)
To deploy Ciphertrust Manager, follow the link https://ciphertrust.io/ 
You can deploy Ciphertrust Manager in you favorite cloud environment or on local server/virtual machine
Detailed steps are available on CM web page above.
### 2) Clone the repo 
Clone this repo on your Windows workstation
```
git clone https://github.com/anugram/docker-images
cd ./docker-images/ansible-provisioner/unified-demo-image
```
### 3) Build Docker Image and run the same
```
docker build -t demo .

docker run --detach --privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:rw --volume=/home/aj/.kube:/root/.kube  --cgroupns=host demo

docker exec --tty <CONTAINER_ID> env TERM=xterm ansible-playbook /root/run_demo.yml -e "CM_IP=<CM_IP>" -e "CM_USERNAME=<CM_USERNAME>" -e "CM_PASSWORD=<CM_PWD>" -e "LOCAL_CA_ID=<CA_ID>" -v
```