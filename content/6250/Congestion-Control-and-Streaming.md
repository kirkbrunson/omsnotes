---
title: "Congestion Control and Streaming"
date: 2018-03-11T20:33:32-04:00
draft: false
weight: 8
---



# Lecture 6: Congestion Control, & Streaming


In this section of the course we'll learn about resource control and content distribution. Resource
control deals with handling bandwidth constraints on links. And in this first section, we'll focus
on controlling congestion along links. To learn more about TCPA and congestion control, your
Mininet project will explore what happens when congestion control goes wrong.

## Congestion Control

![](/6250/images/fig8.0.png)

Okay, we're starting course two on congestion control and streaming. And we'll first talk about
congestion control. In particular, what is congestion control and why do we need it? Simply put,
the goal of congestion control is to fill the Internet's pipes without overflowing them. So to think
about this in terms of an analogy, suppose you have a sink, and you're filling that sink with
water. Well, how should you control the faucet? Too fast and the sink overflows. Too slow and
you are not efficiently filling up your sink. So what you would like to do is to fill the bucket as
quickly as possible without overflowing. The solution here is to watch the sink. And as the sink
begins to overflow, we want to slow down how fast we're filling it. That's effectively how
congest control works.


## Congestion

![](/6250/images/fig8.1.png)

Let's suppose that in a network we have three hosts shown as squares at the edge of the network
that are connected by links with capacities as shown. The two senders on the left can send at
rates of 10 megabits per second and 100 megabits per second, respectively. But the link to the
host on the right is only 1.5 megabits per second. So these different hosts on the left are actually
competing for the same resources inside the network. So the sources are unaware of each other
and also of the current state of whatever resource they are trying to share, in this case, how much
other traffic is on the network. This shows up as lost packets or long delays and can result in
throughput that's less than the bottleneck link, something that's also known as congestion
collapse.

## Congestion Collapse

![](/6250/images/fig8.2.png)

In congestion collapse, an increase in traffic load suddenly results in a decrease of useful work
done. As we can see here, up to a point, as we increase network load, there is an increase in
useful work done. At some point, the network reaches saturation, at which point increasing the
load no longer results in useful work getting done. But at some point, actually increasing the
traffic load can cause the amount of work done or the amount of traffic forwarded to actually
decrease. There are many possible causes. One possible cause is the spurious re-transmissions of
packets that are still in flight. So when senders don't receive acknowledgements for packets in a
timely fashion, they can spuriously re-transmit, thus resulting in many copies of the same packets
being outstanding in the network at any one time. Another cause of congestion collapse is simply
undelivered packets, where packets consume resources and are dropped elsewhere in the
network. The solution to spurious re-transmissions is to have better timers and to use TCP
congestion control, which we'll talk about next. The solution to undelivered packets is to apply
congestion control to all traffic. Congestion control is the topic of the rest of this lesson.


### Congestion Collapse Quiz

![](/6250/images/fig8.3.png)

Let's take a quick quiz. What are some possible causes of congestion collapse? Faulty router
software? Spurious retransmissions of packets in flight? Packets that are travelling distances that
are too far between routers? Or, undelivered packets? Please check all options that apply.

### Congestion Collapse Solution

![](/6250/images/fig8.4.png)

Congestion collapse is caused by spurious retransmissions of packets in flight and undelivered
packets that consume resources in the network but achieve no useful work.

## Goals of Congestion Control

![](/6250/images/fig8.5.png)

Congestion control has two main goals. The first is to use network resources efficiently. Going
back to our sink analogy, we'd like to fill the sink as quickly as possible. Fairness, on the other
hand, ensures that all the senders essentially get their fair share of resources. A final goal, of
course, is to avoid congestion collapse. Congestion collapse isn't just a theory, it's actually been
frequently observed in many different networks.

## Two Approaches to Congestion Control

![](/6250/images/fig8.6.png)

