## ECUs and CAN Bus
An **Electronic Control Unit (ECU)** is an embedded system that controls one or more subsystems inside a car or a vehicle in general. It acts as a sort of brain of the vehicle, managing the vast majority of the available operations, like fuel injection, ignition timing, gas emissions and so on. Since every component of a vehicle has ECUs, like the engine control module, the powertrain control module, the transmission control module, they can be considered as nodes of a in-vehicle vast network and therefore must communicate with each other to report all different types of information.

Initially, all nodes would communicate with each other using a Point-To-Point communication but, since we could easily add new ECUs, it would be problematic and expensive. To resolve this problem, Robert Bosch developed in 1986 the **Controller Area Network (CAN)**, an in-vehicle network bus-based standard of communication. This components allows the ECUs to communicate with each other without all the wiring required for the previous Point-To-Point system. Starting from 2003, the CAN Bus became the ISO 11898 standard of communication for in-vehicle ECUs, guaranteeing asynchronous data rate of 1Mbps at a 40m distance. Since it operates at both physical and data link layers of the OSI model, the ISO 11898-1 describes the data link layer, while the 11898-2 the physical.
### Advantages of the CAN Bus
The advantages of the CAN Bus are numerous:
- The first objective of the CAN Bus was to have **Lower Costs**, achieved by the shared bus and the message handling system;
- It guarantees **Robustness** thanks to its durability and reliability for Operation Technologies (OT), rather than Information Technologies (IT). It is specifically designed for real-time, deterministic communication between microcontrollers, sensors and actuators. Its robustness comes from features like differential signaling on twisted-pair wires, providing very high resistance to electrical and electromagnetic interferences;
- By being a message-based protocol, it is highly **Flexible** in adding and removing nodes without updating the system in a whole;
- Since it uses a specific priority marking mechanism to have different level of importance in communication, it is widely **Efficient**
### ECU's Architecture
All ECUs are essential composed by 3 elements:
	- **Microcontroller**, which is the central processing unit (CPU) of the ECU. It is responsible of the decision-making of what a received signal means and what messages it wants to transmit. Thanks to it, it is possible to connect the ECU to other devices like sensors and actuators;
	- **CAN Controller**, that stores all the received serial bits and passes messages to the processor. It is also responsible of transmitting bits serially on the bus upon reception from the processor, meaning that it is its responsibility to acknowledge the sender of receiving correctly the information. It is also the filter of messages, since thanks to it we ignore messages that are not meant to be read by the ECU or that are not related with the operations that the receiver can do;
	- **CAN Transceiver**, which protects the CAN Controller from over-voltage, that could cause physical damage to the component itself. It is a sort of converter, since it converts the CAN bus levels to levels that are understandable by the CAN Controller and viceversa

![[CANBus_ECU_Architecture.png]] 

### CAN Bus Communication
The CAN Bus, in order to communicate with all ECUs of the network, uses **differential wired-AND signals**:
	- **Differential**: unlike standard communication, which uses a single wire relative to a common ground, CAN uses differential signaling across two dedicated wires, CAN-High (CAN-H) and CAN-Low (CAN-L). Instead of measuring the voltage of a single against ground, the receiver measures the voltage difference between the 2 wires. By doing this, if an electromagnetic interference hits the cable, both wires are hit equally, so the difference is more or less the same, without any major consequences. What happens to the wires defines the **dominance**:
		- Supposing that the bus is at the default state and both wires are biased to a resting voltage, by being at the same voltage, their difference is null and therefore we have a **Recessive State (1)**, which means that any node could potentially easily overrides this state;
		- On the other hand, if a node wants to start the communication, it has to generate an impulse which results into a voltage difference. That difference is what determines the overwrite of the recessive 1 into a **Dominant State (0)**;
	- **Wired-AND**: For an AND output to be a 1, all the inputs must be a 1. If even a single input is 0, the output is 0. Applying this concept to the CAN Bus literally means that for the bus to transmit a recessive 1, the ECU doesn't have to do anything (not sending any impulse), whereas if they want to transmit a 0, they have to send an impulse.
Here is a graphical representation of the communication on the CAN Bus:

![[CANBus_Communication.png]]

