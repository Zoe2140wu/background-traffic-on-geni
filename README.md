# Generating realistic background traffic on GENI


## Background


## Results

## Run my experiment


### Set up topology

First, download my Rspec. Open your computer's control panel (Termianl if you are using MAC) and run 

`wget https://raw.githubusercontent.com/Zoe2140wu/background-traffic-on-geni/master/replaywrapperRspec.xml`

The Rspec should be in your home directory as replaywrapperRspec.xml. If you want to read the contents of the page, you can go to my repository page and open [replaywrapperRspec.xml](https://github.com/Zoe2140wu/background-traffic-on-geni/blob/master/replaywrapperRspec.xml).

In the GENI Portal, create a new slice and press "Add Resources." Scroll down to the "Choose RSpec" bar, select the "File" option, and upload your downloaded replaywrapper RSpec. Note that the topology uses a raw PC IG node, which is not available on all aggregates. Press "Reserve Resources" at the very bottom of the page and wait for your resources to be ready (they should be green as shown). When they are ready, SSH into your tcpreplay node. Run 

`sudo chmod a+w /mnt`

to mount your extra disk space. Your extra disk space should be available in the mnt directory.

Next, SSH into your router node to add static ARP entries. Run 

```
sudo arp -s 10.0.1.254 02:47:a9:bb:e0:d0
sudo arp -s 10.0.2.254 02:37:a9:bb:e0:d0
```

That way traffic will run through the experiment nodes.

Next, get the replaywrapper script from GitHub. In your tcpreplay node, run

`wget -P /usr/local/bin https://raw.githubusercontent.com/Zoe2140wu/background-traffic-on-geni/master/replaywrapper
sudo chmod a+x /usr/local/bin/replaywrapper`

The replaywrapper script should by available when you run `ls /usr/local/bin` 

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