There are two basic approaches to congestion control: end-to-end congestion control and
network assisted congested control. In end-to-end congestion control the network provides no
explicit feedback to the senders about when they should slow down their rates. Instead,
congestion is inferred typically by packet loss, but potentially, also by increased delay. This is
the approach taken by TCP congestion control. In network assisted congestion control, routers
provide explicit feedback about the rates that end systems should be sending in. So they might
set a single bit indicating congestion, as is the case in TCP's ECN, or explicit congestion
notification extensions, or they might even tell the sender an explicit rate that they should be
sending at. We're going to spend the rest of the lesson talking about TCP congestion control.



## TCP Congestion Control

![](/6250/images/fig8.7.png)

In TCP congestion control, the senders continue to increase their rate until they see packet drops
in the network. Packet drops occur because the senders are sending at a rate that is faster than the
rate at which a particular router in the network might be able to drain its buffer. So you might
imagine, for example, that if all three of these senders are sending at a rate that is equal to the
rate at which the router is able to send traffic downstream, then eventually this buffer will fill up.
TCP interprets packet loss as congestion. And when senders see packet loss, they slow down as a
result of seeing the packet loss. This is an assumption. Packet drops are not a sign of congestion
in all networks. For example, in wireless networks, there may be packet loss due to corrupted
packets as a result of interference. But in many cases, packet drops do result because some router
in the network has a buffer that has filled up and can no longer hold anymore packets and hence
it drops the packets as they arrive. So senders increase rates until packets are dropped,
periodically probing the network to check whether more bandwidth has become available; then
they see packet loss, interpret that as congestion, and slow down. So, congestion control has two
parts. One is an increase algorithm, and the other is a decrease algorithm. In the increase
algorithm, the sender must test the network to determine whether the network can sustain a
higher sending rate. In the decrease algorithm, the senders react to congestion to achieve optimal
loss rates, delays in sending rates. Let's now talk about how senders can achieve these increase
and decrease algorithms.


## Two Approaches to Adjusting Rate

![](/6250/images/fig8.8.png)

One approach is a window based algorithm. In this approach, a sender can only have a certain
number of packets outstanding, or quote, in flight. And the sender uses acknowledgements from
the receiver to clock the retransmission of new data. So let's suppose that the sender's window
was four packets. At this point, there are four packets outstanding in the network. And the sender
cannot send additional packets until it has received an acknowledgement from the receiver.
When it receives an acknowledgment, or an ACK from the receiver, the sender can then send
another packet. So at this point there are still four outstanding or four unacknowledged packets in
flight. In this case if a sender wants to increase the rate at which it's sending, it simply needs to
increase the window size. So, for example, if the sender wants to send at a faster rate, it can
increase the window size from four, to five. A sender might increase its rate anytime it sees an
acknowledgement from the receiver. In TCP, every time a sender receives an acknowledgement,
it increases the window size.

![](/6250/images/fig8.9.png)

Upon a successful receipt, we want the sender to increase its window by one packet per round
trip. So, for example, in this case if the sender's window was initially four packets, then at the
end of a single round trip's worth of sending, we want the next set of transmissions to allow five
packets to be outstanding. This is called Additive Increase. If a packet is not acknowledged, the
window size is reduced by half. This is called Multiplicative Decrease. So TCP's congestion
control is called additive increase multiplicative decrease, or AIMD.

![](/6250/images/fig8.10.png)

The other approach to adjusting rates is an explicit rate-based congest control algorithm. In this
case the sender monitors the loss rate and uses a timer to modulate the transmission rate.
Window based congestion control, or AIMD, is the common way of performing congestion
control in today's computer networks. In the next lesson we will talk about the two goals of TCP
congestion control further (efficiency and fairness) and explore how TCP achieves those goals.

## Window Based Congestion Control Quiz

![](/6250/images/fig8.11.png)

Let's have a quick quiz on window based congestion control. Suppose the round trip time
between the sender and receiver is 100 ms, each packet is 1kb, and the window size is 10
packets. What is the rate at which the sender is sending? Please put your answer here in terms of
kilobits per second, keeping in mind that 1 byte is 8 bits.


## Window Based Congestion Control Solution

![](/6250/images/fig8.12.png)

The sending rate of the sender is approximately 800 kbps. With a window size of ten packets and
a round trip time of 100 milliseconds, the sender can send 100 packets per second. With each
packet being one kilobyte, or 8000 bits, that gives us 800,000 bits per second or about 800
kilobits per second.

