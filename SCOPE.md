# Base station and UEs

## Start the SCOPE Base Station

As a first step, on one of the two BS terminals **we start the SCOPE base station on the first node of the reservation** and pass the configuration of the base station through the `radio_interactive.conf` file located in the `~/radio_api` directory. Set the parameters of the `radio_interactive.conf` as those of this [file](radio_interactive.conf). Note that the base station application will only start if this is the first node in the reservation, otherwise a UE application will start. In the SCOPE node, type the following commands which will take care of starting the base station and the core network applications in a `tmux` session (a cheatsheet for `tmux` can be found [here](https://www.stationx.net/tmux-cheat-sheet/)).

```bash
cd radio_api/
python3 scope_start.py --config-file radio_interactive.conf
tmux a
```


## Start the SCOPE UEs

After the BS starts, we can start the UEs. We use the same steps as before. Run the following commands on the **terminal of the second and third SRNs of your reservation**, under the image `scope-with-e2`.

```bash
cd radio_api/
python3 scope_start.py --config-file radio_interactive.conf
tmux a
```

## Exchange traffic between base station and UEs

Now that we have instantiated a cellular network on Colosseum, we will exchange traffic between BS and UEs.

1. We will use the `ping` command to confirm reachability of BS and UEs:
    - Open a terminal on the base station and ping the IP address of the UEs: `ping 172.16.0.2` and `ping 172.16.0.3`
    - Open a terminal on the UEs and ping the IP address of the base station: `ping 172.16.0.1`
2. We will use the `iperf3` tool to exchange TCP/UDP traffic between base station and UE:
    - Open a terminal on each UE and start an iPerf3 server: `iperf3 -s`
    - Open two terminals on the BS and start an iPerf cliend for each UE: `iperf3 -c 172.16.0.2` and `iperf3 -c 172.16.0.3`
    - You should see TCP packets being exchanged for 10 seconds. Compare the transmit and receive rates at the base station and UE
    - Now do the same but with UDP (parameter `-u` in `iperf3`) and with different source rates (parameter `-b` in `iperf3`) and compare the transmit/receive throughput and packet losses (where `X` is the last octect of the UE IP address):
        - 0.5 Mbps: `iperf3 -c 172.16.0.X -u -b 0.5M`
        - 2 Mbps: `iperf3 -c 172.16.0.X -u -b 2M`
        - 5 Mbps: `iperf3 -c 172.16.0.X -u -b 5M`



## Configuring Network Slicing

## Data collection for AI training and data analytics