As explained before, every time the ECUs sends a electromagnetic pulse, it register a differential voltage and therefore transmits a 0; otherwise, while in an idle state, the recorded value is 1.
### Structure of the CAN Frame
The standard CAN Frame structure is composed by 8 different fields.

![[CANBus_Frame_Structure.png]]

- **Start of Frame (SoF)**: it's a single bit that represent the will of a node to talk to other nodes. It's easy to say that this bit is dominant because to "start" a communication, our ECUs has to "stop being quiet" and generate a pulse that results into a differential in the voltage.
- **ID**: it is typically composed of 11bits in the 2.0A format, while it has 29 bits in the 2.0B format, and it represents the **frame** identifier. It's important to note that this **ID doesn't represent any sort of identification for the sender of the message neither the receiver**. Instead, what represents is the **priority** of the message, meaning that the longer the ECU sends a pulse, the higher the priority of the sent message. ;
- **Remote Transmission Request (RTR)**: a bit used to indicate whether a node is sending or requesting data from another node;
- **Control**: contains the **Identifier Extension Bit (IEB)**, which indicates whether the ID is 11-bits long or 29-bits long. In the former case, the IEB is set to 0 dominant, otherwise 1 recessive. It also contains the **Data Length Code (DLC)**, which is a sequence of 4 bits that specifies the length of the data bytes that the ECU wants to transmit;
- **Data**: the payload of our frame, so all the CAN signals that can be extracted and decoded for information from the frame;
- **Cyclic Redundancy Check (CRC)**: the error-checking guardian of the CAN frame and is crucial to ensure data integrity across the bus. Before a frame is sent, the transmitter runs the entire packet (ID, Control and Data Fields) through a mathematical function to generate a 15-bit checksum and then appends it to the frame after the data field. Every receiving ECU runs that same mathematical formula on the bits that it just heard from the bus and compare the calculated results against the CRC value sent by the transmitter. If even one single bit is flipped, the math won't match and therefore the receiver will detect a CRC error and broadcast an active error flag, forcing the transmitter to resend the message.
- **Acknowledgement (ACK)**: indicates whether a node has acknowledged and received the data correctly;
- **End of Frame (EoF)**: end of the CAN frame
### Error Counters and ECU's State
In order for a CAN frame to be valid, it needs to satisfy certain conditions. When these conditions are not met, the ECU takes action accordingly, by altering its error counter and, consequently, its state if needed. There are 2 error counters: 
	- **Transmit Error Counter (TEC)**: transmit errors are all the type of errors that occurs when a node messes up or detects a communication glitch. Its counter keeps track of how many times the nodes causes or encounters errors **while it is trying to send a message**. Every time an error is detected, the counter is incremented by 8, since having a faulty transmitter is dangerous to all the other ECUs;
	- **Receive Error Counter (REC)**: receive errors are all the types of error that are detected by a node **while it is listening to another node**. The counter is incremented by 1 every time an error is detected.
In both cases, when a node successfully sends or receives a message without getting any error, the hardware decrements both counters by 1. These counters are quantitative metrics used from the hardware controller to judge its reliability and decide whether it is safe to keep the node active, to disconnect it or to limit its functionality. In addition to this, let's dive more into the 3 possible states that a node can be set to:
	- **Error Active**: the normal operational state. In this situations, the node can send and receive messages and if it spots an error, it broadcasts an active (dominant) error flag, a sequence of 6-dominant bits;
	- **Error Passive**: the troubled state. The node enters in this state when the error counters climb past a threshold, which usually is 127. The node is still able to communicate, but if it detects an error, it is allowed only to send a passive (or recessive) error flag of 6-recessive bits;
	- **Bus-Off**: the penalty state. The node enters in this state when the error counters get to a very high value, like 255. In this scenario, the node is completely disconnected from the bus line or highly limited and it has to be restored by a qualified personnel