## Fairness and Efficiency in Congestion Control

![](/6250/images/fig8.13.png)

The two goals of congestion control are fairness (meaning every sender gets their fair share of
the network resources) and efficiency (meaning that the network resources are used well). In
other words we shouldn't have a case where there are spare capacity or resources in the network,
and senders have data to send, but are not able to send it. So, we'd like the network to be used
efficiently, but we'd also like it to be shared among the senders.

![](/6250/images/fig8.14.png)


We can represent fairness and efficiency in terms of a phase plot, where each axis represents a
particular user, or particular senders' allocation. In this case we just have two users, one and two,
and we represent their allocations with X1 and X2. If the capacity of the network is C, then we
can represent the optimal operating line as X1 + X2 being some constant, C. Anything to the left
of this diagonal line represents under utilization of the network, and anything to the right of the
line represents overload. We can also represent another line, X1 = X2 as some notion of fair
allocation. So the optimal point is where the network is neither under or over utilized, and when
the allocation is fair. So being on this diagonal line represents efficiency, and being on the green
diagonal line represents fairness. We can use the phase plot to understand why senders who use
additive increase multiplicative decrease, converge to fairness. The senders also converge to the
efficient operating point. Let's suppose that we start at the operating point shown in blue. At this
point both senders will additively increase their sending rates. Additive increase results in
moving along a line that is parallel to x1 and x2, since both senders increase their rate by the
same amount. Additive increase will continue until the network becomes overloaded. At this
point the senders will see a loss and perform multiplicative decrease. In multiplicative decrease
each sender decreases its rate by some constant factor of its current sending rate. For example,
suppose each one of these senders decreases its sending rate by half. The resulting operating
point is shown by this second blue dot. Note that that new operating point, as a result of
multiplicative decrease, is on a line between the point on the efficiency line that the centers hit,
and the origin. At this point the sender's will again increase their sending rate along a line that's
parallel to X1 equals X2 until they hit over load again, at which point they will again retreat
towards the origin. You can see that eventually the senders will reach this optimal operating
point through the path that's delineated by the blue line. To think about this a bit more you can
see that every time additive increase is applied, that increases efficiency. Every time
multiplicative decrease is applied that improves fairness because every time we apply
multiplicative decrease, we get closer to this X1 equals X2 line.


## AIMD

![](/6250/images/fig8.15.png)

The result is the additive increase multiplicative decrease congestion control algorithm. The
algorithm is distributed, meaning that all the senders can act independently, and we've just
shown using the phase plots that it's both fair and efficient. To visualize this sending rate over
time, the sender's sending rate looks roughly as shown. We call this the TCP sawtooth behavior
or simply the TCP sawtooth. TCP periodically probes for available bandwidth by increasing its
rate using additive increase. When the sender reaches a saturation point by filling up a buffer in a
router somewhere along the path, it will see a packet loss, at which point it will decrease its
sending rate by half. You can thus see that a TCP sender sends at a sending rate shown by the
dotted green line that is halfway between the maximum window size at which the sender sends,
and half that rate which it backs off to when it sees a loss. You can see that between the lowest
sending rate and the highest is w_m over 2 plus 1 round trips. Now, given that rate we can
compute the number of packets between periods of packet loss and compute the loss rate from
this. The number of packets sent for every packet lost is the area of this triangle. So the lost rate
is on the order of the square of the maximum window divided by some constant. Now, the
throughput is the average rate, 3 4ths w_max divided by the RTT. Now if we want to relate the
throughput to the loss rate, where we call the loss rate p and the throughput lambda, we simply
need to solve for w_m.

![](/6250/images/fig8.16.png)

And I'm just going to get rid of the constant. So a loss occurs once for this number of packets, so
the loss rate is simply 1 over that quantity. And then when we solve for w_m and plug in for
throughput, we see that the throughput is inversely proportional to both the round trip time and
the square root of the loss rate.

### Additive Increase Quiz

![](/6250/images/fig8.17.png)

So as a quick quiz, and returning to our phase plot, does additive increase increase or decrease
fairness? And does it increase, or decrease, or reduce, efficiency? Please check all that apply

### Additive Increase Solution

