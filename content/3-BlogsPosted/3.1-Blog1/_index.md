---
title: "Blog 1"
date: 2026-11-21
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

**### What Happens If 10,000 People Suddenly Visit Your Website?**

Let's say I have just deployed a website.

At first, everything is pretty peaceful.

There are only **10 people accessing the website at the same time**.

The website responds quickly.

The server is running at around 20–30% CPU usage.

Everything seems fine.

Then one day...

A post on social media suddenly goes viral.

From 10 people...

**10 → 100 → 1,000 → 10,000 people**

all accessing the website at the same time.

And at this point, I start asking a rather scary question:

> **Can my server handle this?**

---

**### What If I Only Have One EC2 Instance?**

Suppose my website is running on a single EC2 instance.

When there are only a few users, everything works normally.

But as the number of requests increases, CPU and memory usage also start to rise.

For example:

**10 users → CPU 20%**

**100 users → CPU 45%**

**1,000 users → CPU 85%**

**10,000 users → 💥 ?**

At this point, I have two choices.

**The first:** get a more powerful server.

**The second:** use multiple servers to handle the traffic together.

And this is where I start encountering two very important concepts in Cloud.

---

**### Scale Up and Scale Out**

If I change:

**Small EC2 → Larger EC2**

this is called **Scale Up**.

For example, I can move from an instance with limited CPU and RAM to a more powerful instance.

This approach is quite simple.

But it has one problem:

> **A server, no matter how powerful, still has limits.**

What if the traffic keeps increasing?

I have to upgrade it again.

And during the upgrade process, I may also have to deal with downtime.

---

Another approach is:

**EC2 #1 + EC2 #2 + EC2 #3**

Instead of building **one very powerful server**, I use **multiple servers working together**.

This is called **Scale Out**.

But this immediately creates a new problem:

> **Which EC2 instance should the user be sent to?**

---

**### I Have 3 EC2 Instances, But Who Distributes the Traffic?**

Suppose I have:

**EC2 #1 — EC2 #2 — EC2 #3**

If 3,000 requests arrive at the same time, I don't want all of them to go to EC2 #1.

I need a component in front of the servers to distribute the traffic.

That's where a **Load Balancer** comes in.

The flow can be visualized simply as:

**Users → Load Balancer → EC2 #1 / EC2 #2 / EC2 #3**

The Load Balancer distributes requests across the backend servers instead of letting one server handle all the traffic.

Now I have solved one problem:

> **A single server no longer has to handle all the traffic.**

But there is still another question.

---

**### So Why Not Just Create 100 EC2 Instances?**

If my website only has 10 users today:

**10 Users → 100 EC2 Instances**

then most of the servers would barely be doing anything.

But I would still have to pay for those resources.

So I need something smarter:

> **When traffic increases, add more servers.**
>
> **When traffic decreases, remove servers.**

This is where **Auto Scaling** comes in.

For example, I might start with:

**2 EC2 Instances**

Traffic increases:

**2 EC2 → 4 EC2 → 6 EC2 → 8 EC2**

When traffic decreases:

**8 EC2 → 6 EC2 → 4 EC2 → 2 EC2**

I don't need to sit in the AWS Console and manually create and delete EC2 instances.

The system can automatically adjust the number of instances based on conditions that I configure.

This is one of the things I find most interesting about learning Cloud:

> **It's not just about having “more servers.”**
>
> It's about having a system that can **automatically change the number of servers based on demand.**

---

**### But What If One EC2 Instance Goes Down?**

This is something I didn't think about at first.

Suppose I have:

**Load Balancer → EC2 #1 / EC2 #2 / EC2 #3**

But EC2 #2 suddenly encounters an error.

If the Load Balancer keeps sending requests to EC2 #2, users may receive errors.

So the system needs to know:

> **Which instances are still healthy?**

The Load Balancer can use **Health Checks** to monitor the health of the instances.

If EC2 #2 is no longer healthy, traffic can be redirected to other healthy instances.

Then Auto Scaling can create a new instance to maintain the desired capacity.

The flow can now be understood simply as:

**EC2 #2 Unhealthy → Health Check detects it → Traffic moves to healthy instances → Auto Scaling creates a replacement instance**

At this point, I start realizing:

> **Having multiple servers is not enough. The system also needs to know which servers are working properly.**