### Types of Errors in the CAN Bus Frames
In CAN Bus Transmission, we can have no less than five types of errors
	- **Bit Error**: this error occurs when an ECU, after comparing its transmitted bits with those on the CAN bus detects a mismatch (note that this is meant for data transmission, not arbitration);
	- **Stuff Error**: after sending five (5) consecutive bits with the same polarity (either dominant or recessive), the ECU has to send a bit with the polarity inverted in order to maintain soft synchronisation (continuously aligns receiver and transmitter clocks). When a stuff doesn't happen, an error is registered;
	- **CRC Error**: the computed bit differs from the received one. As already said, the receiver uses a mathematical formula to compute the received bits and compares them with the CRC value of the sender and if they differ, we have an error;
	- **Form Error**: if a fixed-form bit field has a bit error, the ECU raises a form error;
	- **ACK Error**: when a node receives a frame, it responds with the dominant bit in the acknowledgement frame. If there is no response, we raise an error
### Error Flags 
When a node detects an error, it immediately needs to warn the others on the bus that there is a problem. To do so, the node raises a error flag which can be active and passive, depending on the state that the node is currently set on. If the node is in error-active mode, it issues an active error flag which consists of 6 dominant bits; by doing so, the transmitted frame causes other nodes to violate the bit-stuffing rule of changing the polarity of the bit after 5-bit with the same one, then transmit their own error frame and then terminate any on-going transmissions or receptions. 
To provide a clarifying example, if a node is transmitting a message and it detects an error while reading what's the bus sending to the other ECUs, it immediately stops sending the message and violate the bit stuffing rule, in order to warn all the other nodes about the faulty frame and auto-increments its counter of transmission error. On the other hand, if for example a receiver detects the error, it auto-increments its receiver error counter and violates the bit stuffing rule, following the same principle described before.

Once the error is detected, in both cases, the discoverer node of the error raises an active error flag when it is in the active error mode and blasts 6 dominant bits. The other nodes, the reacting ones, once they have read the bit stuffing violation rule, trigger their own flag in response, raising secondary active error flags (again, active, which means that they are always 0 dominant, otherwise passive so 1 recessive). This mechanism is helpful to overlap the physical wire and clears the bus and reset it. 
### Bus Arbitration
The mechanism adopted by the Bus to decide which node is gonna transmit its message is called **Bus Arbitration Protocol** and it is both priority-based (by reading the Frame ID) and non-preemptive (so until the message is finished, only the current node can use it, even if its priority is higher and it is queued after the transmission started).

A specific interval of time is divided into slots, where the length of every slot is sufficient for any message to go back and forth in the whole bus. In order for a node to know if it can transmit its message, it listens to the bus to check if it is in an idle state, so when none is using the bus.

![[CANBus_Arbitration_1.png]]

If it is in idle state, the node waits for the next slot to start the **contention phase** by transmitting the SoF bit.

![[CANBus_Arbitration_2.png]]

Once the contention phase is started, every node that sent the SoF, now send the Frame ID and they all listen to the Bus output to understand if the bus is gonna transmit their message. 

![[CANBus_Arbitration_3.png]]

The node loses the contention of the slot if they transmitted a recessive 1 and detects a dominant 0.

![[CANBus_Arbitration_4.png]]

As it can be seen from this example, S1, S2 and S3 all listens to the bus. Since S1 transmitted a 1 while both S2 and S3 transmitted a 0, S1 is already out of the games for transmitting its message. S2 and S3 then continue transmit their frame ID until one of them sends a 0 and the other one a 1. In this case, S2 won the contention phase at the 7th bit. 

After the node terminates its transmission after winning the contention, with an EoF, the CAN Bus uses a 3 bit inter-frame symbol to separate packets. After this, it becomes idle again and ready to a new a contention phase.
### Accessing the CAN Bus 
Ideally, since accessing to the CAN Bus is easy because we only need to connect to the On Board Port (typically located near the driver's or passenger's seat), manufacturers use proprietary or semi-proprietary CAN encoding system for different reasons:
- Thanks to **Product Differentiation and Control**, we can expose or hide feature at will;
- They are the only figure capable of interpret those messages, thanks to **Secrecy**;
- Security surely is the major topic, using techniques like obfuscation and checks to prevent reverse engineering;
- Control over updates can provide more leverage over services
For those reasons, messages can differ for usage of IDs, bit offset and length, value offset and multiplexing.