![](/6250/images/fig8.18.png)

Additive increase increases efficiency because it gets us closer to that efficiency line parallel to
the X1 equals X2 line. It technically also decreases fairness because the centers are both
increasing their rates, so we're relatively further away from this X1 equals X2 line. In contrast,
remember that multiplicative decrease reduces efficiency by moving us further away from this
green dotted line, but it increases fairness by moving us closer to the blue dotted line.


## Data Centers and TCP Incast

![](/6250/images/fig8.19.png)

We'll now talk about TCP congestion control in the context of modern datacenters. And we'll
talk about a particular TCP throughput collapse problem called the TCP incast problem. A
typical data center consists of a set of server racks, each holding a large number of servers, the
switches that connect those racks of servers, and the connecting links that connect those switches
to other parts of the topology. So the network architecture is typically made up of some sort of
tree and switching elements that progressively are more specialized and expensive as we move
up the network hierarchy. Some of the characteristics of a data center network include a high fan
in. There is a very high amount of fan in between the leaves of the tree and the top of the root
workloads are high bandwidth and low latency, and many clients issue requests in parallel, each
with a relatively small amount of data per request. The other constraint that we face is that the
buffers in these switches can be quite small. So when we combine the requirements of high
bandwidth and low latency for the applications, the presence of many parallel requests coming
from these servers. and the fact that the switches have relatively small buffers, we can see that
potentially there will be a problem. The throughput collapse that results from this phenomenon is
called the TCP Incast problem. Incast is a drastic reduction in application throughput that results
when servers using TCP all simultaneously request data, leading to a gross underutilization of
network capacity in many-to-one communication networks like a datacenter. The filling up of the
buffers here at the switches result in bursty retransmissions that overfill the switch buffers. And
these bursting retransmissions are cause by TCP timeouts. The TCP timeouts can last hundreds
of milliseconds. But the roundtrip time in a data center network is typically less than a
millisecond. Often just hundreds of microseconds. Because the roundtrip times are so much less
than TCP timeouts, the centers will have to wait for the TCP timeout before they retransmit an
application. Throughput can be reduced by as much as 90% as a result of link idle time.


## Barrier Synchronization and Idle Time

![](/6250/images/fig8.20.png)

A common request pattern in data centers today is something called barrier synchronization
whereby a client or an application might have many parallel threads, and no forward progress
can be made until all the responses for those threads are satisfied. For example, a client might
send a synchronized read with four parallel requests. But, suppose that the fourth is dropped. At
this point we have a request sent at time zero, then we see a response less than a millisecond
later, and at this point, threads one to three complete but TCP may time out on the fourth. In this
case, the link is idle for a very long time while that fourth connection is timed out. The addition
of more servers in the network induces an overflow of the switch buffer, causing severe packet
loss, and inducing throughput collapse. One solution to this problem is to use fine grained TCP
retransmission timers, on the order of microseconds, rather than on the order of milliseconds.
Reducing the retransmission timeout for TCP thus improves system throughput. Another way to
reduce the network load is to have the client acknowledge every other packet rather than every
packet, thus reducing the overall network load. The basic idea here, and the premise, is that the
timers need to operate on a granularity that's close to the round-trip time of the network. In the
case of a data center that's hundreds of microseconds or less.


## TCP Incast Quiz

![](/6250/images/fig8.21.png)


As a quick review what are some solutions to the TCP incast problem? Having senders send
smaller packets? Using finer granularity TCP timeout timers. Having the clients send fewer
ackowledgements or having fewer TCP senders?

## TCP Incast Solution

![](/6250/images/fig8.22.png)


Using finer granularity timers and having the clients acknowledge only every other packet, as
oppose to every packet, are possible solutions to the TCP Incast problem.

## Multimedia and Streaming

![](/6250/images/fig8.23.png)

In this lesson, we'll talk about multimedia and streaming. We'll talk about digital audio and video
data, multimedia applications (in particular, streaming audio and video for playback), multimedia
transfers over a best-effort network (in particular, how to tolerate packet loss delay and jitter),
and quality of service. So we use multimedia and streaming video very frequently on today's
internet. YouTube streaming videos are an example of multimedia streaming as are applications
for video or voice chat such as Skype or Google Hangout. In this lecture we'll talk about the
challenges for streaming these types of applications over best effort networks as well has how to
solve those challenges. We'll also talk about the basics of digital audio and video data. First of
all, let's talk about the challenges for media streaming.


