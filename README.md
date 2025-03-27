# IETF 122 Hackathon

cd bgp-ls-service-segment
docker build . -t ubuntu/watal
sudo containerlab deploy -t bgp-ls-service-segment-gobgp-only.clab.yml


## GoBGP Sender 
```
docker exec -it clab-bgp-ls-service-segment-gobgp-only-gobgp1 bash
gobgpd -f config/gobgpd.yml
gobgp global rib add -a ls srv6sid bgp identifier 1 local-asn 65001 local-bgp-ls-id 10 local-bgp-router-id 10.0.0.1 local-bgp-confederation-member 1 sids fc00:0:1::2 multi-topology-id 1 service-type 1 traffic-type 1 opaque-type 1 value test
```


## GoBGP Receiver 

```
docker exec -it clab-bgp-ls-service-segment-gobgp-only-gobgp2 bash
gobgpd -f config/gobgpd.yml
gobgp global rib -a ls
```

```
root@gobgp2:/# gobgp global rib -a ls
   Network                                                                                                                                                                                                                                                                                   Next Hop             AS_PATH              Age        Attrs
*> NLRI { SRv6SID { LOCAL_NODE: {ASN: 65001, BGP LS ID: 10, BGP ROUTER ID: 10.0.0.1} SRv6_SID: {SIDs: fc00:zero:1::2} MULTI_TOPO_IDs: {MultiTopoIDs: 1}, SERVICE_CHAINING: {ServiceType: 1, Flags: 0, TrafficType: 1}, OPAQUE_METADATA: {OpaqueType: 1, Flags: 0, Value: [116 101 115 116]
}} } 172.100.100.101                           00:00:15   [{Origin: ?} {LocalPref: 100}]
root@exabgp:/#
```

## ExaBGP Sender

```
docker exec -it clab-bgp-ls-service-segment-exabgp bash
cd exabgp/
sbin/exabgp ../config/conf-srv6-bgpls-receiver.conf
```

## ExaBGP Receiver
```
docker exec -it clab-bgp-ls-service-segment-exabgp bash
sbin/exabgp ../config/conf-srv6-bgpls-sender.conf
^c
cat bgpls-receiver.log # ログ確認
```
