## Preliminaries

We will install NextEPC on a [Ubuntu 18.04](https://releases.ubuntu.com/18.04.5/ubuntu-18.04.5-desktop-amd64.iso) virtual machine. We choose Virtual-Box 6.1

```sh
sudo apt-get install virtualbox-6.1
```
Install the ubuntu image, then go to Settings > Network > Adapter 1

And change to Bridged Adapter and select your interface

![alt text](https://raw.githubusercontent.com/angelo-ath/NextEPC-oaiRAN/gh-pages/screenshots/1.png)
![alt text](https://raw.githubusercontent.com/angelo-ath/NextEPC-oaiRAN/gh-pages/screenshots/2.png)
![alt text](https://raw.githubusercontent.com/angelo-ath/NextEPC-oaiRAN/gh-pages/screenshots/3.png)

## Installation of NextEPC

For the installation procedure we follow the instructions from [nextepc.org](https://nextepc.org/installation/02-ubuntu/)

- Install NextEPC

```sh
sudo apt-get update
sudo apt-get -y install software-properties-common
sudo add-apt-repository ppa:nextepc/nextepc
sudo apt-get -y install nextepc
```

- Install Web User Interface

```sh
sudo apt-get -y install curl
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
curl -sL https://nextepc.org/static/webui/install | sudo -E bash -
```

- Verify Installation & the tunnel interface creation

```sh
clear ;sudo systemctl status nextepc-mmed ; sudo systemctl status nextepc-pgwd ; sudo systemctl status nextepc-sgwd ; sudo systemctl status nextepc-hssd ; sudo systemctl status nextepc-pcrfd
ifconfig pgwtun
```

## Configuration of NextEPC

```sh
cd /etc/nextepc
sudo gedit mme.conf sgw.conf
```

### Configure MME

```yaml
mme:
    freeDiameter: mme.conf
    s1ap:
      dev: enp0s3
    gtpc:
    gummei: 
      plmn_id:
        mcc: 208
        mnc: 93
      mme_gid: 2
      mme_code: 1
    tai:
      plmn_id:
        mcc: 208
        mnc: 93
      tac: 1
    security:
        integrity_order : [ EIA1, EIA2, EIA0 ]
        ciphering_order : [ EEA0, EEA1, EEA2 ]
    network_name:
        full: NextEPC
```

### Configure SGW

```yaml
pgw:
    freeDiameter: pgw.conf
    gtpc:
      addr:
        - 127.0.0.3
        - ::1
    gtpu:
      dev: enp0s3
    ue_pool:
      - addr: 45.45.0.1/16
      - addr: cafe::1/64
    dns:
      - 8.8.8.8
      - 8.8.4.4
      - 2001:4860:4860::8888
      - 2001:4860:4860::8844
```

## Installation of oai-RAN

Install oai-RAN on the physical machine, you can follow the instructions for the installation from the link below:

[oai-Ran-Installation-Configuration](https://angelo-ath.github.io/oai/#enb---installation---configuration)

## Configuration of oai-RAN

```yaml

tracking_area_code  =  1;

plmn_list = ( { mcc = 208; mnc = 93; mnc_length = 2; } );

downlink_frequency = 2625000000L;

N_RB_DL = 25;

tx_gain = 90;
rx_gain = 115;

mme_ip_address= ( { ipv4 = "192.168.30.17"; # IP Address of VM 

ENB_INTERFACE_NAME_FOR_S1_MME            = "eno1";
        ENB_IPV4_ADDRESS_FOR_S1_MME              = "192.168.30.6/24";
        ENB_INTERFACE_NAME_FOR_S1U               = "eno1";
        ENB_IPV4_ADDRESS_FOR_S1U                 = "192.168.30.6/24";

parallel_config    = "PARALLEL_SINGLE_THREAD";
```

![alt text](https://raw.githubusercontent.com/angelo-ath/NextEPC-oaiRAN/gh-pages/screenshots/4.png)
