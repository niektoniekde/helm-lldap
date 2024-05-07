# helm-lldap
Simple Helm chart for ``ldap`` deployment to Kubernetes cluster.

Web interface is available via Ingress. Port for LDAPS is available via service - LoadBalancer type by default.
Non-encrypted LDAP port isn't even exposed from container. 

## Installation
This is for example only. Your use-case may differ. 
````bash
git clone https://github.com/niektoniekde/helm-lldap
cp helm-lldap/values.yaml ./values.custom.yaml
# customize your /tmp/values.yaml for your environment
helm install lldap helm-lldap --values ./values.custom.yaml
````

### example values.custom.yaml
````yaml
name: lldap
envVars:
  - name: "TZ"
    value: "Europe/Berlin"
  - name:  "LLDAP_LDAP_BASE_DN"
    value: "dc=sub,dc=example,dc=com"

pvc:
  data:
    name: "pvc-lldap-data"
    specs:
      volumeName: "iscsi-tgt01-lun1"
      storageClassName: ""
      accessModes:
        - "ReadWriteOnce"
      resources:
        requests:
          storage: 1Gi

service:
  ldaps:
    externalIPs:
      - "10.100.100.100"

ingress:
  enabled: true
  className: "nginx"
  annotations: {}
  hosts:
    - host: lldap.example.com
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: web 
              port:
                number: 80
  tls:
    - secretName: secret-tls
      hosts:
      - lldap.example.com
````