## Reverse Engineering the CAN Bus and Attacking
So, given all the information needed to understand the functioning of the CAN Bus, from a security perspective, how could we *reverse engineer* the CAN traffic, by connecting a new ECU to the network? And how could we attack it? 
A first approach would be to read the traffic and try to identify which ID corresponds to which action. Assuming that we captured a packet trace (multiple messages) that contains an action of interest, we can easily replay this trace in different ways to try to identify the exact packet that triggers that action. 
Here a simple representation of the flow:

![[CANBus_Identification_Packet.png]]

Another possible approach would be using tools that were able to identify variations in the CAN bus messages and even thought we wouldn't know what is happening, we could see variations in the data field values.

### Attacker Model and Types of Attacks
Attacker can physically and remotely compromise more than one in-vehicle ECU in different means and this can be achieved by injecting messages with a spoofed ID (so pretending to be a trustable source) or by stopping/suspending message transmission of the compromised ECU. The "compromised" term can mean a weakly compromised component, in which an attacker cannot inject any fabricated message, or fully compromised, where the attacker have full control over the component.
#### Fabrication Attack
The **Fabrication Attack** requires a compromised ECU as a strong attacker to fabricate and inject messages with forged ID, DLC and data. The objective of this type of attack is to override any periodic messages sent by a legitimate safety-critical ECU so that the receiver gets distracted or becomes inoperable. Since the CAN protocol is entirely build on a trust-based broadcast architecture with zero native source authentication or cryptographic verification, any component that can access the bus can craft and transmit a frame using any ID it wants. When this operation is done by an attacker, an attack usually unfolds in these steps:
	- Every standard CAN frame has its ID hardcoded to represent specific physical meaning or sensor value, in a fabrication attack, the malicious component doesn't use its own ID; instead, it forges the ID of a critical victim ECU. Once it has done that, it broadcasts messages using that trusted ID but injects altered, malicious payloads (as an example, it can tell that the vehicle is going 250km/h when it's standing or sending fake breaking/steering commands);
	- Since the other ECUs have no way to verify who actually sent the frame, they blindly trust the message based purely on its ID and arbitration priority. As an example, injecting conflicting wheel-speed sensor packets can trick the traction control system into slamming on brakes while on a highway;

![[CANBus_Fabrication_Attack.png]]
#### Suspension Attack
The **Suspension Attack** requires a weakly compromised ECU and its objective is stop or suspend the weakly compromised ECU's message transmission, preventing the propagation of info to other ECUs. By doing so, ECUs that rely on the reception of such messages are also hit by this type of attack. Since legitimate ECUs transmit periodic messages on fixed schedules, receiving nodes expect to see those message continuously. A suspension attack disrupts this flow, because if an attacker compromises a central gateway or an ECU that bridges different sub-networks, that specific node can choose not to forward specific packets, acting like a malicious checkpoint that intercepts and swallows target frames instead of passing them along. 

![[CANBus_Suspension_Attack.png]]
#### Masquerade Attack
The objective of the **Masquerade Attack** is to manipulate an ECU while shielding the fact that another ECU is compromised; in order to so do, the attacker need to compromise 2 ECUs, one as a strong attacker and the other as a weak one. Firstly, the attacker monitors the traffic of the weak ECU and once it has obtained the transmission period, it stops the weak component and starts transmitting messages with the strong one. 

In many vehicle architectures, specific ECUs are expected to transmit data at strict, predictable time intervals. In a masquerade attack, the attacker will first silence the legitimate owner of that ID; once the ECU is silent or bypassed, the attacker's compromised component start transmitting frames using that exact same trusted ID. Since the receiving components see the correct ID and a valid payload structure, they accept the data completely unaware that it is coming from an unauthorised source.

#### Bus-Off Attack
The **Bus-Off Attack** is a Denial of Service (DoS) attack that exploits how ECUs access the bus. The goal of this type of attack is to compromise the network and in particular multiple healthy ECUs with a minimal number of messages. As described before, the CAN bus forces the ECUs to measure their errors with specific counters and, once they reach a threshold value, they get disconnected from the bus; the attacker exploits this feature of CAN bus to iteratively isolate ECUs injecting attack messages, rapidly increasing the ECU's TEC.
Let's suppose that a victim V periodically sends a message M. The attacker can deliver a successful bus-off attack if all conditions are met:
	1. Both victim and attacker's ID are the same;
	2. Their frame is transmitted at the same time;
	3. The attack message has at least one dominant bit while the victim is recessive