## Challenges

![](/6250/images/fig8.24.png)

One challenge is that, there's a large volume of data. Each sample is a sound or an image and
there are many samples per second. Sometimes because of the way data is compressed, the
volume of data that's being sent may vary over time. In particular, the data may not be set at a
constant rate. But in streaming, we want smooth playout, so the variable volume of data can pose
challenges. Users typically have a very low tolerance for delay variation. Once playout of a
video starts for example, you want that video to keep playing. It's very annoying if once you've
started playing, that the video stops. The users might have a low tolerance for delay period, so in
cases like games or Voice over IP, delay is typically just unacceptable, although users can
tolerate some loss. Before we get into how the network solves these challenges. Let's talk a little
bit about digitizing audio and video.

## Digitizing Audio and Video

![](/6250/images/fig8.25.png)

Suppose we have an analog audio signal that we'd like to digitize, or send as a stream of bits.
What we can do is sample the audio signal at fixed intervals, and then represent the amplitude of
each sample with a fixed number of bits. For example, if our dynamic range was from 0 to 15,
we could quantize the amplitude of this signal such that each sample could be represented with
four bits.

## Digitizing Audio and Video Quiz 1

![](/6250/images/fig8.26.png)

Let's take a couple of examples. So with speech you might take 8000 samples per second, and
you might have 8 bits per sample. So what is the sampling rate in this case? Please give your
answer in kilobits per second.

## Digitizing Audio and Video Solution 1

![](/6250/images/fig8.27.png)

At 8,000 samples per second, and eight bits for every sample, the rate of digitized speech would
be 64 kbps, which is a common bit rate for audio.


## Digitizing Audio and Video Quiz 2

![](/6250/images/fig8.28.png)

Suppose we have a MP3 with 10,000 samples per second and 16 bits per sample. What's the
resulting rate in this case in kbps?


## Digitizing Audio and Video Solution 2

![](/6250/images/fig8.29.png)

The resulting rate in this case is, a 160 kbps.



## Video Compression

![](/6250/images/fig8.30.png)


Video compression works in slightly different ways. Each video is a sequence of images and
each image can be compressed with spatial redundancy, exploiting aspects that humans tend not
to notice. Also there is temporal redundancy. Between any two video images, or frames, there
might be very little difference. So if this person was walking towards a tree, you might see a
version of the image that's almost the same, except with the person shifted slightly to the right.
Video compression uses a combination of static image compression on what are called reference
frames, or anchor frames (sometimes called I frames), and derived frames, sometimes called P
frames. The P frame can be represented as the I frame, compressed.

![](/6250/images/fig8.31.png)


If we take the I frame and divide it into blocks, we can then see that the P frame is almost the
same except for a few blocks here that can be represented in terms of the original I frame blocks,
plus a few motion vectors.


![](/6250/images/fig8.32.png)


A common video compression format that's used on the internet is called MPEG.


## Streaming Video

![](/6250/images/fig8.33.png)


In a streaming video system where the server streams stored audio and video, the server stores
the audio or video files, the client requests the files, and plays them as they download. It's
important to play the data at the right time. The server can divide the data into segments and then
label each segment with a time stamp indicating the time at which that particular segment should
be played, so the client knows when to play that data. The data must arrive at the client quickly
enough, otherwise the client can't keep playing. The solution is to have a client use what's called
a playout buffer, where the client stores data as it arrives from the server, and plays the data for
the user in a continuous fashion. Thus, data might arrive more slowly or more quickly from the
server, but as long as the client is playing data out of the buffer at a continuous rate, the user sees
a smooth playout. A client may typically wait a few seconds before it starts playing the stream to
allow data to be built up in this buffer to account for cases when the server might have times
where it is not sending at a rate that's sufficient to satisfy the client's playout rate.


## Playout Delay

![](/6250/images/fig8.34.png)

