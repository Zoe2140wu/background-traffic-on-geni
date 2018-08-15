# Generating realistic background traffic on GENI


## Background
When researchers want to test a new video streaming algorithm or cyber-attack preventor, they often test their development in a testbeds, network simulators that recreates somewhat realistic network conditions while providing a level of control. While one of the key factors in creating a realistic environment is background traffic, researchers often ignore background traffic entirely or compromise the representation of real traffic for the ease of synthetic traffic generators. 

## Results

## Run my experiment


### Set up topology

In the GENI Portal, create a new slice and press "Add Resources." Scroll down to the "Choose RSpec" section, select the "URL" option, and load the RSpec from the following URL: [https://raw.githubusercontent.com/Zoe2140wu/background-traffic-on-geni/master/replaywrapperRspec.xml](https://raw.githubusercontent.com/Zoe2140wu/background-traffic-on-geni/master/replaywrapperRspec.xml)

This will load the following topology into your canvas:

TODO - include a picture here

TODO: explain the topology

Click on the "Site 1" button and bind this RSpec to an InstaGENI site. Note that the node used for replaying traffic should be a raw PC IG, of which limited quantities are available - if you aren't able to satisfy this request at the first aggregate you try, you may need to try others. 

Press "Reserve Resources" at the very bottom of the page and wait for your resources to be ready to log in. 

When your resources are ready, SSH into your `tcpreplay` node. We have reserved extra disk space on this node in order to have enough room to save large traffic capture files, but you'll need to set it up.

Run 

```
sudo /usr/testbed/bin/mkextrafs /mnt
sudo chmod a+w /mnt
```

to mount your extra disk space. Your extra disk space should be available in the `/mnt` directory.


We will also have to install some software on the `tcpreplay` node. Run

```
sudo apt-get update
sudo apt-get -y install tcpreplay tshark
```

(you can choose "yes" when prompted during the setup).

Next, get the `replaywrapper` script from GitHub. In your `tcpreplay` node, run

```
sudo wget -P /usr/local/bin https://raw.githubusercontent.com/Zoe2140wu/background-traffic-on-geni/master/replaywrapper
sudo chmod a+x /usr/local/bin/replaywrapper
```

If you run `which replaywrapper`, you should see that the `replaywrapper` is ready to run from `/usr/local/bin/replaywrapper`.

We also need to set up the router so that it will redirect the background traffic between the "local network" and the "Internet". Later in the experiment, when you replay the background traffic, you will select the IP address that should appear as the source and destination IP address in the packet headers. Since in our topology, the "Internet" uses the address range 10.0.1.0/24, the "Internet" endpoint IP address for the background traffic should be in this range, and similarly, the "local" endpoint IP address should be in 10.0.2.0/24. However, the source and destination addresses for the background traffic should also be IP addresses that don't exist in the network. Since they don't exist, the router won't be able to resolve the IP addresses to MAC addresses using ARP, so we will add static ARP entries on the router for all of the endpoint IP addresses that we'll use for our background traffic.

The default endpoint IP addresses are 10.0.1.254 and 10.0.2.254, so if you're going to use the defaults, SSH into your router node and add static ARP entries with:

```
sudo arp -s 10.0.1.254 02:47:a9:bb:e0:d0
sudo arp -s 10.0.2.254 02:37:a9:bb:e0:d0
```

(these are fake MAC addresses!) If you're going to use other endpoint IP addresses, add static ARP entries on the router node for these as well.

### Acquire a dataset

In this experiment, we show how to use a dataset of background traffic [described here](http://www.unb.ca/cic/datasets/vpn.html) that was generated at the University of New Brunswick for the paper

> Gerard Drapper Gil, Arash Habibi Lashkari, Mohammad Mamun, Ali A. Ghorbani, "Characterization of Encrypted and VPN Traffic Using Time-Related Features", In Proceedings of the 2nd International Conference on Information Systems Security and Privacy(ICISSP 2016) , pages 407-414, Rome, Italy.

The dataset captures real traffic from end users as they use various applications for browsing, email, chat, video streaming, file transfers, and others.  The major advantages to using this dataset for background traffic are:

* You can specify what kind of applications should appear in your background traffic, and in what proportion.
* The traffic is recent, captured in 2016, and reflects modern Internet applications.

However, this dataset gives no indiciation of what proportion of each application *should* be in background traffic, so you will have to use good judgment in making those experiment decisions.

To acquire a copy of this dataset, email [cic@unb.ca](cic@unb.ca). You will receive in response a link to a download website and a username and password for accessing the download website.

Browse the download website to find the file containing the captures, which is in the "ISCX-VPN-NonVPN-2016" subdirectory and is titled "CompletePCAPs.zip". Right-click on the file link and copy the URL.

Then, on the `tcpreplay` node, move to the directory with extra space:

```
cd /mnt
```

and download this file using the command

```
wget --user USERNAME --password 'PASSWORD' URL
```
where in place of `USERNAME`, `PASSWORD`, and `URL`, you specify the username and password you received by email, and the URL for the "CompletePCAPs.zip" file that you just found in your browser.

Extract the capture files from the archive using

```
unzip CompletePCAPs.zip
```

and run `ls` to verify that you have extracted the files.

As an alternative, depending on the needs of your experiment, you may be more interested in having background traffic with a realistic mix of applications and not necessarily have fine-grained control over what kind of traffic is represented. In this case, you can use the dataset from the University of Twente, described in

> Barbosa, Rafael Ramos Regis, et al. "Simpleweb/University of Twente traffic traces data repository." Centre for Telematics and Information Technology University of Twente, Enschede, Technical Report (2010). PDF: [https://ris.utwente.nl/ws/files/5096467/traces.pdf](https://ris.utwente.nl/ws/files/5096467/traces.pdf)

Specifically, we will use the "Trace 6" data described as follows:

>  A 100 Mbit/s Ethernet link connecting an educational organization to the internet has been measured. This is a relatively small organization with around 35 employees and a ittle over 100 students working and studying at this site (the headquarter location of this organization). All workstations at this location ( 100 in total) have a 100Mbit/s Lan connection. The core network consists of a 1 Gbit/s connection. The recordings took place between the external optical fiber modem and the first firewall. The measured link was only mildly loaded during this period. These measurements are from May - June 2007.

The data is available at [https://traces.simpleweb.org/traces/TCP-IP/location6/](https://traces.simpleweb.org/traces/TCP-IP/location6/). To download this onto your `tcpreplay` node, move to the directory with extra space:

```
cd /mnt
```

and then run

```
wget https://traces.simpleweb.org/traces/TCP-IP/location6/loc6-20070501-2055.gz
wget https://traces.simpleweb.org/traces/TCP-IP/location6/loc6-20070523-0005.gz
wget https://traces.simpleweb.org/traces/TCP-IP/location6/loc6-20070531-2043.gz
wget https://traces.simpleweb.org/traces/TCP-IP/location6/loc6-20070615-1644.gz
```

To extract the traces from the archive files, run

```
gunzip loc6-20070501-2055.gz
gunzip loc6-20070523-0005.gz
gunzip loc6-20070531-2043.gz
gunzip loc6-20070615-1644.gz
```

Also add a file extension to each file:

```
mv loc6-20070501-2055 loc6-20070501-2055.pcap
mv loc6-20070523-0005 loc6-20070523-0005.pcap
mv loc6-20070531-2043 loc6-20070531-2043.pcap
mv loc6-20070615-1644 loc6-20070615-1644.pcap
```

MAWI
Traces are collected from various sampling points on the WIDE Backbone, a trans-Pacific link between Japan and the United States





Citation: R. Fontugne, P. Borgnat, P. Abry, K. Fukuda. "MAWILab: Combining diverse anomaly detectors for automated anomaly labeling and performance benchmarking". ACM CoNEXT 2010. Philadelphia, PA. December 2010.


 Advantages
Disadvantages
As recent as 2018
~48 hours each trace, reflective of daily traffic changes
Backbone traffic better reflects traffic pattern
Greater diversity of clients
Updated daily to include new traffic from upcoming applications and anomalies
Anyone can immediately download


No fine-grained control over traffic patterns
Trans-pacific data may not be reflective of typical traffic

Link to dataset used: http://mawi.wide.ad.jp/mawi/ditl/ditl2018/

### Play back background traffic


## Notes

