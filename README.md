# IETF 122 Hackathon

## Setup

1. Clone the repository with submodules:

    ```bash
    git clone --recurse-submodules https://github.com/watal/ietf122_hackathon
    cd ietf122_hackathon/gobgp/cmd/gobgp
    ```

2. Build GoBGP:

    ```bash
    go build .
    cd ../gobgpd
    go build .
    ```

3. Build the Docker image:

    ```bash
    cd ../../../bgp-ls-service-segment
    docker build . -t ubuntu:watal
    ```

4. Deploy the network using [containerlab]:

    ```bash
    sudo containerlab deploy -t bgp-ls-service-segment-gobgp-only.clab.yml
    ```

## GoBGP (Sender Side)

1. Enter the GoBGP container:

    ```bash
    docker exec -it clab-bgp-ls-service-segment-gobgp-only-gobgp1 bash
    ```

2. Start the GoBGP daemon:

    ```bash
    gobgpd -f config/gobgpd.yml
    ```

3. Add a route:

    ```bash
    gobgp global rib add -a ls srv6sid bgp identifier 1 local-asn 65001 \
      local-bgp-ls-id 10 local-bgp-router-id 10.0.0.1 local-bgp-confederation-member 1 \
      sids fc00:0:1::2 multi-topology-id 1 service-type 1 traffic-type 1 \
      opaque-type 1 value test
    ```

## GoBGP (Receiver Side)

1. Enter the GoBGP container:

    ```bash
    docker exec -it clab-bgp-ls-service-segment-gobgp-only-gobgp2 bash
    ```

2. Start the GoBGP daemon:

    ```bash
    gobgpd -f config/gobgpd.yml
    ```

3. Display the BGP-LS information:

    ```bash
    gobgp global rib -a ls
    ```

## ExaBGP (Sender Side)

1. Enter the ExaBGP container:

    ```bash
    docker exec -it clab-bgp-ls-service-segment-exabgp bash
    ```

2. Start ExaBGP in sender mode:

    ```bash
    sbin/exabgp ../config/conf-srv6-bgpls-sender.conf
    ```

## ExaBGP (Receiver Side)

1. Enter the ExaBGP container:

    ```bash
    docker exec -it clab-bgp-ls-service-segment-exabgp bash
    ```

2. Navigate to the ExaBGP directory:

    ```bash
    cd exabgp/
    ```

3. Start ExaBGP in receiver mode:

    ```bash
    sbin/exabgp ../config/conf-srv6-bgpls-receiver.conf
    ```

4. To stop ExaBGP, press `Ctrl+C`.

5. Check the logs to verify:

    ```bash
    cat bgpls-receiver.log
    ```