---

**### But How Does the System Know When to Scale?**

This leads to another question.

> **When traffic increases, when should the system create more EC2 instances?**

For example, I may want to monitor:

- CPU Utilization
- Network traffic
- Request count
- Error rate
- Response time

If CPU utilization keeps increasing:

**20% → 30% → 45% → 60% → 75% → 85%**

I can configure a scaling policy so that the system reacts when appropriate conditions are reached.

AWS provides **Amazon CloudWatch** to collect metrics and support monitoring.

At this point, I realize:

> **Auto Scaling doesn't magically know when to create EC2 instances.**

It needs data to observe the condition of the system.

Monitoring and scaling therefore have a close relationship.

---

**### But There's Another Problem...**

Suppose my website has users in:

🇻🇳 Vietnam  
🇯🇵 Japan  
🇺🇸 United States  
🇬🇧 United Kingdom

All of them need to send requests to the backend.

If the website contains a lot of static content such as:

- images
- CSS
- JavaScript
- videos
- files

does every request really need to go directly to the server?

**Not necessarily.**

This is where a **CDN** becomes useful.

With AWS, **Amazon CloudFront** can help distribute content through edge locations closer to users.

Content that can be cached does not necessarily need to go back to the origin every time.

And once again, I realize:

> A service doesn't appear simply because “AWS has this service, so let's add it.”
>
> It appears because the system is facing a specific problem.

**User is far from the server → Latency**

→ A CDN can help address this problem.

---

**### What About Security?**

At this point, my system looks something like:

**User → CloudFront → Load Balancer → EC2**

But what if anyone can send millions of malicious requests to the application?

The system can still run into problems even with Auto Scaling.

So:

> **Scalability does not mean Security.**

Additional layers of protection may be needed, such as:

- Security Groups
- Network controls
- AWS WAF
- IAM

This is something I find quite important:

> **Being able to scale does not mean being secure.**
>
> **A powerful server does not mean the application is protected.**

Performance, scalability, and security are different problems.

---

**### So Why Did the Architecture Become So Complex When I Started With Just One EC2?**

At first:

**User → EC2**

Then I started asking a series of questions:

> **What if traffic increases?**

→ Scale Up / Scale Out

> **If I have multiple EC2 instances, who distributes the traffic?**

→ Load Balancer

> **What if traffic keeps increasing and decreasing?**

→ Auto Scaling

> **What if one EC2 instance goes down?**

→ Health Check

> **How do I know if the system is having problems?**

→ CloudWatch

> **What if users are very far from the server?**

→ CloudFront

> **What if the traffic is malicious?**

→ Security Layer / WAF

From a simple single-server setup, the architecture gradually becomes a system capable of:

**Handling traffic → Distributing traffic → Scaling automatically → Detecting failures → Monitoring → Improving performance → Increasing security**

---

**### What I Learned**

The most interesting thing I learned from exploring this problem is not:

> “AWS has Load Balancer.”

or:

> “AWS has Auto Scaling.”

Instead, it's the way I started thinking about a system.

At first, I only had one question:

> **“What if too many people visit my website?”**

Then that question led to more questions:

**What if one server can't handle the load?**

→ Scale Out.

**If there are multiple servers, where should requests go?**

→ Load Balancer.

**What if traffic keeps increasing and decreasing?**

→ Auto Scaling.

**What if one server goes down?**

→ Health Check.

**How do I know if the system is having problems?**

→ Monitoring.

**What if users are far away from the server?**

→ CDN.

**What if the traffic is malicious?**

→ Security Layer.

And I realized something:

> **Cloud Architecture doesn't start by choosing AWS Services.**
>
> It starts by asking:
>
> **“What will happen to my system if this occurs?”**

When I start thinking this way, names like **EC2, Load Balancer, Auto Scaling, CloudWatch, and CloudFront** are no longer just services I have to memorize.

They become **tools for solving specific problems**.

Perhaps this is also one of the things I like most about learning Cloud:

> **It's not about building a huge system.**
>
> It's about understanding why the system needs each component, and what happens when one of them is no longer able to meet the requirements.

#AWS #CloudComputing #AWSArchitecture #EC2 #AutoScaling #LoadBalancer #CloudWatch #CloudFront #CloudLearning #AWSBeginner