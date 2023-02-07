Steps to remove and clean worker nodes from a kubernetes cluster:

Drain the node: Before removing a node, it is important to drain it, which means moving all pods running on the node to other nodes in the cluster. You can use the command kubectl drain <node_name>.

Stop and reset the kubelet service: The kubelet service is responsible for running containers on the node. To reset it, you can use the command systemctl stop kubelet and then systemctl reset-failed kubelet.

Remove etcd data: The etcd data store holds the cluster state. To remove etcd data, stop the etcd service using the command systemctl stop etcd. Then, delete the data directory, typically located at /var/lib/etcd.

Remove network configuration: The network configuration files are stored in the /etc/cni directory. You can delete the entire directory to remove the network configuration.

Remove packages: If there are any additional packages installed on the node, such as filebeat, you can remove them using the command apt-get remove filebeat.

Reset the node: After removing all the configurations and packages, you can reset the node to its original state. You can use the command sudo reboot to reset the node.

---
- name: Delete Worker Node from Kubernetes Cluster
  hosts: k8s-masters
  gather_facts: false
  tasks:
    - name: Drain worker node
      command: kubectl drain {{ worker_node_name }} --ignore-daemonsets
      register: drain_result

    - name: Check drain result
      fail:
        msg: "Failed to drain worker node {{ worker_node_name }}"
      when: drain_result.rc != 0

    - name: Delete worker node
      command: kubectl delete node {{ worker_node_name }}
      register: delete_result

    - name: Check delete result
      fail:
        msg: "Failed to delete worker node {{ worker_node_name }}"
      when: delete_result.rc != 0

    - name: Wait for worker node to disappear
      wait_for:
        timeout: 300
        state: absent
        host: "{{ worker_node_name }}"
      delegate_to: localhost

In this playbook, you need to specify the worker node name in the variable worker_node_name. You can pass this variable as an extra variable when running the playbook.

You also need to specify the host group k8s-masters in the hosts field, which should be the host group for the Kubernetes master nodes.

This playbook performs the following tasks:

Drain the worker node using the kubectl drain command.
Check the result of the drain operation and fail the task if the draining process fails.
Delete the worker node using the kubectl delete node command.
Check the result of the deletion operation and fail the task if the node deletion fails.
Wait for the worker node to disappear from the cluster using the wait_for module.
