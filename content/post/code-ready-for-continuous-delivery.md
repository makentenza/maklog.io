---
title: "Is your code ready for Continuous Delivery?"
date: 2018-01-09
tags: ["techmaturity", "openshift", "CD", "Continuous Delivery", "Cloud Native"]
draft: false
---
Containers have helped revolutionize software development. Microservices and cloud-native applications are now the trendings on the Software industry. With containers, developers can quickly test their code in a production-like environment, which helps to speed up the development process.
PaaS software like Kubernetes and OpenShift have quickly become really popular and the key to accelerating Continuous Delivery approach for software engineering. On this post, we are going to review another tool we usually use with our Customers on [Red Hat Open Innovation Labs](https://www.redhat.com/en/open-innovation-labs). This tool is called _Tech Maturity_, by Ticketmaster, and can be used to measure the _cloud readiness_ for a product, but also give us very good indicators and tips about the code be ready for Continuous Delivery, which is the ultimate goal.

The application can be easily deployed from [Ticketmaster's GitHub repository](https://github.com/Ticketmaster/techmaturity) or use an existing image from [DockerHub](https://hub.docker.com/r/ticketmaster/techmaturity/). I have also created a PR to add OpenShift/Kubernetes support to the product as well data persistence. Check templates and instructions from [my forked repository](https://github.com/makentenza/techmaturity/tree/openshift-enabled/openshift)


The application is really easy to use. The only thing you need to do is to create a new Asset for your application (or application component) and go through the survey answering questions on 5 different sections: Code, Build and Test, Release, Operate, Optimize.

![survey](/post/img/techmaturity01.png "Tech Maturity survey")

![survey](/post/img/techmaturity02.png "Tech Maturity survey")

Once you have completed the survey, you will get a score. This is the **_Native Cloud Readiness Score_** for your product. The detailed score will give you a better visibility around the areas you need to improve your product. As you will see during the survey, many questions are directly related to the Continuous Delivery process. Pay special attention to these questions as they will give you an idea of best practices for this.

![score](/post/img/techmaturity03.png "Tech Maturity score")

![score](/post/img/techmaturity04.png "Tech Maturity score")

Check on the following video how the application can be used.

<div align="center"><a href="http://www.youtube.com/watch?feature=player_embedded&v=LLAg_LxuBzM
" target="_blank"><img src="http://img.youtube.com/vi/LLAg_LxuBzM/0.jpg"
alt="Tech Maturity Portal Overview" width="240" height="180" border="10" /></a></div>
