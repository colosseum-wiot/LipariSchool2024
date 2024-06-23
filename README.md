# LipariSchool2024
Tutorial for end-to-end O-RAN. deployment and AI development for the Lipari Summer School 2024

# O-RAN Assignment

In this tutorial, you will learn how to (i) access and make a reservation on Colosseum; (ii) use the SCOPE [[1]](#cite1) and ColO-RAN [[2]](#cite2) components of OpenRAN Gym [[3]](#cite3) to develop O-RAN-compliant Artificial Intelligence (AI)/Machine Learning (ML) solutions; and (iii) deploy them as xApps on the near-RT RAN Intelligent Controller (RIC). In this tutorial, we are going to use 4 nodes, representing one Base Station (BS), two users (UE) and the near-RT RIC. You will learn how to collect data on Colosseum, perform data analytics and design AI-based xApps that perform either forecasting or real-time RAN control of network slicing strategies.

## Important information regarding your credentials to access Colosseum

We have pre-allocated 10 accounts exclusively dedicated to the school. Each account follows the nomenclature `lssa<X>`, where `<X>` is an integer number from 1 to 10. The account for team 5 is `lssa5`. Please take notice of the team number assigned to you, it will be necessary to access your team account on Colosseum. The password for all team accounts is `ChangeMe`. If you do not know which team number has been assigned to you, please ask one of the instructors.

NOTE: In some portions of the tutorial (e.g., when asked to login into the VPN, a couple of lines below), your credentials will also be referred to as LDAP username and password.

<code style="color : red">Freshdesk needs email address to login. How do we make that happen?</code>

## Make a reservation on Colosseum

1. Connect to Colosseum VPN (instructions [here](https://colosseumneu.freshdesk.com/support/solutions/articles/61000285824-cisco-anyconnect-remote-vpn-access)) and login to [Colosseum website](https://experiments.colosseum.net).
2. Make a reservation with four SRNs (see instructions on [Making a Reservation](https://colosseumneu.freshdesk.com/en/support/solutions/articles/61000253463-making-a-reservation-interactive-and-batch-mode-)). The order of the SRNs shall be the following: for the first three SRNs choose the `scope-with-e2` image, while for the forth choose the `coloran-near-rt-ric-prebuilt` image. It is noted that the RIC should be allocated to the last SRN in the Colosseum reservation.

    <p align="center">
      <img src="images/order-SRNs.png" width="350" />
    </p>

3. On the reservation page, you can find the assigned SRNs/nodes and their hostnames by hovering over the nodes.
4. At your scheduled reservation time, open five terminals. Two for the SCOPE BS, one for each SCOPE UE (i.e., two), and one for the RIC. In each terminal, ssh as `root` user into the assigned Colosseum SRNs: `ssh root@<srn-hostname>`. The password for the `scope-with-e2` container is `scope`, while for the `coloran-near-rt-ric-prebuilt` image, the password is `ChangeMe`. In **any one** of the terminals, run the following command to start a Colosseum Radio-frequency (RF) scenario through the Colosseum CLI API (see instructions [here](https://colosseumneu.freshdesk.com/en/support/solutions/articles/61000253397-colosseum-cli)): `colosseumcli rf start 1009 -c`. More information about the scenario can be found in [Test Scenario All Paths 0 dB (1009)](https://colosseumneu.freshdesk.com/support/solutions/articles/61000277641-test-scenario-all-paths-0-db-1009). When the scenario starts, an output similar to the following is returned (time is in UTC):


```
Scenario Start Time is 22:30:45
```

## Start the SCOPE Base Station

As a first step, on one of the two BS terminals **we start the SCOPE base station on the first node of the reservation** and pass the configuration of the base station through the `radio_interactive.conf` file. Note that the base station application will only start if this is the first node in the reservation, otherwise a UE application will start. In the SCOPE node, type the following commands which will take care of starting the base station and the core network applications.

```
cd radio_api/
python3 scope_start.py --config-file radio_interactive.conf
tmux a
```

## Start the SCOPE UEs

After the BS starts, we can start the UEs. We use the same steps as before. Run the following commands on the **terminal of the second and third SRNs of your reservation**, under the image `scope-with-e2`.

```
cd radio_api/
python3 scope_start.py --config-file radio_interactive.conf
tmux a
```


# References

<a id="cite1">[1]</a> https://openrangym.com/ran-frameworks/scope

<a id="cite2">[2]</a> https://openrangym.com/o-ran-frameworks/coloran

<a id="cite3">[3]</a> https://openrangym.com/


