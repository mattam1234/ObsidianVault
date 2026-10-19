# Kubernetes DNS

## Custom DNS Settings

### Edit coredns config map

Add entry to the `Corefile: |` section of the `configmap/coredns` in section **kube-system**.

```yml
.:53 {
  # ...
}
import /etc/coredns/custom/*.server
```

### Add new config map

Example for local DNS server using the **home.arpa** zone (RFC 8375 reserves
`home.arpa` for home networks — substitute your own zone and resolver IP).

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  home-arpa.server: |
    home.arpa:53 {
      forward . 192.0.2.53
    }
```