By doing so, since in the CAN Bus the ECU are never told that they won the arbitration phase, if their ID matches the one that was read by the Bus, they would think that it is transmitting their message. The problem is raised when the victim reads a bit value different from the one that it transmitted (but this is not an error, the Bus is transmitting the attacker's dominant bit of the transmitted message). Following the logic described before, once an ECU spots an error during transmission, it increments by 8 its TEC. 

##### Attack Phases of the Bus-Off Attack
Both victim and attacker start in error-active mode, and the attacker targets one of the victim's periodical messages. The attacker sends the malicious message and the victim's TEC increases by 8 since it's reading a different value on the Bus. Since the victim is in error active mode, by detecting an error during the transmission, it would raise a active error flag of dominant bits. By doing that, the attacker would also have to increment its counter because it would detects discrepancies during the transmission. After this, they would both retransmit their messages and the attackers would bring the victim to error-passive mode, simply by sending one message.

After 16 retransmissions, both victim's and attacker's TEC reaches 128, and are set to error-passive. As a consequence, the victim attempts the delivery of a passive error flag with 6 recessive bits. The attempt to transmit this flag persists until the end of the attacker's frame. Due to the successful transmission, the attacker doesn't go to error-passive.

Once the scheduled interval of the target message is elapsed, the victim retransmits the message again. At the same time, the attacker re-injects the malicious message. Since the victim is in error-passive mode, the attacker's TEC decreases by 1, whereas the victim's increases by 7 (this happens before when the ECU is in error-active mode, the IFS is 3 recessive bits, while for error-passive mode nodes is 11, so at some point, the victim node will eventually transmit the message and decreasing by 1 the TEC). The attacker keeps sending the message until the victim reaches 255 and then it's bus-offed. It's important to note that, since Frame IDs do not contain any information regarding the transmitter, but rather priority and time interval of messages, the attacker should target low-value ID (so safety-critical components with very high priority).

As said before, the attacker must have a dominant bit where the the victim has a recessive one, while all the other preceding bits are equal. Since they have the same frame ID, that bit should rather be in the control or in data field (because CRC and ACK are set by the CAN controller and cannot be altered). Setting the ID of the attacker to be the same as the victim can be quite difficult since the ECUs cannot acquire the IDs of all received messages, but only the ones that pass through its message filter. 
We can distinguish the messages in received and accepted and since the attacker can only read the ID from accepted messages, meeting the requirements on the same ID depends on the filter of the victim ECU.
Still, even if the attacker knew anything about the ID and message's modification, they need also to know when to send it. Precise synchronisation is mandatory for the success of the attack since even a little de-synch can result in a single arbitration winner (so failing the attack). In order to have perfect synchronisation, the attacker can leverage the periodicity of CAN frames and send a message every T seconds, analysing the traffic flow. 
##### Preventing Bus-Off Attacks
The defence mechanism over bus off attacks can leverage two features:
- In Phase 1, at least 2 consecutive errors occur during the transmission of frames, so we watch for consecutive error frames with an active error flag;
- In Phase 2, at the time when the error-passive victim's TEC increases, a message with the same ID will be successfully transmitted by some ECU on the bus.
The bus off attack is easily detectable by a good intrusion detection system, but there are also other approaches like including physical layer intrusion detection system due to difference in hardware between attacker and victim. A secure hardware may also prevent the attack by detecting and blocking the local transmission of the attacker. Finally, CAN Bus Authentication may also prevent these kinds of attackers, but it is rarely implemented due to high costs and complexity.
##### Preceded ID
Even if CAN messages are periodic, so we could understand the flow of messages over time, jitter may still impair the effectiveness of our attack, so it is something we need to consider when planning an attack. 
To overcome this issue and have a good level of certainty that our attack will success, the attacker can exploit the fact that **nodes, which have either lost arbitration or had new messages buffered while the bus was busy, attempt to transmit their messages as soon as the bus becomes idle again**. This is fundamental to define **preceded ID** which, given a node with ID M, is the ID of the message that completed its transmission right before the start of M. 

![[CANBus_Preceded_ID.png]]

In the picture above we can see clearly what I just exposed: when $M_1$ arrives, it is transmitted since it won the arbitration phase. While the CAN Bus is transmitting $M_1$, both $M_2$ and $M_3$ arrives and they have to wait untile the next slot to decide which is gonna be transmitted. Supposing that $M_2$'s ID is lower (so it has a higher priority) of $M_3$, even if the latter came first, it still has to wait $M_2$ to be transmitted. With that said, $M_1$'s ID is the Preceded ID of $M_2$ and $M_2$'s ID is the preceded ID of $M_3$. 

Since message IDs never changes (and therefore their priority is unchanged), the preceded ID gives the attacker a way of predicting when to send the message. If the target message has no preceded ID, the attacker can create one and send both the preceded ID and the attack message: to successfully inject an attack message at the exact millisecond, the attacker's ECU needs a reliable timing reference from the bus traffic when its turn or window is approaching. If the target victim message doesn't have a reliable frequent preceding message that the attacker can use as a timing anchor, the attacker takes the initiative. The attacker injects its own manufactured anchor message which acts as the preceded ID onto the bus right before the attack window. 
###### Fabricating a Preceded ID 
Elements that are fundamentals for success in Bus-Off Attacks are injection timing, quantity and content of the fabricated preceded ID messages. Regarding timing, it is mandatory to inject the message right before the target and simultaneously manage the jitter situation, which is the only source of randomness and follows a Gaussian distribution.

The successive times at which the attacker receives the victim message can be expressed as: $$ t^{adv}_n = t^{vic}_n + \mathbb{D} = t_{orig} + J_n + \mathbb{D} \\ \\ t^{adv}_{n + 1} = t^{vic}_{n + 1} + \mathbb{D} = t_{orig} + T + J_{n + 1} + \mathbb{D}$$Since the attacker and the victim ECU do not share the same internal hardware clock, the attacker needs to have a shared physical event on the bus to synchronise their timers and that event is the preceded ID. Regarding the formulas above:
- $t^{vic}_n$ is the exact moment the victim send its $n$-th periodic message;
- $t^{adv}_n$ is the exact moment the attacker must send its $n$-th malicious message to cause a collision with the victim;
- $t_{orig}$ is the ideal starting timestamp of the victim's periodic stream;
- $J_n$ is the jitter caused by CPU load or bus traffic in the victim's $n$-th transmission;
- $\mathbb{D}$ is the precise microsecond offset the attacker waits after their timing checkpoint before firing the payload
So for fabrication of the preceded ID, the attacker has to start transmission of its preceded ID message(s) before the target and make sure that the busy is busy until the attack and the target message are synchronised.

We can therefore compute the next victim's transmit time as: $$t^{vic}_{n + 1} = t^{adv}_n + T - \mathbb{D} + J_{n + 1} - J_n = t^{adv}_n + T - \mathbb{D} + J^*$$With that in mind, we can say that the next instant in which the victim will send its next message ($t^{vic}_{n + 1}$)  is given by taking the time we just attacked ($t^{adv}_n$), adding to it the fixed periodic interval between the victim's messages ($T$), minus the attacker's delay because the attacker's previous timestamp was already offset by $\mathbb{D}$ and we finally add the jitter difference ($J^* = J_{n + 1} - J_n$, since the attacker doesn't need to know absolute perfect clock time of the vehicle's internal clock, it calculates the difference of consecutive jitter variations. By doing that, the attacker can dynamically correct their internal prediction clock from one cycle to the next).

We can so determine some constraints for the attacker: $$
	\begin{aligned} 
		1.\  &t_{fab} < min(t^{vic}_{n + 1}) = t^{adv}_n + T - \mathbb{D} + min(J^*) \\ \\
		2. \ &t_{fab} + \mathbb{H} > max(t^{vic}_{n+1}) = t^{adv}_n + T - \mathbb{D} + max(J^*) \\ \\
		3. \ &\mathbb{H} = \mathcal{k}\mathbb{F} > max(J^*) - min(J^*)
	\end{aligned}
$$So it basically means that :
1. the attacker fabrication time must happen before the earliest possible arrival time of the victim's next message;
2. The attacker's transmission window plus the the bus holding time must extend beyond the latest possible arrival of the victim's message;
3. The attacker's transmission window must be large enough to cover the entire uncertainty gap caused by the jitter (in simple words, it must keep the bus busy).
To sum it up, this proves, whether or not, the attacker can successfully catch the victim's window. If the jitter range is to wide and the attacker's injection window isn't long enough, the attack message will miss, arbitration will sort it out cleanly and the collision won't happen. 

This "random" value of $J^*$ is, in reality, bounded with boundaries approximated as $$|max(J^*)| = |min(J^*)| \approx \mathbb{I}\sqrt{2}\sigma_v$$where $\sigma_v$ is the standard deviation of the victim's normal transmission timing and $\mathbb{I}$ is an attack parameter used to guarantee success (in a normal bell-curve distribution, multiplying a standard deviation for a specific number covers 99.9% of all possible variation. In our case, it's usually 3 or 4).

