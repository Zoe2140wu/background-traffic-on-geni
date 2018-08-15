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

Advantages    | Disadvantages
------------- | ------------- 
Able to specify application traffic | Have to email them to obtain a copy of the dataset
Fine-grained control: each capture is a few minutes long, can control proportion of each application type in experiment
Recent| Doesn’t indicate what proportion of each application should be in background traffic      | 

Citation: 


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