Looking at this graphically, we might see packets generated at a particular rate and the packets
might be received at slightly different times, depending on network delay. These types of delays
are the types that we want to avoid when we playout. So if we wait to receive several packets
and fill the buffer before we start playing, say to here, then we can have a playout schedule that
is smooth regardless of the erratic arrival times that may result from network delays. So this
playout delay or buffering allows the client to achieve a smooth playout. Some delay at the
beginning of the playout is acceptable. Startup delays of a few seconds are things that users can
typically tolerate, but clients cannot tolerate high variation in packet arrivals if the buffer starves
or if there aren't enough packets in the buffer. Similarly, small amount of loss or missing data
does not disrupt the playback, but retransmitting a lost packet might actually take too long and
result in delays or starvation of the playout buffer.

## Streaming Quiz

![](/6250/images/fig8.35.png)


So as a quick review, which of these pathologies can streaming audio and video tolerate? Packet
loss, delay, variation in delay, or jitter?

## Streaming Solution


![](/6250/images/fig8.36.png)

Some delay at the beginning of a packet stream is acceptable. And similarly, some small amount
of missing data is okay. We can tolerate small amounts of missing data that result in slightly
reduced quality of the audio or video stream. On the other hand, a receiver is not very good at
tolerating variability in packet delay in the packet stream, particularly if the client buffer is
starved.

## TCP is Not a Good Fit

![](/6250/images/fig8.37.png)


It turns out that TCP is not a good fit for congestion control for streaming video, or streaming
audio. TCP retransmits lost packets, but retransmissions may not always be useful. TCP also
slows down its sending rate after packet loss, which may cause starvation of the client. There's
also a fair amount of overhead in the protocol. A TCP header of 20 bytes for every packet is
large for audio samples and sending acknowledgements for every other packet may be more
feedback than is needed. Instead, one might consider using UDP. UDP does not retransmit lost
packets and it does not automatically adapt the sending rate. It also has a smaller header. Because
UDP does not automatically retransmit packets or adjust the sending rate, many things are left to
higher layers, potentially the application, such as when to transmit the data, how to encapsulate
it, whether to retransmit, and whether to adapt the sending rate, or to adapt the quality of the
video or audio encoding. So higher layers must solve these problems. In particular, the sending
rate still needs to be friendly or fair to other TCP senders, which may be sharing the link. There
are a variety of video streaming and audio streaming transport protocols that are built on top of
UDP that allows senders to figure out when and how to retransmit lost packets and how to adjust
sending rates.


## (More) Streaming

![](/6250/images/fig8.38.png)

We're going to talk a little bit more about streaming and in particular, specific applications and
how they work. We'll talk about a streaming video application, YouTube, and a Voice over IP
(or streaming audio) application, Skype. With YouTube, all uploaded videos are converted to
Flash or html5 and nearly every browser has a Flash plug-in. Thus, every browser can essentially
play these videos. HTTP and TCP are implemented in every browser and the streams easily get
through most firewalls. So, in this case, even though we've talked about why TCP is suboptimal
for streaming, the designers of YouTube decided to keep things simple, potentially at the
expense of video quality. When a client makes an HTTP request to youtube.com, it is redirected
to a content distribution network server located in a content distribution network, such as
Limelight or perhaps even YouTube's own content distribution network. We will talk about
content distribution networks later on in this course. When the client sends an HTTP get message
to this CDN server, the server responds with a video stream. Similarly with Skype, or Voice
Over IP, your analog signal is digitized through an A to D conversion and this resulting digitized
bit stream is sent over the internet. In the case of Skype, this A to D conversion happens by way
of the application. And in the case of Voice over IP, this conversion might be performed with
some kind of phone adapter that you actually plug your phone into. An example of that might be
Vonage. In VoIP with an analogue phone, the adapter converts between the analogue and digital
signal. It sends and receives data packets and then communicates with the phone in a standard
way. Skype, on the other hand, is based on what's known as peer-to-peer technology where
individual users using Skype actually route voice traffic through one another. We will talk more
about peer-to-peer content distribution later in this course.


## Skype

![](/6250/images/fig8.39.png)

