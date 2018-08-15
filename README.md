# Generating realistic background traffic on GENI


## Background


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


Next, get the `replaywrapper` script from GitHub. In your `tcpreplay` node, run

```
sudo wget -P /usr/local/bin https://raw.githubusercontent.com/Zoe2140wu/background-traffic-on-geni/master/replaywrapper
sudo chmod a+x /usr/local/bin/replaywrapper
```

The `replaywrapper` script should by available when you run `ls /usr/local/bin` 

We also need to set up the router so that it will redirect the background traffic between the "local network" and the "Internet". , SSH into your router node to add static ARP entries. Run 

```
sudo arp -s 10.0.1.254 02:47:a9:bb:e0:d0
sudo arp -s 10.0.2.254 02:37:a9:bb:e0:d0
```

That way traffic will run through the experiment nodes.

### Acquire dataset

VPN/ non-VPN Dataset

The dataset from the University of New Brunswick includes real traffic from two users– Alice and Bob– as they use various applications for browsing, email, chat, video streaming, file transfers, and others. 

Advantages    | Disadvantages
------------- | ------------- 
Able to specify application traffic | Have to email them to obtain a copy of the dataset
Fine-grained control: each capture is a few minutes long, can control proportion of each application type in experiment
Recent| Doesn’t indicate what proportion of each application should be in background traffic      | 

Citation: Gerard Drapper Gil, Arash Habibi Lashkari, Mohammad Mamun, Ali A. Ghorbani, "Characterization of Encrypted and VPN Traffic Using Time-Related Features", In Proceedings of the 2nd International Conference on Information Systems Security and Privacy(ICISSP 2016) , pages 407-414, Rome, Italy.



Link: http://www.unb.ca/cic/datasets/vpn.html


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

