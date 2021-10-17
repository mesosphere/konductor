## Install Kommander
​
Download DKP
​
- Linux:
​
```
wget https://downloads.mesosphere.io/konvoy/dkp_v0.0.0-agent.1_linux_amd64.tar.gz && tar -xzvf dkp_v0.0.0-agent.1_linux_amd64.tar.gz
```
​
- Darwin:
​
```
wget https://downloads.mesosphere.io/konvoy/dkp_v0.0.0-agent.1_darwin_amd64.tar.gz && tar -xzvf dkp_v0.0.0-agent.1_darwin_amd64.tar.gz
```
​
1. Create a Kubernetes cluster for Kommander:
​
```
export CLUSTER_NAME=kommander-cluster
./dkp create bootstrap
./dkp create cluster aws --cluster-name=${CLUSTER_NAME}
```
​
2. Wait for the cluster to be complete
​
```
./dkp describe cluster -c $CLUSTER_NAME
```
​
3. Get the Kubeconfig for the cluster
​
```
./dkp get kubeconfig --cluster-name=$CLUSTER_NAME > $CLUSTER_NAME.conf
```
​
4. Deploy CAPI controllers on the Kommander cluster
​
```
export KUBECONFIG=$CLUSTER_NAME.conf
./dkp create bootstrap controllers --with-aws-bootstrap-credentials=false
```
​
5. Install Kommander:
​
```
wget https://dkp-cappp-agent.s3.us-west-2.amazonaws.com/kommander.sh && chmod +x kommander.sh
export KUBECONFIG=$CLUSTER_NAME.conf
./kommander.sh
# wait for it to complete
```
​
## Create a workload cluster
​
1. Install the kube-tunnel configuration neeeded to connect to the workload clusters
​
```
wget https://dkp-cappp-agent.s3.us-west-2.amazonaws.com/ktunnel.sh && chmod +x ktunnel.sh
export CLUSTER_NAME=workload-cluster-1
./ktunnel.sh
```
​
2. Create a workload cluster
​
```
wget https://dkp-cappp-agent.s3.us-west-2.amazonaws.com/cluster.sh && chmod +x cluster.sh
export CLUSTER_NAME=workload-cluster-1
export TUNNEL_ADDRESS=$(kubectl get svc -n dkp-tunnel -l kubetunnel.d2iq.io/tunnel-connector=$CLUSTER_NAME,kubetunnel.d2iq.io/service-type=tunnel-server -o custom-columns=NAME:.metadata.name --no-headers)
export INTERNAL_API_SERVER_ADDRESS=<set this to the IP of control-plane[0]>
./cluster.sh
```
​
3. Download the agent:
​
```
wget https://dkp-cappp-agent.s3.us-west-2.amazonaws.com/agent && chmod +x agent
```
​
4. Get the contents of `KUBECONFIG` and the `agent` onto the remote machines.
​
5. On control-plane[0] run the agent:
​
```
./agent -k8sconfig=kommander-cluster.conf -role=control -cluster-name=workload-cluster-1 -machine-id=<some unique ID>
```
​
6. On each worker machine (TODO)
​
7. Shutdown the agent on control-plane[0]. A deployment with the tunnel will be already be running.