So Skype has a central log-in server but then uses pier to pier to exchange the actual voice
streams, and compresses the audio to achieve a fairly low bit rate. At around 67 bytes per packet
and 140 packets per second, we see Skype uses about 40 kilobites per second in each direction.
Data packets are also encrypted in both directions. Good audio compression and avoiding hops
through far away hosts can improve the quality of the audio, as can forward error correction.
Ultimately, though, the network is a major factor. Long propagation delays, high congestion, or
disruptions as a result of routing changes can all degrade the quality of a voice over IP call. To
ensure that some streams achieve acceptable performance levels, we sometimes use what's called
Quality of Service. One way of doing Quality of Service is through explicit reservations. But,
another way is to simply mark certain packet streams as higher priorities than others. Let's first
take a look at marking and policing of traffic sending rates.



## Marking (and Policing)

![](/6250/images/fig8.40.png)

So we know from before that applications compete for bandwidth. Consider a Voice over IP
application and a file transfer application that want to share a common network link. In this case,
we'd like the audio packets to receive priority over the file transfer packets since the user's
experience can be significantly degraded by lost or delayed audio packets. In this case, we want
to mark the audio packets as they arrive at the router so that they receive a higher priority than
the file transfer packets. You might imagine implementing this with priority queues where the
VOIP packets were put in one queue and the file transfer packets are put in a separate queue that
is served less often than a high priority queue. An alternative is to allocate fixed bandwidth per
application, but the problem with this alternative is that it can result in inefficiency if one of the
flows doesn't fully utilize its fixed allocation. So the idea, in addition to marking and policing, is
to apply scheduling. One way of applying scheduling is to use what's called weighted fair
queuing, whereby the queue with the green packets is served more often than the queue with the
red packets. Another alternative is to use admission control, whereby an application declares its
needs in advance, and the network may block the application's traffic if the application can't
satisfy the needs. A busy signal on a telephone network is an example of admission control. But
can you imagine how annoying it would be if you attempted to load a web page and were
blocked. This blocking, or the user experience that results from it, is a negative consequence of
admission control and is one of the reasons it's not commonly applied for internet applications.


### QoS Quiz

![](/6250/images/fig8.41.png)

So as a quick quiz what are some commonly used Quality of Service techniques for streaming
audio and video? Marking packets and putting them into queues with different priorities,
scheduling the service of these queues at different rates depending on the priority of the
application, blocking flows with admission control if the network can't satisfy their rates, or
performing fixed bandwidth allocations? Please check all that apply.


### Qos Solution

![](/6250/images/fig8.42.png)

A common way of applying QoS for streaming applications is to mark the packets at a higher
priority, put those packets into a higher priority queue, and then schedule that queue so that it's
serviced more regularly, or more frequently, than traffic or applications that are lower priority.



## Parking Lot Problem

![](/6250/images/fig8.43.png)


Recall that congestion collapse occurs when packets consume valuable network resources only to
get dropped later at a downstream link. And although the network is forwarding packets, none of
them actually reach the destination and no useful communication occurs, resulting in what we
called congestion collapse. We talked about congestion collapse in an earlier lesson in this
course.

![](/6250/images/fig8.44.png)


Recall that the goal of TCP is to prevent congestion collapse. In this assignment, you will
become familiar with the Mininet environment, creating custom network topologies, running
programs in virtual hosts that generate TCP traffic, and learn about TCP congestion control, an
the TCP sawtooth. You'll also see how bandwidth is shared across multiple flows.


![](/6250/images/fig8.45.png)


We are going to explore these concepts in a particular topology that we'll just call a parking lot.
So in the assignment, you'll create the topology below with the following parameters. N will be
the number of receivers connected via this switch like topology, the data rate of each link will be
100 megabits per second, and the delay of each link will be one millisecond. Now you're going
to use iperf to generate simultaneous TCP flows from each of the senders to the lone receiver.
Then you're going to plot the time series of throughput versus time for each of these senders for
each of your experiments as you vary the value of N. Your plot should run for 60 seconds. We
have given an initial file called parkinglot.py. Your goal is to fill in the gaps. You'll need to both
fill in the part that sets up the topology using a parameter of N, and then you'll need to fill in the
part that actually generates the TCP flows between the senders and the receiver using iperf, and
monitor the throughput of each of these flows.



