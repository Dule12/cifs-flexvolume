# Ansible Role: cifs-flexvolume

An Ansible Role that enables the CIFS volumes in your Kubernetes cluster. It is compatible with CentOS node(s).

## Role Variables

Available variables are listed below along with their default values:

```yaml
k8s_volume_plugins_directory: /usr/libexec/kubernetes/kubelet-plugins/volume/exec
fstab_cifs_directory: fstab~cifs
fstab_cifs_download_url: https://raw.githubusercontent.com/fstab/cifs/master/cifs
```

##  How to use this plugin?
First, you have to create a secret containing username and password to your cifs share:

```yaml

apiVersion: v1
kind: Secret
metadata:
  name: cifs-secret
  namespace: default
type: fstab/cifs
data:
  username: ... #base64 encoded username
  password: ... #base64 encoded password

```

then you need to add a volume in your deployment:

```yaml
apiVersion: v1
...
spec:
  containers:
  - name: SOMENAME
    ...
    volumeMounts:
    - name: test          
      mountPath: /data    
  volumes:
  - name: test
    flexVolume:
      driver: "fstab/cifs"
      fsType: "cifs"
      secretRef:
        name: "cifs-secret"   # reference to deployed secret
      options:
        networkPath: "//HOSTAME/SHARENAME"
        mountOptions: "dir_mode=0755,file_mode=0755,noperm"
```