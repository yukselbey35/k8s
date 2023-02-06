Steps to remove and clean worker nodes from a kubernetes cluster:

Drain the node: Before removing a node, it is important to drain it, which means moving all pods running on the node to other nodes in the cluster. You can use the command kubectl drain <node_name>.

Stop and reset the kubelet service: The kubelet service is responsible for running containers on the node. To reset it, you can use the command systemctl stop kubelet and then systemctl reset-failed kubelet.

Remove etcd data: The etcd data store holds the cluster state. To remove etcd data, stop the etcd service using the command systemctl stop etcd. Then, delete the data directory, typically located at /var/lib/etcd.

Remove network configuration: The network configuration files are stored in the /etc/cni directory. You can delete the entire directory to remove the network configuration.

Remove packages: If there are any additional packages installed on the node, such as filebeat, you can remove them using the command apt-get remove filebeat.

Reset the node: After removing all the configurations and packages, you can reset the node to its original state. You can use the command sudo reboot to reset the node.