###### Determining the Number of Messages
In order to satisfy the third attacker's constraint, the attacker need to occupy the bus for a duration of $2\sqrt{2}\mathbb{I}\sigma_v$ and this can be done by sending one or more preceded ID messages. Since we don't want to be detected while doing this, we need to minimise the number of injections by maximising $\mathbb{F}$, which is the length of the preceded ID message. To do that, we can exploit the bit stuffing rule and send messages with the most stuffed bit. 
Based on this, we can conclude that the number of preceded ID needed can be expressed as $$k = \ce{\frac{max(J^*) - min(J^*)}{\mathbb{F}^*}} = \frac{2\sqrt{2}\mathbb{I}\sigma_vS_{bus}}{124}$$
###### Content of Preceding ID Messages
The adversary needs to decide which id to use for fabricating the preceded ID messages; if only one is needed, the attacker can just use the next "free" ID. In order to be as elusive as possible, this ID can be changed at every attack attempt by choosing among the least frequently sent on the bus. 
##### WeepingCAN: A Stealthy Bus-Off Attack
WeepingCAN is a variation of the original bus-off attack, in which:
- The attacker disables the retransmission of the attack message with the same ID as the victim;
- The attacker causes recessive bit errors;
- The attacker doesn't fabricate preceded ID messages;
- The attacker randomises bit errors;
The main goal of this variation is to avoid exhibiting temporal differences due to the error state transition. 
###### Attack Cycle
The attacker synchronised with the victim using the same approach of the original bus-off attack; however, it disables the transmission of the attack packet:
1. The attacker injects the attack message;
2. The attacker's CAN Controller sends an error active flag do to the bit error in the attacker's packet, which increases both attacker's and victim's TEC by 8 (as standard);
3. The victim then retransmits the original packet and decrement its TEC;
By disabling retransmission, the attacker removes an easily detectable feature (consecutive retransmissions), so the attack is now composed by an active error flag and a successful retransmission. 
However, in order to disable retransmissions, the attacker can:
- Disable automatic retransmission for all messages, by writing to a control register that the ECU should never retransmits;
- Abort transmission on transmit error, since most controller can raise an interrupt in transmit errors
In order to keep its TEC lower than the victim, the attacker must identify additional messages to transmit, since its need to transmit at least one more message than the victim for each attack packet, while the victim should transmit less than 7 other packets in this time frame.

Supposing that the attacker and victim both have a single message they can send and the attacker sends its packet with $\frac{1}{5}$ the period of the victim. With that, the attacker's TEC is 3, while the victim's is 7. By that, the victim can be forced to error passive mode with 19 attack messages. 
Since the presence of successful messages over passive error flags of the victim is a marker of a possible bus-off attack in action, the attacker should randomise packets or, even better, the error bit's position. 
By offline analysis of a CAN trace, it is possible to discover the DLC of messages, which is a fixed constant. The number of dominant bits in DLCs is between 1 and 3, so the entropy of a random injection is low. 
An attacker may inject recessive error in the data field, which often encodes a number that does not change quickly because of physical constraints or an event identifier that comes from a set of events. By identifying a pattern in this, they can inject the error in the data.

Moreover, injecting errors via controlling CAN Frames requires synchronisation with the victim and careful-crafted attack frames when, in simple words, we just need to flip some bits to bus off an ECU. As a result, with at least 32 injected fake frames or bits we can force an ECU to go bus-off
