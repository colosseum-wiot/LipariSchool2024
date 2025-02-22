# Base station and UEs

As a first step, set the parameters of the `radio_interactive.conf` _on ALL `scope` nodes (gNB and the two UEs)_ as those of this [file](radio_interactive.conf) (you can use `scp` to copy the file, or `nano/vim` to edit the file). Or, you can use this code below:

```
cat > /root/radio_api/radio_interactive.conf << EOF
{
  "capture-pkts": "False",
  "colosseumcli": "True",
  "generic-testbed": "False",
  "iperf": "False",
  "users-bs": "3",
  "write-config-parameters": "True",
  "network-slicing": "True",
  "global-scheduling-policy": "0",
  "slice-scheduling-policy": "[0, 0]",
  "tenant-number": "2",
  "slice-allocation": "{0: [0, 8], 1: [9, 16]}",
  "slice-users": "{0: [2], 1: [3]}",
  "custom-ue-slice": "True",
  "force-dl-modulation": "False",
  "heuristic-params": "{'buffer_thresh_bytes': [1000, 2000], 'thr_thresh_mbps': [0.25, 0.75]}",
  "bs-config": "{'dl_freq': 980000000, 'ul_freq': 1020000000, 'n_prb': 50}",
  "ue-config": "{'dl_freq': 980000000, 'ul_freq': 1020000000, 'force_ul_amplitude': 0.9}"
}
EOF
```

## Start the SCOPE Base Station

On one of the two BS terminals **we start the SCOPE base station on the first node of the reservation** and pass the configuration of the base station through the `radio_interactive.conf` file located in the `~/radio_api` directory.  Note that the base station application will only start if this is the first node in the reservation, otherwise a UE application will start. In the SCOPE node, type the following commands which will take care of starting the base station and the core network applications in a `tmux` session (a cheatsheet for `tmux` can be found [here](https://www.stationx.net/tmux-cheat-sheet/)).

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


## Configuring Network Slicing and Scheduling

The slicing configuration specified in the [radio_interactive.conf](radio_interactive.conf) is loaded at the BS start-up time. This is passed in the form of `{slice_num: [lowest_allowed_rbg, highest_allowed_rbg], ...}`` (inclusive). E.g., `{0: [0, 3], 1: [5, 7]}`` assigns RBGs 0-3 to slice 0 and 5-7 to slice 1. The SCOPE APIs then write the passed configuration on configuration files (e.g., on this [file](https://github.com/wineslab/colosseum-scope/blob/main/radio_code/scope_config/slicing/slice_allocation_mask_tenant_0.txt) for slice 0), one per each slice. These slicing parameters are periodically reloaded from file. The frequency at which they are reloaded can be modified through the variable `line_change_frequency_ms` in `../radio_code/srsLTE/srsenb/src/stack/mac/scheduler_metric.cc`, function `dl_metric_rr::sched_users`. In each of these files, slicing is specified as a 0-1 bitstring, where a `0` means the RBG cannot be used, and `1` that if can be used for transmissions. As an example, the string `1111110000000000000000000` would allocate RBGs 0-5 to the slice.

Similarly, the scheduling can be tuned among the round-robin (corresponding to policy `0`), waterfilling (`1`), and proportional fairness (`2`) scheduling policies. They are written on this [file](https://github.com/wineslab/colosseum-scope/blob/main/radio_code/scope_config/slicing/slice_scheduling_policy.txt), one entry per slice. This file is also reloaded periodically. If slicing is **not** used, instead, the global slicing policy can be set in this [file](https://github.com/wineslab/colosseum-scope/blob/main/radio_code/scope_config/scope_cfg.txt).


## Data collection for AI training and data analytics

KPIs are periodically (e.g., every 250 ms) written on file in the folder `~/radio_code/scope_config/metrics/csv` for the _gNB only_ (an example is shown here [directory](https://github.com/wineslab/colosseum-scope/tree/main/radio_code/scope_config/metrics/csv)), one file per user, named `<ue_imsi>_metrics.csv`, where `ue_imsi` is the IMSI of the UE. Valid UE IMSI for this exercise should be `001010123456002` and `001010123456003`, you can ignore files for which the IMSI value only has 2 or 3 digits. The E2 agent of the O-DU peridically reads user KPIs from these files and sends them to the E2 termination of the near-real-time RIC, and eventually to the xApp.

