
# SUMO — a Traffic Simulator over the Cloud with Yonohub

![SUMO](https://miro.medium.com/max/700/1*RLi4jgINeNszuFafJ-vnpQ.png)*Simulation of Urban Mobility — SUMO*

Simulation of Urban Mobility — SUMO

Intelligent Transportation System is an essential building block of full autonomy on the roads. To have a safe, sustainable, and efficient autonomous system in our daily life, a complex traffic system should be deployed on our normal roads. Vehicles should communicate with each other as well as with the road to send and receive some information.

This can be exemplified by assuming that we have a traffic jam in one of the intersections in the city. If we have a standard traffic lighting system, we can not normally solve the problem. On the other hand, assume that we have an intelligent system that interacts with the vehicles by receiving their locations and speed, the system at that point can change the traffic light sequence timing to resolve that issue. But how can we implement such an algorithm in the first place? Are we going to test it directly on the road?

Now, the traffic simulation comes to place. It facilitates the implementation, evaluation, and testing of such algorithms. However, it is not easy to find such a comprehensive simulator. But we have [SUMO](https://sumo.dlr.de/docs/)!

## SUMO
> **“S**imulation of **U**rban **MO**bility” (Eclipse SUMO) is an open-source, highly portable, microscopic, and continuous road traffic simulation package designed to handle large road networks.

![SUMO Scenario](https://cdn-images-1.medium.com/max/2560/1*l8m3a4PnxCnOlmOUkEtNhw.gif)*SUMO Scenario*

### What features offered by SUMO?

* Microscopic simulation — vehicles, pedestrians and public transport are modeled explicitly

* Online interaction — control the simulation with [TraCI](https://sumo.dlr.de/docs/TraCI.html)

* Simulation of multimodal traffic, e.g., vehicles, public transport, and pedestrians

* Time schedules of traffic lights can be imported or generated automatically by SUMO

* No artificial limitations in network size and the number of simulated vehicles

* Supported import formats: [OpenStreetMap](https://sumo.dlr.de/docs/Networks/Import/OpenStreetMap.html), [VISUM](https://sumo.dlr.de/docs/Networks/Import/VISUM.html), [VISSIM](https://sumo.dlr.de/docs/Networks/Import/Vissim.html), [NavTeq](https://sumo.dlr.de/docs/Networks/Import/DlrNavteq.html)

* SUMO is implemented in C++ and uses only portable libraries

SUMO is a powerful simulator for researchers, developers, and enthusiasts. Two ways are normally used to use any simulator. Either by running it on a local machine or over the cloud. Sadly, running the simulator locally is a bottleneck for many developers with any powerful simulator due to the available computational power. Fortunately, we can use [Yonohub](https://yonohub.com/).

## How to integrate SUMO in Yonohub?

Not only the computational power in Yonohub we want to get benefit from, however the encapsulation concept in [YonoArc](https://yonohub.com/yonoarc/) by designing your simulation scenario as a pipeline with different pieces of code encapsulated in blocks.

### SUMO Environment

Normally step zero will be the installation of SUMO. Yonohub offers [YonoEBuilder](https://yonohub.com/yonoebuilder/) which facilitates the steps of creating environments with few clicks. You can reuse and share the environments easily. For SUMO, you can even forget step zero as we published an [environment](https://store.yonohub.com/product/sumo-environment/), which contains the latest version of SUMO, in [YonoStore](https://yonohub.com/yonostore/). Here is a view of the SUMO environment developed using YonoEBuilder,

![SUMO Environment](https://cdn-images-1.medium.com/max/2000/1*rEw8V8a8xSJhM4y6vakwTg.png)*SUMO Environment*

As we can notice, the environment installs SUMO, clones the official repository, and setups [noVNC](https://novnc.com/info.html) to visualize SUMO.

**NOTE**: You can use this environment in a [custom app](https://docs.yonohub.com/docs/yonohub/custom-apps/) in the phase of creating a network in [NETEDIT](https://sumo.dlr.de/docs/NETEDIT.html) as well as interacting with SUMO-GUI directly.

### Scenario

Traffic Light System (TLS) is the scenario integrated. We used the pre-implemented TLS [example](https://github.com/eclipse/sumo/tree/master/tests/complex/tutorial/traci_tls) in SUMO. The example uses the TraCI API to interact with SUMO via a client-server protocol, where SUMO acts as the server and TraCI as the client.

### SUMO YonoArc Block

Next, we use that environment to develop a YonoArc block. We used the Block Manager to create the YonoArc block. The SUMO block encapsulates the following functionalities,

* Running noVNC in a background process.

* Testing the above TLS scenario.

* Streaming some data related to the simulation to be visualized through the Dashboard.

To implement the above functionalities, we created the SUMO block using the [YonoArc Python 3 API](https://docs.yonohub.com/docs/yonohub/yonoarc/yonoarc-python3-api/). The python 3 source code of the block is an integration of the Python 3 API and the TLS [example](https://github.com/eclipse/sumo/blob/master/tests/complex/tutorial/traci_tls/runner.py) as shown below,

<iframe src="https://medium.com/media/57728e582f531084cfbeb99644c4d463" frameborder=0></iframe>

In the *on_start(self)* function, we run noVNC as a separate process. Then, generating the routes and randomly assign 3600 vehicles to the created routes, using *generate_routefile() *function. Next, we start SUMO using the *start* function in TraCI which establishes as well the client-server communication.

After that, the *run(self)* function is called. The function simply calls another function called *run_scenario(self) *which is responsible for the changing of the traffic light sequence as well as publish the following data related to one of the vehicles,

* Position

* Speed

* Acceleration

* Lateral Speed

* Waiting Time

All these data are published through the block’s ports. All the information is extracted and streamed each step of the simulation to be visualized in YonoArc via the Dashboard.

Moreover, we need to add the noVNC URL to the block during creation to be able of opening noVNC in YonoArc.

![URL Insertion](https://cdn-images-1.medium.com/max/2216/1*I6VmkFOPKRaknQ-z47E-nw.png)*URL Insertion*

Now, you successfully create the SUMO YonoArc block and ready to construct the pipeline in YonoArc.

### How to Use SUMO in Yonohub?

Let us demonstrate the utilization of the SUMO block in the following video tutorial by forming a pipeline which runs the TLS scenario and visualize some data.

[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/7-fWAOZOP18/0.jpg)](https://www.youtube.com/watch?v=7-fWAOZOP18)*SUMO TLS Tutorial in Yonohub*

All the [blocks](https://store.yonohub.com/product/sumo/) you can freely purchase it from YonoStore. You can download the arc file of the tutorial above directly from [here](https://github.com/YonoHub/SUMO---a-Traffic-Simulator-over-the-Cloud-with-YonoHub) as well as the source code of the SUMO YonoArc block.

## Conclusion

To sum up, we demonstrated the process of integrating SUMO in Yonohub and using it that shows the capability of SUMO for traffic simulation in addition to Yonohub’s computational power as well as the encapsulation concept in YonoArc.

## About Yonohub
> Yonohub is a web-based cloud system for development, evaluation, integration, and deployment of complex systems, including Artificial Intelligence, Autonomous Driving, and Robotics. Yonohub features a drag-and-drop tool to build complex systems, a marketplace to share and monetize blocks, a builder for custom development environments, and much more. YonoHub can be deployed on-premises and on-cloud.

Get $25 free credits when you sign up now. For researchers and labs, contact us to learn more about Yonohub sponsorship options.

If you liked this article, please consider following us on Twitter at [@yonohub](https://twitter.com/YonoHub), [email us directly](mailto:info@yonohub.com), or[ find us on LinkedIn](https://www.linkedin.com/showcase/yonohub). I’d love to hear from you if I can help you or your team with how to use Yonohub.

## Reference

[1] [https://sumo.dlr.de/docs/](https://sumo.dlr.de/docs/)

[2] [https://www.dlr.de/ts/en/desktopdefault.aspx/tabid-9883/16931_read-41000/](https://www.dlr.de/ts/en/desktopdefault.aspx/tabid-9883/16931_read-41000/)

[3] [https://verkehrsforschung.dlr.de/de/news/verkehr-basteln-ganz-einfach-mit-sumo](https://verkehrsforschung.dlr.de/de/news/verkehr-basteln-ganz-einfach-mit-sumo)

[4] [https://yonohub.com/](https://yonohub.com/)
